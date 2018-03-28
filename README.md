# JSON Structures To Define Siddhi Apps

## General Types
These are the structures that are used in most of the siddhi elements, so they have been written here as general structs because the following structs have been used in two or more siddhi element definitions.

### Key-Value Pair JSON
```
{
    `key`: `value`,
    ...
}
```

### Attributes
Defines attributes of a Stream, Table etc.. that have the same format. So they all use the same structure which is named `attributeList` where ever they are used in a element definition.
```
[
    {
        name*: ‘’,
        type*: ‘string|int|long|double|float|bool|object’
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
        values*: ['value1',...]
    },
    << and|or >>
    {
        name*: ‘’
        type*: ‘map’,
        values*: {Key-Value Pair JSON}
    },
    ...
]

```
**Note - Sources, Sinks & Stores do not come under the general annotation struct as they have a different structure and are show seperately**

### Store
The store annotation is only used for tables and aggregations. So they are only defined in both table and aggregation structs as the name `store` in the following format:
```
{
    type: ‘’,
    options: {Key-Value Pair JSON}
}
```

## Stream Definition
```
id: '',
name: '',
isInnerStream: true|false,
attributeList: {Attributes JSON Array},
annotationList: {Annotations JSON Array}
```

## Source Definition
```
id: ‘’,
type: ‘’,
options: {Key-Value Pair JSON},
map: {
    type: ‘’,
    options: {Key-Value Pair JSON},
    attributes: {
        type: ‘map’
        value: {Key-Value Pair JSON}
    }
    << or >>
    attributes: {
        type: ‘list’
        value: ['value1',...]
    }
}
```

## Sink Definition
```
id: ‘’,
type: ‘’,
options: {Key-Value Pair JSON},
map: {
    type: ‘’,
    options: {Key-Value Pair JSON},
    payload: {
        type: ‘map’,
        value: {Key-Value Pair JSON}
    }
    << or  >>
    payload: {
        type: 'single',
        value: ''
    }
}
```

## Table Definition
```
id: ‘’,
name: ‘’,
attributeList: {Attributes JSON Array},
store: {Store JSON},
annotationList: {Annotations JSON Array}
```

## Window Definition
```
    id: ‘’,
    name: ‘’,
    attributeList: {Attributes JSON Array},
    type: ‘’,
    parameters: ['value1',...],
    outputEventType: ‘{current events|expired events|all events}’,
    annotationList: {Annotations JSON Array}
```

## Trigger Definition
```
    id: ‘’,
    name: ‘’,
    at: ‘{every|start|cron-expression}’
    annotationList: {Annotations JSON Array}
```

## Aggregation Definition
```
id: ‘’,
   name: ‘’,
   from: ‘’,
   select: [
       {
           name: ‘’,
           aggregateFunction: ‘’,
           attribute: ‘’
       },
       ...
   ],
   groupBy: ['value1',...],
   aggregateBy: {
       timeStamp: ‘’,
       timePeriod: ‘’
   },
   store: {Store JSON},
   annotationList: {Annotations JSON Array}
```


## Query Definition
A query has the following body structure
```
{
    id*: '',
    queryInput*: {Query Input JSON},
    select*: {Query Select JSON},
    groupBy: ['value1',...], 
    having: '',
    queryOutput*: {Query Output JSON}
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
    from: '',
    filter: '',
    window: {
        type: '',
        parameters: ['value1',...],
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
            paramters: ['value1',...],
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
                paramters: ['value1',...],
                filter: ''
            },
        unidirectional: true|false
        as: ''
    },
    on: ''
}
```

**JSON structure for `pattern & sequence` query input type:**
```
{
    
}
```

### Query Select
```
{
    type*: 'user defined',
    value*: [
        {
            condition: '',
            as: ''
        },
        ...
    ]
    << or >>
    type*: 'all',
    value*: '*'
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
    type*: 'insert',
    insert*: 'current events|expired events|all events',
    into*: ''
}
```

**JSON structure for the `delete` query output type:**
```
{
    type*: 'delete',
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

## Partition Definition
```

```