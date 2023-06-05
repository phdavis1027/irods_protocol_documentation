This XML element simulates a string-valued hashmap keyed on strings. A KeyValPair_PI mainly consists of a series of strings contained in `<keyWord>` tags, followed a series of strings contained in `<svalue>` tags. These represent a keyWord array and an svalue array respectively. To access the value stored in string `s`, iterate over the keyWord array to find the index `i` at which `s` is stored. The target value will be at index `i` of the svalue array. Additionally, the first field is `sslen`, which indicates how long both arrays are. Here's an example:

```
<KeyValPair_PI>
	<sslen>2</sslen>
	<keyWord>First</keyWord>
	<keyWord>Last</keyWord>
	<svalue>Abraham</svalue>
	<svalue>Lincoln</svalue>
</KeyValPair_PI>
```