Note that the corresponding header will have intInfo 0. The fact that the last message sent by the client had a [GenQueryInp_PI](GenQueryInp_PI) means it is expecting a GenQueryOut_PI in the server's reply.

- rowCnt: the number of rows returned by the genQuery (remains to be determined whether that means rows returned in this element, or rows return by the overall genQuery)
- continueInx: determined by the value of continueInx in the preceding [GenQueryInp_PI](GenQueryInp_PI). 
- attriCnt: appears to be the number of columns returned by the query (reason for thinking this comes from ~general_query.cpp:2476).
- totalRowCount: see [here](GenQueryInp_PI#^98fcd9)

These fields will be followed by 50 instances of [SqlResult_PI](SqlResult_PI), which is the maximum number of rows that can be return in a single output structure. If there are fewer total rows than 50, then the remaining SqlResults will have attrInx and reslen set to 0, and no value tag.