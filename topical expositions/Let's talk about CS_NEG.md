The connection process described in  [[init logic|init logic]]  assumes that no client-server negotiation happens. Here is my attempt to figure out the exact circumstances under which client-sever negotiation happens and where it chronologically fits into the handshake process.

It seems pretty clear from the PRC source code that the client will only request negotiation if its environment file (or whatever proxy the client source code has for that information) has set `client_server_negotiation` to "request_server_negotiation." There is another environment setting called `client_server_policy` which, by the PRC source code, takes either "CS_NEG_REQUIRE" or "CS_NEG_REFUSE," and these correspond to whether the client insists on using SSL. So there's part of the mystery. Now to figure out how this happens at a protocol level.  

Let's just take the case where client wants to request negotiation. Then, in the initial `StartupPack` it will set `option` to "request_server_negotiation." 

In the case that the client does not request negotiation, and the server's client_server_negotiation is not set to "request_server_negotiation,." but the server's policy is also CS_NEG_REQUIRE, it will not send back a request to perform an SSL negotiation and will instead reply with an error message.

