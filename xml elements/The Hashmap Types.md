The iRODS protocol provides several serialization types which amount to associative arrays/hash maps with keys and values of different types. The Python iRODS Client (PRC) makes these types explicit through its naming convention for these serialization types. Below are some PRC types and their corresponding iRODS protocol serialization types, followed by a type declaration in the style of Java or C++:
- IntegerStringMap : InxValPair_PI : HashMap<int, String>
- StringStringMap : KeyValPair_PI : HashMap<String, String>
- IntegerIntegerMap : InxIvalPair_PI : HashMap<int, int>

	In terms of other generic types, it seems like there are a couple of other "genericts." types, namely those which get unpacked into a `dataArray_t`. `dataArray_t` itself just hold a void pointer to its contents. The first field of `dataArray_t` is an int called `type` which signals to the iRODS packing/unpacking logic how to do the pointer arithmetic necessary to interpret the contents as an array. The packing instructions for the `dataArray_t` types are as follows, from `rodsDef.h`: 
```cpp
/* PI for dataArray_t */
#define charDataArray_PI "int type; int len; char *buf(len);"
#define strDataArray_PI "int type; int len; str *buf[len];"
#define intDataArray_PI "int type; int len; int *buf(len);"
#define int16DataArray_PI "int type; int len; int16 *buf(len);"
#define int64DataArray_PI "int type; int len; double *buf(len);"
```

