# JSON Structures To Define Siddhi Apps

## Contents
1. [General Types](#general-types)
    1. [Key-Value Pair JSON](#key-value-pair-json)
    2. [Attributes](#attributes)
    3. [Annotations](#annotations)
    4. [Store](#store)    
2. [Stream Definition](#stream-definition)
3. [Table Definition](#table-definition)
4. [Window Definition](#window-definition)
5. [Trigger Definition](#trigger-definition)
6. [Aggregation Definition](#aggregation-definition)
7. [Source Definition](#source-definition)
8. [Sink Definition](#sink-definition)
9. [Query Definition](#query-definition)
    1. [Query Input](#query-input)
        1. [Window-Filter-Projection Query](#window-filter-projection)
        2. [Join Query](#join)
            1. [Join Stream](#join-stream)
            2. [Join Table](#join-table)
            3. [Join Aggregation](#join-aggregation)
            4. [Join Window](#join-window)
        3. [Pattern Query](#pattern)
            1. [Counting Pattern](#counting-pattern)
            2. [AND-OR Pattern](#andor-pattern)
            3. [NOT-FOR Pattern](#notfor-pattern)
            4. [NOT-AND Pattern](#notand-pattern)
        4. [Sequence Query](#sequence)
            1. [Counting Sequence](#counting-sequence)
            2. [AND-OR Sequence](#andor-sequence)
            3. [NOT-FOR Sequence](#notfor-sequence)
            4. [NOT-AND sequence](#notand-sequence)
    2. [Query Select](#query-select)
    3. [Query Output](#query-output)
        1. [Insert](#insert)
        2. [Delete](#delete)
        3. [Update](#update)
        4. [Update Or Insert](#update-or-insert)
10. [Partition Definition](#partition-definition)

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
        values*: [
            {
                value: '',
                isString: true|false
            },
            ...
        ]
    },
    << and|or >>
    {
        name*: ‘’
        type*: ‘map’,
        values*: {
            'option1': {
                value: '',
                isString: true|false
            },
            ...
        }
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
    outputEventType: ‘{current|expired|all}’,
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
    outputEventType: 'expired',
    annotationsList: []
}
```

## Trigger Definition
```
{
    id*: ‘’,
    name*: ‘’,
    at*: ‘’
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
    select*: {
        type*: 'user_defined',
        value*: [
            {
                expression*: '',
                as: ''
            },
            ...
        ]
        << or >>
        type*: 'all',
        value*: '*'
    },
    groupBy: ['value1',...],
    aggregateByAttribute*: '',
    aggregateByTimePeriod*: {
        minValue*: '', // Atleast one value should be added, and that will be marked as the minValue
        maxValue: '' // Max value is added if the user wants to define a range of timestamps
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
    select: {
        type: 'user_defined',
        value: [
            {
                expression: 'symbol',
                as: ''
            },
            {
                expression: 'avg(price)',
                as: 'avgPrice'
            },
            {
                expression: 'sum(price)',
                as: 'total'
            }
        ]
    },
    groupBy: ['symbol'],
    aggregateByAttribute: 'timestamp',
    aggregateByTimePeriod: {
        minValue: 'sec', 
        maxValue: 'year'
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

## Query Definition
All queries have the following body structure
```
{
    id*: '',
    queryInput*: {Query Input JSON},
    select*: {Query Select JSON},
    groupBy: ['value1',...],
    orderBy: [
        {
            value*: '',
            order: 'asc|desc'
        },
        ...
    ],
    limit: <integer|long>, 
    having: '',
    outputRateLimit: ''
    queryOutput*: {Query Output JSON},
    annotationList: {Annotation JSON Array}
}
```

### Query Input
The query input can be of the following types:
* Window-Filter-Projection
* Join
* Pattern & Sequence

#### <a name="window-filter-projection">JSON Structure for `Window-Filter-Projection` query input type:</a>
```
{
    type*: 'window_filter_projection',
    subType*: 'window|filter|projection',
    from*: '',
    filter: '',
    window: {
        function*: '',
        parameters*: ['value1',...],
        filter: ''
    }
}
```

_**Example:**_
```
from InputStream[age >= 18]#window.time(1 hour)[age < 30]
select ...
```
_The JSON for the above `Window-Filter-Projection` input is,_
```
{
    type: 'window_filter_projection',
    subType: 'window',
    from: 'InputStream',
    filter: 'age >= 18',
    window: {
        function: 'time',
        parameters: ['1 hour'],
        filter: 'age < 30'
    }
}
```

#### <a name="join">JSON Structure for `Join` query input type:</a>
`join` queries can be broken down to 4 types:
* Join Stream
* Join Table
* Join Aggregation
* Join Window

The way to identify a join query is using the `joinWith` attribute.

##### <a name="join-stream">JSON structure for the `Join Stream` type query</a>
```
{
    type*: 'join',
    joinWith*: 'stream'
    left*: {
        name*: '',
        filter: '', // If there is a filter, then there must be a window
        window: {
            function*: '',
            paramters*: ['value1',...],
        },
        as: '',
        isUnidirectional: true|false // Only one 'isUnidirectional' value can be true at a time
    },
    joinType*: 'join|left_outer|right_outer|full_outer',
    right*: {
        name*: '',
        filter: '', // If there is a filter, then there must be a window
        window: {
            function*: '',
            paramters*: ['value1',...],
        },
        as: '',
        isUnidirectional: true|false // Only one 'isUnidirectional' value can be true at a time
    },
    on*: ''
}
```

_**Example:**_
```
from TempStream[temp > 30.0]#window.time(1 min) as T
    join RegulatorStream[isOn == false]#window.length(1) as R
    on T.roomNo == R.roomNo
select ...
```
_The JSON for the above `Join Stream` input is,_
```
{
    type: 'join',
    joinWith: 'stream',
    left: {
        name: 'TempStream',
        filter: 'temp > 30.0',
        window: {
            function: 'time',
            parameters: ['1 min']
        },
        as: 'T',
        isUnidirectional: false
    },
    joinType: 'join',
    right: {
        name: 'RegulatorStream',
        filter: 'isOn == false',
        window: {
            function: 'length',
            parameters: ['1']
        },
        as: 'R',
        isUnidirectional: false
    },
    on: 'T.roomNo == R.roomNo'
}
```



##### <a name="join-table">JSON structure for the `Join Table` type query</a>
```
{
    type*: 'join',
    joinWith*: 'table',
    left*: {
        name*: '',
        filter: '', // If there is a filter, then there must be a window.
        window: {
            function: '',
            parameters: ['value1',...]
        },
        as: '',
        isUnidirectional: true|false // Only one 'isUnidirectional' value can be true at a time
    },
    joinType*: 'join|left_outer|right_outer|full_outer',
    right*: {
        name*: '',
        filter: '',
        window: {
            function: '',
            parameters: ['value1',...]
        },
        as: '',
        isUnidirectional: true|false // Only one 'isUnidirectional' value can be true at a time
    },
    on*: ''
}
```
_**Example:**_
```
from TempStream[temp > 25.0]#window.time(10 min) as T 
    join RoomTypeTable[roomNo > 100]#window.length(20) as R
    on R.roomNo == T.roomNo
select ...
```
_The JSON for the above `Join Table` input is,_
```
{
    type: 'join',
    joinWith: 'table',
    left: {
        name: 'TempStream',
        filter: 'temp > 25.0',
        window: {
            function: 'time',
            parameters: ['10 min']
        },
        as: 'T',
        isUnidirectional: false
    },
    joinType: 'join',
    right: {
        name: 'RoomTypeTable',
        filter: 'roomNo > 100',
        window: {
            function: 'length',
            parameters: ['20']
        },
        as: 'R',
        isUnidirectional: false
    },
    on: 'R.roomNo == T.roomNo'
}
```


##### <a name="join-aggregation">JSON structure for the `Join Aggregation` type query</a>
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
        as: '',
        isUnidirectional: true|false // Only one 'isUnidirectional' value can be true at a time
    },
    joinType*: 'join|left_outer|right_outer|full_outer',
    right*: {
        name*: '',
        filter: '',
        window: {
            function: '',
            parameters: ['value1',...]
        },
        as: '',
        isUnidirectional: true|false // Only one 'isUnidirectional' value can be true at a time
    },
    on: '',
    within*: '',
    per*: ''
}
```

_**Example:**_
```
from StockStream as S join TradeAggregation as T
    on S.symbol == T.symbol 
    within "2014-02-15 00:00:00 +05:30", "2014-03-16 00:00:00 +05:30" 
    per "days" 
select ...
```
_The JSON for the above `Join Aggregation` input is,_
```
{
    type: 'join',
    joinWith: 'aggregation',
    left: {
        name: 'StockStream',
        filter: '',
        window: {},
        as: 'S',
        isUnidirectional: false
    },
    joinType: 'join',
    right: {
        name: 'TradeAggregation',
        filter: '',
        window: {},
        as: 'T',
        isUnidirectional: false
    },
    on: 'S.symbol == T.symbol',
    within: '\"2014-02-15 00:00:00 +05:30\", \"2014-03-16 00:00:00 +05:30\"',
    per: '\"days\"'
}
```


##### <a name="join-window">JSON structure for the `Join Window` type query</a>
```
{
    type*: 'join',
    joinWith*: 'window',
    left*: {
        name*: '',
        filter: '', // If there is a filter, there must be a window.
        window: {
            function*: '',
            parameters*: ['value1',...]
        },
        as: '',
        isUnidirectional: true|false // Only one 'isUnidirectional' value can be true at a time
    },
    right*: {
        name*: '',
        filter: '',
        as: '',
        isUnidirectional: true|false // Only one 'isUnidirectional' value can be true at a time
    },
    on*: ''
}
```

_**Example:**_
```
from CheckStream as C join TwoMinTempWindow as T
    on T.temp > 40
select ...
```
_The JSON for the above `Join Window` input is,_
```
{
    type: 'join',
    joinWith: 'window',
    left: {
        name: 'CheckStream',
        filter: '',
        window: {},
        as: 'C',
        isUnidirectional: false
    },
    right: {
        name: 'TwoMinTempWindow',
        filter: '',
        as: 'T',
        isUnidirectional: false
    },
    on: 'T.temp > 40'
}
```

#### <a name="pattern">JSON structure for `pattern` query input type:</a>
All pattern queries have the following JSON structure. The JSON structure of the events depends on the type of each 
event, and that structure is added in the `value` attribute.
```
{
    type*: 'pattern',
    eventList*: [
        {COUNTING JSON | ANDOR JSON | NOTFOR JSON | NOTAND JSON},
        ...
    ]
}
```

##### <a name="counting-pattern">Structure for `counting` value JSON</a>
```
{
    type*: 'counting',
    forEvery*: true|false,
    eventReference: '',
    streamName*: '',
    filter: '',
    minCount: '',
    maxCount: '',
    within: ''
} 
```

_**Example:**_
```
-> every event1=InStream[age < 100]<21:234> within 10 min
```
_The JSON for the above `counting` event is,_
```
{
    type: 'counting',
    forEvery: 'true',
    eventReference: 'event1',
    streamName: 'InStream',
    filter: 'age < 100',
    minCount: '21',
    maxCount: '234',
    within: '10 min'
}
```


##### <a name="andor-pattern">Structure for `andor` value JSON</a>
```
{
    type*: 'andor',
    forEvery*: true|false,
    leftStreamEventReference: '',
    leftStreamName*: '',
    leftStreamFilter: '',
    connectedWith*: 'and|or',
    rightStreamEventReference: '',
    rightStreamName*: '',
    rightStreamFilter: '',
    within: ''
}
```

_**Example:**_
```
-> every event2=InStream[age > 30] and event3=InStream[age < 50] within 10 min
```
_The JSON for the above `andor` event is,_
```
{
    type: 'andor',
    forEvery: 'true',
    leftStreamEventReference: 'event2',
    leftStreamName: 'InStream',
    leftStreamFilter: 'age > 30',
    connectedWith: 'and',
    rightStreamEventReference: 'event3',
    rightStreamName: 'InStream',
    rightStreamFilter: 'age < 50',
    within: '10 min'
}
```


##### <a name="notfor-pattern">Structure for the `notfor` value JSON</a>
```
{
    type*: 'notfor',
    forEvery*: true|false,
    streamName*: '', 
    filter: '',
    forDuration*: ''
}
```

_**Example:**_
```
-> every not InStream[age >= 18] for 5 sec
```
_The JSON for the above `notfor` event is,_
```
{
    type: 'notfor',
    forEvery: 'true',
    streamName: 'InStream', 
    filter: 'age >= 18',
    forDuration: '5 sec'
}
```

##### <a name="notand-pattern">Structure for the `notand` value JSON</a>
```
{
    type*: 'notand',
    forEvery*: true|false,
    leftStreamName*: '',
    leftStreamFilter: '',
    rightStreamEventReference: '',
    rightStreamName*: '',
    rightStreamFilter: '',
    within: ''
}
```

_**Example:**_
```
-> every not InStream[age < 18] and event6=InStream[age > 30] within 10 min
```
_The JSON for the above `notand` event is,_
```
{
    type: 'notand',
    forEvery: 'true',
    leftStreamName: 'InStream',
    leftFilter: 'age < 18',
    rightStreamEventReference: 'event6',
    rightStreamName: 'InStream',
    rightFilter: 'age > 30',
    within: '10 min'
}
```

#### <a name="sequence">JSON structure for `sequence` query input type:</a>
Like pattern queries a sequence query has the same JSON body structure. The structures of the events are different 
from the JSON structures of events in a pattern query.

```
{
    type*: 'sequence',
    eventList*: [
        {COUNTING JSON | ANDOR JSON | NOTFOR JSON | NOTAND JSON},
        ...
    ]
}
```

**Note - The `forEvery` value for a JSON in the `eventList` can only be true _if that event is the first event
of the entire `eventList`_**

##### <a name="counting-sequence">Structure for `counting` value JSON</a>
```
{
    type*: 'counting',
    forEvery*: true|false,
    eventReference: '',
    streamName*: '',
    filter: '',
    countingSequence: {
        type*: 'minMax',
        minCount: '',
        maxCount: ''
        << or >>
        type*: 'countingPattern',
        value*: '+|*|?'
    },
    within: ''
} 
```

_**Example:**_
```
, every event1=InStream[age < 100]+ within 10 min
```
_The JSON for the above `default` event is,_
```
{
    type: 'counting',
    forEvery: 'true',
    eventReference: 'event1',
    streamName: 'InStream',
    filter: 'age < 100',
    countingSequence: {
        type: 'countingPattern',
        value: '+'
    },
    within: '10 min'
}
```


##### <a name="andor-sequence">Structure for `andor` value JSON</a>
```
{
    type*: 'andor',
    forEvery*: true|false,
    leftStreamEventReference: '',
    leftStreamName*: '',
    leftStreamFilter: '',
    connectedWith*: 'and|or',
    rightStreamEventReference: '',
    rightStreamName*: '',
    rightStreamFilter: '',
    within: ''
}
```

_**Example:**_
```
, event2=InStream[age > 18] and event3=InStream[age < 30] within 10 min
```
_The JSON for the above `andor` event is,_
```
{
    type: 'andor',
    forEvery: 'false',
    leftStreamEventReference: 'event2',
    leftStreamName: 'InStream',
    leftStreamFilter: 'age > 18',
    connectedWith: 'and',
    rightStreamEventReference: 'event3',
    rightStreamName: 'InStream',
    rightStreamFilter: 'age < 30',
    within: '10 min'
}
```


##### <a name="notfor-sequence">Structure for the `notfor` value JSON</a>
```
{
    type*: 'notfor',
    forEvery*: true|false,
    streamName*: '', 
    filter: '',
    forDuration*: '',
    within: ''
}
```

_**Example:**_
```
, not InStream[age >= 18] for 5 sec within 10 min
```
_The JSON for the above `notfor` event is,_
```
{
    type: 'notfor',
    forEvery: 'false',
    streamName: 'InStream', 
    filter: 'age >= 18',
    forDuration: '5 sec',
    within: '10 min'
}
```

##### <a name="notand-sequence">Structure for the `notand` value JSON</a>
```
{
    type*: 'notand',
    forEvery*: true|false,
    leftStreamName*: '',
    leftStreamFilter: '',
    rightStreamEventReference: '',
    rightStreamName*: '',
    rightStreamFilter: '',
    within: ''
}
```

_**Example:**_
```
, not InStream[age < 18] and event6=InStream[age > 30] within 10 min
```
_The JSON for the above `notand` event is,_
```
{
    type: 'notand',
    forEvery: 'false',
    leftStreamName: 'InStream',
    leftStreamFilter: 'age < 18',
    rightStreamEventReference: 'event6',
    rightStreamName: 'InStream',
    rightStreamFilter: 'age > 30',
    within: '10 min'
}
```

### Query Select
```
{
    type*: 'user_defined',
    value*: [
        {
            expression*: '',
            as: ''
        },
        ...
    ]
    << or >>
    type*: 'all',
    value*: '*'
}
```

_**Example:**_
```
select Stream1.id as UID, avg(price) as avgPrice, ((TempStream.temp - 32) * 5)/9 as Celsius, isInStock ...
```
_The JSON for the above `select` function is,_
```
{
    type: 'user_defined',
    value: [
        {
            expression: 'Stream1.id',
            as: 'UID'
        },
        {
            expression: 'avg(price)',
            as: 'avgPrice'
        },
        {
            expression: '((TempStream.temp - 32) * 5)/9',
            as: 'Celsius'
        },              
        {
            expression: 'isInStock',
            as: ''
        }
    ]
}
```

### Query Output
All query outputs have the following generic structure:
```
{
    type*: 'insert|delete|update|update-or-insert-into',
    output*: {INSERT JSON|DELETE JSON|UPDATE JSON|UPDATE-OR-INSERT JSON},
    target*: ''
}
```

The `output` Attribute Can Be Of 4 JSON Structures:
* Insert
* Delete
* Update
* Update or insert into

#### <a name="insert">JSON structure for `insert` query output type:</a>
```
{
    eventType: 'current|expired|all'
}
```

_**Example:**_
```
insert all events into LogStream;
```
_The JSON for the above `insert` function is,_
```
{
    type: 'insert',
    output: {
        eventType: 'all'
    },
    target: 'LogStream'
}
```

#### <a name="delete">JSON structure for the `delete` query output type:</a>
```
{
    forEventType: 'current|expired|all',
    on*: ''
}
```

_**Example:**_
```
delete RoomTypeTable
    for all events 
    on RoomTypeTable.roomNo == roomNumber;
```
_The JSON for the above `insert` function is,_
```
{
    type: 'delete',
    output: {
        forEventType: 'all',
        on: 'RoomTypeTable.roomNo == roomNumber'
    },
    target: 'RoomTypeTable',
}
```

#### <a name="update">JSON structure for the `update` query output type:</a>
```
{
    forEventType: 'current|expired|all',
    set*: [
        {
            attribute*: '',
            value*: ''
        },
        ...
    ],
    on*: ''
}
```

_**Example:**_
```
update RoomTypeTable
    set RoomTypeTable.people = RoomTypeTable.people + arrival - exit
    on RoomTypeTable.roomNo == roomNumber;
```
_The JSON for the above `update` function is,_
```
{
    type: 'update',
    output: {
        forEventType: '',
        set: [
            {
                attribute: 'RoomTypeTable.people',
                value: 'RoomTypeTable.people + arrival - exit'
            }
        ],
        on: 'RoomTypeTable.roomNo == roomNumber'
    },
    target: 'RoomTypeTable',
}
```


#### <a name="update-or-insert">JSON structure for the `update or insert into` query output type:</a>
```
{
    forEventType: 'current|expired|all',
    set*: [
        {
            attribute*: '',
            value*: ''
        },
        ...
    ],
    on*: ''
}
```

_**Example:**_
```
update or insert into RoomAssigneeTable
    set RoomAssigneeTable.assignee = assignee
    on RoomAssigneeTable.roomNo == roomNo;
```
_The JSON for the above `update or insert into` function is,_
```
{
    type: 'update_or_insert_into',
    output: {
        forEventType: '',
        set: [
            {
                attribute: 'RoomAssigneeTable.assignee',
                value: 'assignee'
            }
        ],
        on: 'RoomAssigneeTable.roomNo == roomNo'
    },
    target: 'RoomAssigneeTable',
}
```

## Partition Definition
```
{
    id*: ‘’,
    members*: {
        queryIds*: ['query1',...],
        innerStreamIds: ['stream1',...] 
    },
    partitionWith*: {
        type*: ‘{value|range}’,
        expressions*: [
            {
                condition: ‘’,
                streamId: ‘’
            },
            ...
        ]
    },
    annotationList: {Annotation JSON Array}
}
```

_**Example:**_
```
@info(name='TestPartition')
partition with ( roomNo >= 1030 as 'serverRoom' or
                 roomNo < 1030 and roomNo >= 330 as 'officeRoom' or
                 roomNo < 330 as 'lobby' of TempStream)
begin
    @info(name='TestQuery1') 
    from TempStream
    select *
    insert into #OutputStream;

    @info(name='TestQuery2') 
    from TempStream#window.time(10 min)
    select roomNo, deviceID, avg(temp) as avgTemp
    insert into AreaTempStream;
end;
```
_The JSON for the above `partition` is,_
```
{
    id: ‘TestPartition’,
    members: {
        queryIds: ['TestQuery1','TestQuery2'],
        innerStreamIds: ['OutputStream'] 
    },
    partitionWith: {
        type: ‘range’,
        expressions: [
            {
                condition: ‘roomNo >= 1030 as \'serverRoom\'
                    or roomNo < 1030 and roomNo >= 330 as \'officeRoom\'
                    or roomNo < 330 as \'lobby\'’,
                streamId: ‘TempStream’
            }
        ]
    },
    annotationList: [
        {
            name: ‘info’
            type: ‘map’,
            values: {
                'name': 'TestPartition'  
            }
        }
    ]
}
```
_The JSON for `TestQuery1` is,_
```
{
    id: 'TestQuery1',
    queryInput: {
        type: 'window_filter_projection',
        from: 'TempStream',
        filter: '',
        window: {}
    },
    select: {
        type: 'all',
        value: '*'
    },
    groupBy: [],
    orderBy: [],
    limit: '', 
    having: '',
    output: ''
    queryOutput: {
        type: 'insert',
        eventType: '',
        target: '#OutputStream'
    },
    annotationList: [
        {
            name: ‘info’
            type: ‘map’,
            values: {
                'name': 'TestQuery1'  
            }
        }
    ]
}
```
_The JSON for `TestQuery2` is,_
```
{
    id: 'TestQuery2',
    queryInput: {
        type: 'window_filter_projection',
        from: 'TempStream',
        filter: '',
        window: {
            function: 'time',
            parameters: ['10 min'],
            filter: ''
        }
    },
    select: {
        type: 'user_defined',
        value: [
            {
                expression: 'roomNo',
                as: ''
            },
            {
                expression: 'deviceID',
                as: ''
            },
            {
                expression: 'avg(Temp)',
                as: 'avgTemp'
            }
        ]
    },
    groupBy: [],
    orderBy: [],
    limit: '',
    having: '',
    output: ''
    queryOutput: {
        type: 'insert',
        eventType: '',
        target: 'AreaTempStream'
    },
    annotationList: [
        {
            name: ‘info’
            type: ‘map’,
            values: {
                'name': 'TestPartition'  
            }
        }
    ]
}
```
