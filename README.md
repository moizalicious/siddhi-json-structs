# JSON Structures To Define Siddhi Apps

## General Types
These are general structures that are used in most of the siddhi element definitions, they are not stand 
alone elements (that is why they do not have an 'id'). 

### Key-Value Pair JSON
This just is used to define a map of key-value pair and is named as `{Key-Value Pair JSON}` when the structure 
is referred.
```
{
    `key`: `value`,
    ...
}
```

### Attributes
_**NOTE - The JSON attributes that end with a `*` symbol means that the attribute cannot be left null/empty**_

Defines attributes of a Stream, Table etc.. that have the same format. So they all use the same structure 
which is named `attributeList` where ever they are used in a element definition.
```
[
    {
        name*: ‘’,
        type*: ‘string|int|long|double|float|bool|object’
    },
    ...
]
```

_**Example:** To define the attributes `(name string, age int)` the JSON structure would look like this,_
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
Defines the annotations of any Siddhi element, as all annotations have somewhat the same structure.
They are defined in as a part of a Siddhi element using the name `annotationList`, 
and have the following JSON structure.
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

_**Example:** To define the annotations `@Async(buffer.size='1024') @PrimaryKey('name','age')` the JSON 
structure would look like this,_
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

**Note - Sources, Sinks & Stores do not come under the general annotation struct as they have a different 
structure and are show separately**

### Store
The store annotation is only used for tables and aggregations. So they are only defined in both table and 
aggregation JSON structs as the name `store` in the following format:
```
{
    type*: ‘’,
    options*: {Key-Value Pair JSON}
}
```

