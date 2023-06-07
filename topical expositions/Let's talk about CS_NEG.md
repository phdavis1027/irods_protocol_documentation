The connection process described in  [[init logic|init logic]]  assumes that no client-server negotiation happens. Here is my attempt to figure out the exact circumstances under which client-sever negotiation happens and where it chronologically fits into the handshake process.

It seems pretty clear from the PRC source code that the client will only request negotiation if its environment file (or whatever proxy the client source code has for that information) has set `client_server_negotiation` to "request_server_negotiation." There is another environment setting called `client_server_policy` which, by the PRC source code, takes either "CS_NEG_REQUIRE" or "CS_NEG_REFUSE," and these correspond to whether the client insists on using SSL. So there's part of the mystery. Now to figure out how this happens at a protocol level.  

Let's just take the case where client wants to request negotiation. What exactly is being negotiated? Not any TLS/SSL-specific content, just whether the servers will communicate using TLS/SSL or not. Then, in the initial `StartupPack` it will set `option` to "request_server_negotiation." Update: this is actually wrong. In icommands at least, it will concatenate the string representation of the icommand being sent with "reqest_server_negotiation." For example, the `option` field of an `ils` would be "ilsrequest_server_negotiation." 

The server would then respond with a header of type `RODS_CS_NEG_T` and fields other than `msgLen` set to 0. The message body is an XML element of type `CS_NEG_PI`. It reports the status of the negotiation, which for the server will always be success. It also reports the results of the negotiation, which for this first message will contain the server's SSL/TLS policy. 

The client then sends another `RODS_CS_NEG_T` header with a `CS_NEG_PI` This time, we learn that the string contained in `result` is actually a semi-colon-separated list of key-value pairs (KEY_1=VALUE_1;KEY_2=VALUE_2;...).  This particular string returns two kvps, and here is an example of some that I found:

```xml
<CS_NEG_PI>
   <status>1</status>
<result>cs_neg_sid_kw=f919840ac119b2ffeb091ef10455ce92;cs_neg_result_kw=CS_NEG_USE_SSL;</result>
</CS_NEG_PI>

```

	It appears that `cs_neg_sid_kw` is completely vestigial; PRC and iCommands don't use it at all and go-irodsclient has a comment describing it as 'unknown.' On the other hand, the client will check whether the `cs_neg_result_kw` is compatible with their SSL/TLS preference; if so, the client 

(Sidenote: How, on an implementation level, is it determined whether the client and server respectively will negotiation and what their policy is once negotiation has been started? I need to know this in order to run experiments.
Well, on the client side, things seem pretty straightforward. `irods_environment.json` can define those two environment variables mentioned in the first paragraph. If either one is unset, the client will not request negotiation. If the `client_server_negotiation` field is not set to "request_server_negotiation", the client will not request negotiation. Otherwise, a negotiation request is sent.
On the server side, things seem slightly more complicated. The first thing the server checks for is whether an environment variable called `rods_cs_neg` is set. Then, it determines its cs_neg policy by checking the results of executing `acPreConnect`. )

In the case that the client does not request negotiation, and the server's client_server_negotiation is not set to "request_server_negotiation,." but the server's policy is also CS_NEG_REQUIRE, it will not send back a request to perform an SSL negotiation and will instead reply with an error message.



