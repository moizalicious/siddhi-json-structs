# JSON Structures For The Design View Of The Stream Processor Editor

## General Types

### Attributes
Attributes of a Stream, Table etc.. have the same format. So they all use the same structure which is named `attributeList` where ever they are used in a element definition.
```
[
    {
        name: ‘’,
        type: ‘’
    },
    {
            name: ‘’,
            type: ‘’
    },
    ...
]
```

### Annotations
All annotations have somewhat the same structure, so annotations are shown in an element definition as `annotationList` and have the following structure:
```
[
    {
        name*: ‘’,
        type*: ‘list’,
        values*: [‘value1’,’value2’]
    },
    *and|or*
    {
        name*: ‘’
        type*: ‘map’,
        values*: {‘option’:’value’}
    },
    ...
]

```
**Note - Sources, Sinks & Stores do not come under the general annotation struct as they have a different structure and are show seperately**

### Stores
The store annotation is only used for tables and aggregations. So they are only defined in both table and aggregation structs in the following format:
```
{
    type: ‘’,
    options: {
        ‘option’: ‘value’
    }
}
```

## Stream
```
id: '',
name: '',
isInnerStream: {boolean},
attributeList: *Attributes Struct*,
annotationList: *Annotation Struct*

```

## Source
```
id: ‘’,
type: ‘’,
options: {
    ‘option’: ‘value’ 
},
map: {
    type: ‘’,
    options: {
        ‘option’: ‘value’
    }
    attributes: {
        ‘name’: ‘attributes’,
        ‘type’: ‘map’
        ‘value’: {
            ‘attribute1’: ‘value1’
        }
    }
    **or**
    attributes: {
        ‘name’: ‘attributes’,
        ‘type’: ‘list’
        ‘value’: [‘value1’, ‘value2’]
    }
}
```

## Sink
```
id: ‘’,
type: ‘’,
options: {
    ‘option’: ‘value’
},
map: {
    type: ‘’,
    options: {
        ‘option’: ‘value’
    },
    payload: {
        name: ‘payload’,
        type: ‘map’,
        value: {
          ‘key’: ‘value’
        } 
    }
    or
    payload: ‘’
}
```

## Table
```
id: ‘’,
name: ‘’,
attributeList: *Attributes Struct*,
store: *Store Struct*,
annotationList: *Annotation Struct*
```

## Window
```
    id: ‘’,
   name: ‘’,
   attributeList: *Attributes Struct*,
   type: ‘’,
   parameters: [‘param1’, ‘param2’],
   outputEventType: ‘{current events|expired events|all events}’,
   annotationList: *Annotations Struct*
```

## Trigger
```
    id: ‘’,
    name: ‘’,
    at: ‘{every|start|cron-expression}’
    annotationList: *Annotations Struct*
```

## Aggregation
```
id: ‘’,
   name: ‘’,
   from: ‘’,
   select: [
       {
           name: ‘’,
           aggregateFunction: ‘’,
           attribute: ‘’
       }
   ],
   groupBy: ‘’,
   aggregateBy: {
       timeStamp: ‘’,
       timePeriod: ‘’
   },
   store: *Store Struct*,
   annotationList: *Annotation Struct*
```


## Query
A query has the following body structure

```
{
    id*: '',
    queryInput*: {},
    select*: {},
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


**JSON Structure for `Window|Filter|Projection` query input type:**
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

**JSON Structure for `Join` query input type:_NOT FINALISED_**
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

**JSON structure for `insert` query output type:**
```
{
    type*: insert,
    insert*: 'current events|expired events|all events',
    into*: ''
}
```

**JSON structure for the `delete` query output type:**
```
{
    type*: delete,
    target*: '',
    for: 'current events|expired events|all events',
    on*: ''
}
```

**JSON structure for the `update` query output type:**
```
{
    type*: 'update',
    target*: '',
    for: 'current events|expired events|all events',
    clause: '',
    on*: ''
}
```

**JSON structure for the `update or insert` query output type:**
```
{
    type*: 'update or insert',
    target*: '',
    for: 'current events|expired events|all events',
    clause: '',
    on*: ''
}
```
