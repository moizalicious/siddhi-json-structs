## JSON Structures For The Design View Of The Stream Processor Editor

## Query
A query has the following body structure

```json
{
  "id*": "",
  "queryInput*": {},
  "select": {},
  "groupBy": [], 
  "having": "",
  "queryOutput*": {}
}
```

### Query Input
Query input can be of the following types:
* Window|Filter|Projection
* Join
* Pattern & Sequence


JSON Structure for `Window|Filter|Projection` query input type:

```json
{

}
```


### Query Output
Query Output Can Have 4 different output types:
* insert
* delete
* update
* update or insert

JSON structure for 'insert' query output type:
```json
{
  "type*": "insert",
  "insert*": "current events|expired events|all events",
  "into*": ""
}
```

JSON structure for the 'delete' query output type:
```json
{
  "type*": "delete",
  "target*": "",
  "for": "current events|expired events|all events", // Optional
  "on*": ""
}
```

JSON structure for the 'update' query output type:
```json
{
  "type*": "update",
  "target*": "",
  "for": "current events|expired events|all events", // Optional
  "clause": "", // Optional
  "on*": ""
}
```

JSON structure for the `update or insert` query output type:

```json
{
  "type*": "update or insert",
  "target*": "",
  "for": "current events|expired events|all events",
  "clause": "",
  "on*": ""
}
```