_**Example:**_
```
@Store(type="rdbms",
    jdbc.url="jdbc:mysql://localhost:3306/production",
    username="wso2",
    password="123" ,
    jdbc.driver.name="com.mysql.jdbc.Driver")
```
_The JSON for the above store definition is,_
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
    id*: '',
    name*: '',
    isInnerStream*: true|false,
    attributeList*: {Attributes JSON Array},
    annotationList: {Annotations JSON Array}
}
```

_**Example:**_
```
@Async(buffer.size="1024")
define stream InStream(name string, age int);
```
_The JSON for the above stream definition is,_
```
{
    id: 'InStream',
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
**Note that if a stream is an inner stream, then it's attributes cannot be defined. 
Have to handle this in another way.**

## Table Definition
```
{
    id*: ‘’,
    name*: ‘’,
    attributeList*: {Attributes JSON Array},
    store: {Store JSON},
    annotationList: {Annotations JSON Array}
}
```

_**Example:**_
```
define table InTable(name string, age int);
```
_The JSON for the above table definition is,_
```
{
    id: 'InTable',
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
    id*: ‘’,
    name*: ‘’,
    attributeList*: {Attributes JSON Array},
    function*: ‘time|length|timeBatch|lengthBatch...’,
    parameters*: ['value1',...],
    outputEventType: ‘{current events|expired events|all events}’,
    annotationList: {Annotations JSON Array}
}
```

_**Example:**_
```
define window SensorWindow (name string, value float) timeBatch(1 second) output expired events;
```
_The JSON for the above window definition is,_
```
{
    id: 'SensorWindow',
    name: 'SensorWindow',
    attributeList: [
        {
            name: 'name',
            type: 'string'
        },
        {
            name: 'value',
            type: 'float'
        }
    ],
    function: 'timeBatch',
    parameters: ['1 second'],
    outputEventType: 'expired events',
    annotationsList: []
}
```

## Trigger Definition
```
{
    id*: ‘’,
    name*: ‘’,
    at*: ‘{every|start|cron-expression}’
    annotationList: {Annotations JSON Array}
}
```

_**Example:**_
```
define trigger FiveMinTrigger at every 5 min;
```
_The JSON for the above trigger definition is,_
```
{
    id: 'FiveMinTrigger',
    name: 'FiveMinTrigger',
    at: 'every 5 min',
    annotationList: []
}
```

## Aggregation Definition
```
{
    id*: ‘’,
    name*: ‘’,
    from*: ‘’,
    select*: [
        {
            name*: ‘’,
            aggregate: {
                aggregateFunction*: ‘’,
                attribute*: ‘’    
            }
        },
        ...
    ],
    groupBy: ['value1',...],
    aggregateBy*: {
        timeStamp*: ‘’,
        timePeriod*: ‘’
    },
    store: {Store JSON},
    annotationList: {Annotations JSON Array}
}
```

_**Example:**_
```
define aggregation TradeAggregation
    from TradeStream
        select symbol, avg(price) as avgPrice, sum(price) as total
        group by symbol
        aggregate by timestamp every sec ... year;
```
_The JSON for the above aggregation definition is,_
```
{
    id: 'TradeAggregation',
    name: 'TradeAggregation',
    from: 'TradeStream',
    select: [
        {
            name: 'symbol',
            aggregate: {}
        },
        {
            name: 'avgPrice',
            aggregate: {
                aggregateFunction: ‘avg’,
                attribute: ‘price’
            }
        },
        {
            name: 'total',
            aggregate: {
                aggregateFunction: ‘sum’,
                attribute: ‘price’    
            }
        }
    ],
    groupBy: ['symbol'],
    aggregateBy: {
        timeStamp: 'timestamp',
        timePeriod: 'sec...year'
    },
    store: {},
    annotationList: []
}
```

## Source Definition
```
{
    id*: ‘’,
    type*: ‘’,
    options: {Key-Value Pair JSON},
    map: {
        type*: ‘’,
        options: {Key-Value Pair JSON},
        attributes: {
            type*: ‘map’
            value*: {Key-Value Pair JSON}
        }
        << or >>
        attributes: {
            type*: ‘list’
            value*: ['value1',...]
        }
    }
}
```

_**Example:**_
```
@Source(
    type = 'http',
    receiver.url='http://localhost:8006/productionStream',
    basic.auth.enabled='false',
    @map(type='json')
)
```
_The JSON for the above source definition is,_
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
    id*: ‘’,
    type*: ‘’,
    options: {Key-Value Pair JSON},
    map: {
        type*: ‘’,
        options: {Key-Value Pair JSON},
        payload: {
            type*: ‘map’,
            value*: {Key-Value Pair JSON}
        }
        << or  >>
        payload: {
            type*: 'single',
            value*: ''
        }
    }
}
```

_**Example:**_
```
@sink(
    type='http',
    publisher.url='http://localhost:8005/endpoint', 
    method='POST', 
    headers='Accept-Date:20/02/2017', 
    basic.auth.username='admin', 
    basic.auth.password='admin', 
    basic.auth.enabled='true',
    @map(type='json')
)
```
_The JSON for the above sink definition is,_
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

## Query Definition (NOT FINALISED)
All queries have the following body structure
```
{
    id*: '',
    queryInput*: {Query Input JSON},
    select*: {Query Select JSON},
    groupBy: ['value1',...], 
    having: '',
    output: ''
    queryOutput*: {Query Output JSON}
}
```

### Query Input (NOT FINALISED)
The query input can be of the following types:
* Window|Filter|Projection
* Join
* Pattern & Sequence

#### JSON Structure for `Window|Filter|Projection` query input type:
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

#### JSON Structure for `Join` query input type:_NOT FINALISED_
`join` queries can be broken down to 4 types:
* Join Stream
* Join Table
* Join Aggregation
* Join Window

The way to identify if a join query is using `joinWith` attribute.

##### JSON structure for the `Join Stream` type query
```
{
    type*: 'join',
    joinWith*: 'stream'
    left*: {
        name*: '',
        filter: '',
        window: {
            type: '',
            paramters: ['value1',...],
            filter: ''
        },
        unidirectional: true|false
        as*: ''
    },
    joinType*: 'join|left outer|right outer|full outer',
    right*: {
        name*: '',
        filter: '',
        window: {
            type: '',
            paramters: ['value1',...],
            filter: ''
        },
        unidirectional: true|false
        as*: ''
    },
    on: ''
}
```

##### JSON structure for the `Join Table` type query
```
{
    type*: 'join',
    joinWith*: 'table',
    left*: {
        name*: '',
        filter: '', // If there is a filter, there must be a window.
        window: {
            function: '',
            parameters: ['value1',...]
        },
        as: ''
    },
    joinType*: 'join|left outer|right outer|full outer',
    right*: {
        name*: '',
        filter: '',
        window: {
            function: '',
            parameters: ['value1',...]
        },
        as: ''
    },
    on: ''
}
```

##### JSON structure for the `Join Aggregation` type query
```
{
    type*: 'join',
    joinWith*: 'aggregation',
    left*: {
        name*: '',
        filter: '', // If there is a filter, there must be a window.
        window: {
            function: '',
            parameters: ['value1',...]
        },
        as: ''
    },
    joinType*: 'join|left outer|right outer|full outer',
    right*: {
        name*: '',
        filter: '',
        window: {
            function: '',
            parameters: ['value1',...]
        },
        as: ''
    },
    on: '',
    within*: '',
    per*: ''
}
```

##### JSON structure for the `Join Window` type query
```
{
    queryType: 'join',
    joinWith: 'window',
    left: {
        name: '',
        filter: '', // If there is a filter, there must be a window.
        window: {
            function: '',
            parameters: ['value1',...]
        },
        as: ''
    },
    right: {
        name: '',
        filter: '',
        as: ''
    },
    on: ''
}
```



#### JSON structure for `pattern` query input type:
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

##### Structure for `default` value JSON
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

##### Structure for `andor` value JSON
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

##### Structure for the `notfor` value JSON
```
{
    forEvery: 'true|false',
    streamName: '', 
    filter: '',
    for: ''
}
```

##### Structure for the `notand` value JSON
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

#### JSON structure for `sequence` query input type:
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

##### Structure for `default` value JSON
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

##### Structure for `andor` value JSON
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

##### Structure for the `notfor` value JSON
```
{
    streamName: '', 
    filter: '',
    for: ''
}
```

##### Structure for the `notand` value JSON
```
{
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


### Query Select (NOT FINALISED)
```
{
    type*: 'user-defined',
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

### Query Output (NOT FINALISED)
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

## Partition Definition (NOT FINALISED)
```

```

# Full Siddhi App Definition (NOT FINALISED)
```

```
