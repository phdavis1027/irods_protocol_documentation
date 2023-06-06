iRODS messages have five parts. These five parts are sent as separate transport packets. In order of transmission, they are:
- Prelude: Required. 4 bytes indicating how long in bytes the header will be.
- Header: Required. An XML element with the tag <MsgHeader_PI>. Here is an example: ```
<MsgHeader_PI>
	<type>RODS_CONNECT</type>
	<msgLen>338</msgLen>
	<errorLen>0</errorLen>
	<bsLen>0</bsLen>
	<intInfo>0</intInfo>
</MsgHeader_PI> ```
All headers will contain the same fields. Here is a description of each field:
	- type: The type of the message. Note that it is note quite correct to say that this describes the type of the message body, since all messages have headers with a type, but not all messages have a body.
	- msgLen: The length in bytes of the message body.
	- errorLen: The length of an optional buffer for error messages. If transmitted, this buffer is sent as a separate packet.
	- bsLen: Stands for "byte stream length" (I think). The length of an optional message for additional byte-encoded content. If transmitted, this buffer is sent as a separate packet.
	- intInfo: Zero when type is not either RODS_API_REQ or RODS_API_REPLY (I think). Otherwise, indicates the apiReqNum. 
- Body/Message: Optional. An XML element which provides extra information about the operation described by the header. What XML element can appear here is constrained by the `type` field of the header. For example:
	-  The iRODS server enforces that if a header's `type` is `RODS_CONNECT`, that message's body must be of type `StartupPack_PI`. 
	-  If a client sends a header with type `RODS_API_REQ` and `intInfo` set to `633` (for `OBJ_STAT_AN`), it must be followed up by a body with type `DataObjInp_PI` providing enough information to locate the object whose stat is being requested. 
 - BsBuf: Optional, from bsLen. An unformatted stream of bytes transmitted after certain API operations, e.g., for file transmission.
 - ErrorBuf: Optional, from errorLen. An additional buffer for error information transmitted a fter bsBuf (find out in which order this happens).