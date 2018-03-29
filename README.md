# JSON Structures To Define Siddhi Apps

## General Types
These are general structures that are used in most of the siddhi element definitions, and are not stand alone elements (that is why they do not have an 'id'). 

### Key-Value Pair JSON
This just is used to define a map of key-value pair and is named as `{Key-Value Pair JSON}`.
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

_Example: If to define the attributes `(name string, age int)` the JSON structure would look something like this,_
```
[
    {
        name: 'name',
        type: 'string'
    },
    {
        name: 'age',
        type: 'int'
    }
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

_Example: `@Async(buffer.size='1024') @PrimaryKey('name','age')`_
```
[
    {
        name: 'Async',
        type: 'map',
        value: {
            'buffer.size': '1024'
        }
    },
    {
        name: 'PrimaryKey',
        type: 'list',
        values: ['name', 'age']
    }
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

_Example: `@Store(type="rdbms",
                  jdbc.url="jdbc:mysql://localhost:3306/production",
                  username="wso2",
                  password="123" ,
                  jdbc.driver.name="com.mysql.jdbc.Driver")`_
```
{
    type: 'rdbms',
    options: {
        'jdbc.url': 'jdbc:mysql://localhost:3306/production',
        'username': 'wso2',
        'password': '123',
        'jdbc.driver.name': 'com.mysql.jdbc.Driver'
    }
}
```                  
                  

## Stream Definition
```
{
    id: '',
    name: '',
    isInnerStream: true|false,
    attributeList: {Attributes JSON Array},
    annotationList: {Annotations JSON Array}
}
```

_Example: `@Async(buffer.size="1024") define stream InStream(name string, age int);`_
```
{
    id: '<UUID>',
    name: 'InStream',
    isInnerStream: false,
    attributeList: [
        {
            name: 'name',
            type: 'string'
        },
        {
            name: 'age',
            type: 'int'
        }
    ],
    annotationList: [
        {
            name: 'Async',
            type: 'map',
            values: {
                'buffer.size': '1024'
            }
        }
    ]
}
```


## Source Definition
```
{
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
}
```

_Example: `@Source(type = 'http',
                   receiver.url='http://localhost:8006/productionStream',
                   basic.auth.enabled='false',
                   @map(type='json'))`_
```
{
    id: '<UUID>',
    type: 'http',
    options: {
        'receiver.url': 'http://localhost:8006/productionStream',
        'basic.auth.enabled': 'false',
    },
    map: {
        type: 'json',
        options: {},
        attributes: {}
    },
}
```                   


## Sink Definition
```
{
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
}
```

_Example: `@sink(type='http', publisher.url='http://localhost:8005/endpoint', method='POST', headers='Accept-Date:20/02/2017', 
             basic.auth.username='admin', basic.auth.password='admin', basic.auth.enabled='true',
             @map(type='json'))`_
```
{
    id: '<UUID>',
    type: 'http',
    options: {
        'publisher.url': 'http://localhost:8005/endpoint',
        'method': 'POST',
        'headers': 'Accept-Date:20/02/2017',
        'basic.auth.username': 'admin',
        'basic.auth.password': 'admin',
        'basic.auth.enabled': 'true'
    },
    map: {
        type: 'json',
        options: {},
        payload: {}
    }
}
```

## Table Definition
```
{
    id: ‘’,
    name: ‘’,
    attributeList: {Attributes JSON Array},
    store: {Store JSON},
    annotationList: {Annotations JSON Array}
}
```

_Example: `define table InTable(name string, age int);`_
```
{
    id: '<UUID>',
    name: 'InTable',
    attributeList: [
        {
            name: 'name',
            type: 'string'
        },
        {
            name: 'age',
            type: 'int'
        }
    ],
    store: {},
    annotationList: []
}
```

## Window Definition
```
{
    id: ‘’,
    name: ‘’,
    attributeList: {Attributes JSON Array},
    type: ‘’,
    parameters: ['value1',...],
    outputEventType: ‘{current events|expired events|all events}’,
    annotationList: {Annotations JSON Array}
}
```

## Trigger Definition
```
{
    id: ‘’,
    name: ‘’,
    at: ‘{every|start|cron-expression}’
    annotationList: {Annotations JSON Array}
}
```

## Aggregation Definition
```
{
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
}
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

**JSON structure for `pattern` query input type:**
```
{
    type: 'pattern',
    events: [
        {
            type: 'default | andor | notfor | notand',
            value: {Default JSON | ANDOR JSON | NOTFOR JSON | NOTAND JSON}
        },
        ...
    ]
}
```

_Structure for `default` value JSON_
```
{
    forEvery: 'true|false',
    eventReference: '',
    streamName: '',
    filter: '',
    minCount: '',
    maxCount: ''
} 
```

_Structure for `andor` value JSON_
```
{
    forEvery: 'true|false',
    firstStream: {
        eventReference: '',
        streamName: '',
        filter: ''
    },
    connectedWith: 'and|or',
    secondStream: {
        eventReference: '',
        streamName: '',
        filter: ''
    }
}
```

_Structure for the `notfor` value JSON_
```
{
    forEvery: 'true|false',
    streamName: '', 
    filter: '',
    for: ''
}
```

_Structure for the `notand` value JSON_
```
{
    forEvery: 'true|false',
    firstStream: {
        streamName: '',
        filter: ''
    },
    secondStream: {
        eventReference: '',
        streamName: '',
        filter: ''
    }
}
```

**JSON structure for `sequence` query input type:**
```
{
    type: 'sequence',
    events: [
        {
            type: 'default | andor | notfor | notand',
            value: {Default JSON | ANDOR JSON | NOTFOR JSON | NOTAND JSON}
        },
        ...
    ]
}
```

_Structure for `default` value JSON_
```
{
    forEvery: 'true|false',
    eventReference: '',
    streamName: '',
    filter: '',
    countingSequence: {
        type: 'minMax',
        value: {
            minCount: '',
            maxCount: ''
        }
        << or >>
        type: 'countingPattern',
        value: '+|*|?'
    }
} 
```

_Structure for `andor` value JSON_
```
{
    firstStream: {
        eventReference: '',
        streamName: '',
        filter: ''
    },
    connectedWith: 'and|or',
    secondStream: {
        eventReference: '',
        streamName: '',
        filter: ''
    }
}
```

_Structure for the `notfor` value JSON_
```
{
    streamName: '', 
    filter: '',
    for: ''
}
```

_Structure for the `notand` value JSON_
```
{
    forEvery: 'true|false',
    firstStream: {
        streamName: '',
        filter: ''
    },
    secondStream: {
        eventReference: '',
        streamName: '',
        filter: ''
    }
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
    set: '',
    on*: ''
}
```

**JSON structure for the `update or insert` query output type:**
```
{
    type*: 'update or insert',
    target*: '',
    for: 'current events|expired events|all events',
    set: '',
    on*: ''
}
```

## Partition Definition
```

```

# Full Siddhi App Definition
```

```
