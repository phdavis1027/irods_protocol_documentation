- Run a handshake between incompatible versions of iRODS to see what happens.
- Run connection init using a different client auth scheme.
- Write up some formal, spec-style definitions of seemingly obvious terms like "field" and "server/client"
	- "party" is defined to mean "either client or server"
- Address what in the world is formally meant by "XML-like," since for one thing we don't ever include schema tags or anything like that. 
	- For one thing, it seems pretty clear that at least conventionally the iRODS protocol wants XML elements to appear in the order in which they're defined in their corresponding C structs. This is not a common feature of XML, but there is a standardized field for XML Schema Definition documents which signals to parsers that child elements should appear in a certain order, namely [x:sequence](https://www.w3schools.com/xml/el_sequence.asp). This gives us some really nifty language with which to describe the relation of iRODS protocol to XML.
- Once I get my development environment more friendly toward 4.3.0, directly inspect the R_SPECIFIC_QUERY table in iCAT to document what values are valid for the `sql` field in [SpecificQueryInp_PI](xml\ elements/specificQueryInp_PI)
