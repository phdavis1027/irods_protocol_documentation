It appears that the iCAT database has a table dedicated to special SQL functions, which are stored as a DB instead of being loaded into the SQL server itself. This XML element provides access to these special functions. Note that the corresponding output will be sent by the server in a `GenQueryOut_PI`. There is no `SpecificQueryOut_PI`.
- sql: the name of the specific SQL command to be executed. 
- args: At this point, there may be up to 10 XML elements of the form `argX` for `X` between 1 and 10. The function of these arguments depends on the `sql` value. 
- continueInx: Similar to the field of the same name in [GenQueryInp_PI](GenQueryInp_PI#^68db59).
- rowOffset: not used currently
- options: bits for special options, currently unused.
- KeyValPair_t: represents a CondInput struct.