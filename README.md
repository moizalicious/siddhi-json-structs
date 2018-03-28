# JSON Structures For The Design View Of The Stream Processor Editor

## Query
A query has the following body structure

```
{
    id*: '',
    queryInput*: {},
    select: {},
    groupBy: [], 
    having: '',
    queryOutput*: {}
}
```

### Query Input
Query input can be of the following types:
* Window|Filter|Projection
* Join
* Pattern & Sequence


JSON Structure for `Window|Filter|Projection` query input type:

```
{
    type: 'window|filter|projection',
    filter: '',
    window: {
        type: '',
        parameters: ['value1', 'value2',...],
        filter: ''
    }
}
```

JSON Structure for `Join` query input type:
```
{
    type: 'join',
    leftStream: {
        name: '',
        filter: '',
        window: {
            type: '',
            paramters: ['value1','value2',...],
            filter: ''
        },
        unidirectional: true|false
        as: ''
    },
    joinType: 'join|left outer|right outer|full outer',
    rightStream: {
        name: '',
            filter: '',
            window: {
                type: '',
                paramters: ['value1','value2',...],
                filter: ''
            },
        unidirectional: true|false
        as: ''
    },
    on: ''
}
```


### Query Output
Query Output Can Have 4 different output types:
* Insert
* Delete
* Update
* Update or insert

JSON structure for `insert` query output type:
```
{
    type*: insert,
    insert*: 'current events|expired events|all events',
    into*: ''
}
```

JSON structure for the `delete` query output type:
```
{
    type*: delete,
    target*: '',
    for: 'current events|expired events|all events',
    on*: ''
}
```

JSON structure for the `update` query output type:
```
{
    type*: 'update',
    target*: '',
    for: 'current events|expired events|all events',
    clause: '',
    on*: ''
}
```

JSON structure for the `update or insert` query output type:

```
{
    type*: 'update or insert',
    target*: '',
    for: 'current events|expired events|all events',
    clause: '',
    on*: ''
}
```
