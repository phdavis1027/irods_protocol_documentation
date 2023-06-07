This element is exchanged during client-server SSL negotiation. That is, StartupPacks can request that the client and the server negotiate over whether or not to use SSL/TLS, and this element is used to convey that negotiation. 

- status: either 0 or 1. Indicates whether the sender successfully initiated client-server negotiation. Appears that servers, if they send a CsNeg_PI, will always send with status 1.
- results: indicates SSL/TLS preference. Possible values are:
	- CS_NEG_REFUSE: Will not use SSL/TLS. 
	- CS_NEG_REQUIRE: Must use SSL/TLS.
	- CS_NEG_DONT_CARE: Will follow whichever party.

If both client and server send CS_NEG_DONT_CARE, the connection will default to not using SSL/TLS. If only party sends  CS_NEG_DONT_CARE, the connection will use either SSL/TLS or vanilla TCP depending on whether the other party sent CS_NEG_REQUIRE or CS_NEG_REFUSE.

In 4.0.X, the result keyword was not a KeyValPair string. 

I might have found the vestigial remains of some partial spec? the comments in `server/core/src/irods_sever_negotiation.cpp` describe the first message sent by the server during SSL/TLS negotiation as `CS_NEG_SVR_1_MSG`, which kind of implies the existence of a standardized naming scheme for the messages passed through in the process of the negotiation