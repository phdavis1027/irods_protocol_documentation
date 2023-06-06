Consider the file `hello.cpp`:
```
int main(int argc, char** argv)
{
        std::cout << "Hello world!" << std::endl;
        return 0;
}
```
and consider running the simple command `iget hello.cpp` from the collection `/tempZone/home/rods`. What protocol transactions happen to execute the file transfer? 

After the initial connection handshake, the client will send a request to stat the target collection: 
```
<MsgHeader_PI>
   <type>RODS_API_REQ</type>
   <msgLen>264</msgLen>
   <errorLen>0</errorLen>
   <bsLen>0</bsLen>
   <intInfo>633</intInfo>
</MsgHeader_PI>
<DataObjInp_PI>
   <objPath>/tempZone/home/rods</objPath>
   <createMode>0</createMode>
   <openFlags>0</openFlags>
   <offset>0</offset>
   <dataSize>0</dataSize>
   <numThreads>0</numThreads>
   <oprType>0</oprType>
   <KeyValPair_PI>
      <ssLen>0</ssLen>
   </KeyValPair_PI>
</DataObjInp_PI>
```

The server will then respond with a header whose `intInfo` field is set 2, followed by a `RodsObjStat_PI` with `objType` 2 for collection. 

Once the client has received this stat, the client will then send a header with `intInfo` set to 633 for `RODS_STAT_AN` followed by a `DataObjInp_PI` with `objPath` set to the pwd from which the `iget` was sent, and the `oprType` set to 0 (which I'm assuming means that it's just not used here). `createMode`, `openFlags`, `offset`, and `dataSize` are also set to 0. The CondInput [KeyValPair_PI](KeyValPair_PI) is empty, with `sslen` equal to 0. 

Once the stat has been exchanged, the client then sends a header with `intInfo` set to 606 for `DATA_OBJ_PUT_AN` and `bsLen` set to the length in bytes of the file being transferred. Following this, the client will send another `DataObjInp_PI` with `objPath` set to the absolute logical path to which the file is being transferred within the zone, `dataSize` set to the size in bytes of the file being transferred, `openFlags` set to 2, and `oprType` set to 2. It also includes a CondInput. After this message has been sent, the contents of the bsBuf are transferred without any additional padding or formatting. This is the actual file transfer. If the server successfully receives this data, it responds with a header of type `RODS_API_REPLY` with all other fields set to 0, and if the client has no further work to do it will send a header of types `RODS_DISCONNECT` and all other fields set to 0. 


