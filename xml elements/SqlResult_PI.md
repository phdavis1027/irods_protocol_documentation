This XML element represents a single row retrieved from the catalog SQL table. 
- attriInx: the index of the column from the SQL table being queried. NOTE: If the query that generated this result was a SpecificQuery, then the attriInx MAY be 0. This is because specific queries have formats known ahead of time, allowing the results to be interpreted by the caller.
- reslen: the length of a string representing the name of the attribute column.
- value: a self-closing tag representing 
