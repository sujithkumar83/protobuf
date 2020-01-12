# Go
Protobuf playpen 

# Use Cases

* Helps with forward and backward compatibility of -
* Can be used to define end points for Applications on top of messages. For example for a search service

``` service SearchService {                                       
    rpc Search (SearchRequest) returns (SearchResponse)        
}
```
* can potentially replace HTTP/JSON


# Useful Other Resources 

* Uber has put together an amazing style guidelines [here] (https://github.com/uber/prototool/blob/dev/etc/style/uber1/uber1.proto)
* [Documentation](https://developers.google.com/protocol-buffers/docs/proto3)
* [Naming Conventions](https://developers.google.com/protocol-buffers/docs/reference/csharp/namespace/google/protobuf/well-known-types)
* [Wll kown Data Types] (https://developers.google.com/protocol-buffers/docs/reference/csharp/namespace/google/protobuf/well-known-types)

# Schema Changes- How to change schema w/o breaking the App

- Schema Changes- Fields may be added, deleted, modified 
  - Forward compatibility- we want to read New data written by new .proto files to be read by old code written thru old .proto file
  - Backward compatibility- we want to read old data written by old .proto files to be read by new code written thru new .proto file
- Rules for Schema Changes
  - Don't change Numeric tags for any existing fields (for ex string name=1, don't chnage 1 ever)
  - You can add new fields and old code will just ignore them
  - If old/ new code reads unknown data, the default will take place
  - Fields can be removed as long as tag number is not used again in updated message type. Better rename the field by adding OBSOLETE_ as a prefix or reserve the tag. 
  - For data type changes refer the documentation- ideally just add a new fields
- Adding fields 
  - Old code will not read the new field at all and it will be dropped
  - New code reading old data will will not find the field and hence it will assume default value

```
// v1.0 schema
message MyMessage{
    int32 id=1;
} 
// v1.1 schema
message MyMessage {
    int32 id=1;
    string first_name=2;
}
```
 - Renaming fields- No change!
    
```
// v1.0 schema

message MyMessage {
    int32 id=1;
    string first_name=2;
}  

// v1.1 schema

message MyMessage {
    int32 id=1;
    string person_first_name=2;
}  
```

 - Removing fields
   - If old code doesn't find the field it assumes a default
```
// v1.0 schema

message MyMessage {
    int32 id=1;
    string person_first_name=2;
}  

// v1.1 schema

message MyMessage{
    int32 id=1;
}
```
   - You need to reserve the tag and the name
```
// v1.0 schema

message MyMessage {
    int32 id=1;
    string person_first_name=2;
}  

// v1.1 schema

message MyMessage{

reserved 2;
reserved "first_name";
    int32 id=1;
}
```
 - Reserved Example

```
message MyMessage{

reserved 2, 15, 12, 34, 45 to 56;
reserved "first_name", "last_name";
    int32 id=1;
}  
```

   - Do not ever remove the Reserved tags


