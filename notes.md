- 6/5/23: I'm getting the sneaking suspicion that the binary format of different PIs might just be the internal, binary representation of the corresponding structs in C.
- 6/5/23: I would really like to figure out what the "_AN" suffix stands for in, e.g., RODS_OBJ_AN
- 6/5/23: It definitely looks like the client stats all the directories and whatnot before it does an `iput`, and I have two theories for why this might be necessary, both related to concurrency issues:
	- Maybe this is to check that some stateful information being maintained by the client agrees with that some information in the server
	- It could also be that calling these APIs triggers some kind of update operations in the server that are needed to make the `iput` make sense.
 - 6/6/23: talked with Kory yesterday about the conceptual distinction between protocol and API. One feature we might care about it in terms of this project is whether changes to the API would also force changes to the protocol documentation. Ideally, these concerns would be as separate as possible. However, it seems like a lot of the iRODS protocol (at least as concerns file transfer and the state management surrounding it) seems pretty tightly coupled with API requests. For example, client and server only know to expect certain transport packets to be raw bytes representing files because of a certain chain of RODS_API_REQ messages. One solution as we try to work toward a cleaner spec might be to define the "coupled region" where the API cannot change because it is actually a part of the protocol specification, and a "decoupled region," consisting of API calls that don't break the protocol interface. Plugins? This kind of makes sense because the iRODS protocol is just inherently more application-specific than something like HTTP or even AMQP, but it seems kind of awkward to explain to people who want a clear explanation of just what the heck our networking protocol is. 
 - 6/7/23: The question I have spent way too much time trying to answer today is: where in the world is the server sending these sids? Then, Alan told me that sid now means zone_key. Then, I looked a little closer at the ngrep output and realized that Ih was being stupid: *the client sends the key.* But okay, the client code that signs and sends the zone key has all these comments about how pure client should not actually be sending or signing zone keys. So my new question is: *why is this happening?* 
 - 6/7/23: Okay, I've given up on answering that question for now. I will now just look at the zone_key and signing process on the assumption that the non-exceptional codepath is taken. 
 - 6/7/23: cs_neg_t has another very weird implication from the perspective of a standardized protocol. That is, when the client sends it, the `result` field is just a plain old string, but when the server sends it back, the `result` field is formatted a list of keyVal pairs. Moreover, it's extremely odd that these kvps would be sent in the form of a plain old string when the protocol already has a series of xml elements dedicated to encoding kvps. 
 - 6/20/23: Thinking about how to create these graphs, some things I care about are:
	 - Who sent the message: server or client?
	 - 


Writing this simple python client to solidify understanding of iRODS protocol. Here's what's up:
- I accidentally sent a RODS_VERSION instead of a RODS_CONNECT header, and the server responded with error code -6000 SYS_HEADER_TYPE_LEN_ERR
- When that was corrected I got -15000 SYS_PACK_INSTRUCT_FORMAT_ERR
- Wouldn't you know it, the protocol cares whatever order the XML elements are in. For example, if you send a BinBytesBuf_PI  with the `buflen` and `buf` elements in the wrong order (with `buf` first), the server just won't respond at all.
- Weird note: the protocol doesn't seem to care about white space in most circumstances, BUT if the message body starts with a \n, then in fact you will get SYS_PACK_INSTRUCT_FORMAT_ERR. 

- Here's another investigative question: What is the difference in syntax between parens and square brackets in the pack instruction syntax:
	- Original hypothesis was that parens indicate raw pointers and brackets indicate c-style fixed-size arrays. Falsified by "MsParamArray_PI," which is defined as follows:
 
	```
	#define MsParamArray_PI "int paramLen; int oprType; struct *MsParam_PI[paramLen];"
	```
	
	    but it corresponds to msParamArray_t, which is: 
	 
	    ```
	    typedef struct MsParamArray {
	    int len;
	    int oprType;
	    msParam_t **msParam;
		} msParamArray_t;
	```