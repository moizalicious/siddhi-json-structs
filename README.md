# JSON Structures To Define Siddhi Apps

## Contents
1. [General Types](#general-types)
    1. [Key-Value Pair JSON](#key-value-pair-json)
    2. [Attributes](#attributes)
    3. [Annotations](#annotations)
    4. [Store](#store)  
    5. [Stream Handler](#stream-handler)  
2. [Stream Definition](#stream-definition)
3. [Table Definition](#table-definition)
4. [Window Definition](#window-definition)
5. [Trigger Definition](#trigger-definition)
6. [Aggregation Definition](#aggregation-definition)
7. [Function Definition](#function-definition)
8. [Source & Sink Definition](#source-and-sink-definition)
9. [Query Definition](#query-definition)
    1. [Query Input](#query-input)
        1. [Window-Filter-Projection Query](#window-filter-projection)
        2. [Join Query](#join)
        3. [Pattern & Sequence Query](#pattern-sequence)
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
This is used to define a map of key-value pair and is named as `{Key-Value Pair JSON}` when the structure 
is referred.
```
{
    `key`: `value`,
    ...
}
```

### Attributes
Define's attributes of a Stream, Table etc.. that have the same format. So they all use the same structure 
which is named `attributeList` where ever they are used in a element definition.
```
[
    {
        name*: ‘’,
        type*: ‘STRING|INT|LONG|DOUBLE|FLOAT|BOOL|OBJECT’
    },
    ...
]
```
_**NOTE - The JSON attributes that end with a `*` symbol means that the attribute cannot be left null/empty**_

_**Example:** To define the attributes `(name string, age int)` the JSON structure would look like this,_
```
[
    {
        name: 'name',
        type: 'STRING'
    },
    {
        name: 'age',
        type: 'INT'
    }
]
```

### Annotations
Defines the annotations of any Siddhi element, as all annotations have somewhat the same structure.
They are defined in as a part of a Siddhi element using the name `annotationList`, 
and have the following JSON structure.
```
["annotation1", "annotation2", ...]
```

_**Example:** To define the annotations `@Async(buffer.size='1024') @PrimaryKey('name','age')` the JSON 
structure would look like this,_
```
["@Async(buffer.size='1024')", "@PrimaryKey('name', 'age')"]
```

**Note - Sources, Sinks & Stores do not come under the general annotation struct as they have a different 
structure and are shown separately**

### Store
**The store annotation is only used for tables and aggregations**. So they are only defined in both table and 
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

### Stream Handler
Streams in queries can have multiple filters and functions, but only one window.
* If the query is of type `window-filter-projection`, then it's window can be placed anywhere in the input stream element section.
* If the query is of type `join`, then it's last element in the input stream element must end with a Window (i.e. If the input stream element has atleast one function or filter).
* If the query is of type `pattern & sequence`, then it's input stream element cannot have any window at all.

The JSON structure (denoted as `streamHandlerList` in other JSON structures) for an 
input stream element Filter/Function/Window in a query is given below,
```
[
    {
        type*: 'FILTER',
        value*: ''
    },
    << and|or >>
    {
        type*: 'FUNCTION|WINDOW',
        value*: {
            function*: '',
            parameters*: ['value1',...],
        }
    },
    ...
]
```

_**Example:** Consider the following `streamHandlerList` of a plain `window-filter-projection` query_
```
from Instream[name == 'Mark']#window.time(10 min)#str:tokenize('<Test>', '<Test Regex>')
select ...
```
The JSON for the above example would look like this,
```
[
    {
        type: 'FILTER',
        value: 'name == \'Mark\''
    },
    {
        type: 'WINDOW',
        value: {
            function: 'time',
            parameters: ['10 min']
        }
    },
    {
        type: 'FUNCTION',
        value: {
            function: 'str:tokenize',
            parameters: ['<Test>', '<Test Regex>']
        }
    }
]
```   

## Stream Definition
```
{
    id*: '',
    name*: '',
    attributeList*: {Attributes JSON Array},
    annotationList: {Annotations JSON Array}
}
```

_**Example:**_
```
@Async(buffer.size="1024")
define stream InStream (name string, age int);
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
            type: 'STRING'
        },
        {
            name: 'age',
            type: 'INT'
        }
    ],
    annotationList: ["@Async(buffer.size='1024')"]
}
```
**Note that if a stream is an inner stream, then it's attributes cannot be defined and it cannot have any annotations. 
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
define table InTable (name string, age int);
```
_The JSON for the above table definition is,_
```
{
    id: 'InTable',
    name: 'InTable',
    attributeList: [
        {
            name: 'name',
            type: 'STRING'
        },
        {
            name: 'age',
            type: 'INT'
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
    outputEventType: ‘current_events|expired_events|all_events’,
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
    select*: {Query Select JSON},
    groupBy: ['value1',...],
    aggregateByAttribute: '',
    aggregateByTimePeriod*: {
        type*: 'RANGE',
        value*: {
            min*: '',
            max*: ''
        }
        << or >>
        type*: 'INTERVAL',
        value*: ['sec', 'min', ...] // Atleast one value must be available
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
        aggregate by timestamp every sec...year;
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
        type: 'RANGE',
        value: {
            min: 'sec',
            max: 'year'
        }
    },
    store: {},
    annotationList: []
}
```

## Function Definition
```
{
    id*: '',
    name*: '',
    scriptType*: 'JAVASCRIPT|R|SCALA',
    returnType*: 'STRING|INT|LONG|DOUBLE|FLOAT|BOOL|OBJECT',
    body*: ''
}
```
_**Example:**_
```
define function concatFn[javascript] return string {
    var str1 = data[0];
    var str2 = data[1];
    var str3 = data[2];
    var responce = str1 + str2 + str3;
    return responce;
};
```
_The JSON for the above function definition is,_
```
{
    id: '',
    name: 'concatFn',
    scriptType: 'JAVASCRIPT',
    returnType: 'STRING',
    body: 'var str1 = data[0];\nvar str2 = data[1];\nvar str3 = data[2];\nvar responce = str1 + str2 + str3;\nreturn responce;'
}
```

## Source And Sink Definition
```
{
    id*: ‘’,
    annotationType*: 'SINK|SOURCE',
    connectedElementName*: '',
    type*: ‘’,
    options: ['option1', 'option2=value2',...],
    map: {
        type*: ‘’,
        options: {Key-Value Pair JSON},
        attributes: {
            type*: ‘MAP’
            value*: {Key-Value Pair JSON}
        }
        << or >>
        attributes: {
            type*: ‘LIST’
            value*: ['value1',...]
        }
    }
}
```
**IMPORTANT**
* Map attributes for sources are either key-value pair JSON or list (no single), but the map attributes for sinks are either key-value pair JSON or single (no list).
* If the `annotationType` is _SOURCE_ then the map attributes are denoted using `@attributes(...)`, but if the definitionType is _SINK_ then the map attributes are defined as `@payload(...)` in Siddhi.

_**Example 1 (Source):**_
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
    annotationType: 'SOURCE',
    connectedElementName: '<StreamName||TriggerName>',
    type: 'http',
    options: ["receiver.url = 'http://localhost:8006/productionStream'", "basic.auth.enabled = 'false'"],
    map: {
        type: 'json',
        options: [],
        attributes: []
    },
}
```                   

_**Example 2 (Sink):**_
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
    annotationType: 'SINK',
    connectedElementName: '<StreamName||TriggerName>',
    type: 'http',
    options: ["publisher.url = 'http://localhost:8005/endpoint'", "method = 'POST'", "headers = 'Accept-Date:20/02/2017'", "basic.auth.username = 'admin'", "basic.auth.password = 'admin'", "basic.auth.enabled = 'true'"],
    map: {
        type: 'json',
        options: [],
        attributes: []
    }
}
```

## Query Definition
All queries have the following JSON body structure
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
    limit: <long>, 
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
Note that for this type there are a few conditions for each type even though they have the same JSON structure:
* If the `type` is `window`, then the query must have a window and can have an optional filter.
* If the `type` is `filter`, then the query must have a filter and no window.
* If the `type` is `projection`, then the query cannot have a filter or window.
```
{
    type*: 'window|filter|projection',
    from*: '',
    streamHandlerList: {Stream Handler JSON}
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
    type: 'window',
    from: 'InputStream',
    streamHandlerList: [
        {
            type: 'FILTER',
            value: 'age >= 18'
        },
        {
            type: 'WINDOW',
            value: {
                function: 'time',
                parameters: ['1 hour']
            }
        },
        {
            type: 'FILTER',
            value: 'age < 30'
        }
    ]
}
```

**Please note that even triggers can be used in window-filter-projection queries as they are treated as
 streams underneath in the siddhi runtime**

#### <a name="join">JSON Structure for `Join` query input type:</a>
A `join` query can be one of 4 types:
* Join Stream
* Join Table
* Join Aggregation
* Join Window
* Join Trigger

The way to identify a join query type is by using the `joinWith` attribute. 
However all 4 of these join types will be defined using the same JSON structure.
```
{
    type*: 'join',
    joinWith*: 'stream|table|window|aggregation|trigger',
    left*: {Join Element JSON},
    joinType*: 'join|left_outer|right_outer|full_outer',
    right*: {Join Element JSON},
    on: '',
    within: '', // If joinWith == aggregation
    per: '' // If joinWith == aggregation
}
```
The `Join Element JSON` has the following structure:
```
{
    type*: 'stream|table|window|aggregation|trigger',
    from*: '',
    streamHandlerList: {Stream Handler JSON} // If there is a filter, there must be a window for joins (the only exception is when type = window).
    as: '',
    isUnidirectional: true|false // Only one 'isUnidirectional' value can be true at a time (either left definition|right definition|none)
}
```
There are a few conditions that must be met for a join query input to be a valid one:
* At least one, `left` or `right` JSON value must be of **stream or trigger type**, or else it is not a valid join query input.
* If a `Join element JSON` is of type `window`, then that element's window attribute must be null. This is because a window definition cannot have another window within it.
* If there is a `Join Element JSON` of type `aggregation`, then the `within` and `per` attributes in the JSON structure cannot be null. If there is no aggregation definition, then those attributes have to be null.
* If a `Join Element JSON` has a `filter`, then it must have a window as well or else it is invalid (except if that element is of type `window` definition).
* Only one `Join Element JSON` can be marked as `isUnderectional: true`. It is either the left definition, right definition or neither of them.

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
        type: 'stream',
        from: 'StockStream',
        streamHandlerList: [],
        as: 'S',
        isUnidirectional: false
    },
    joinType: 'join',
    right: {
        type: 'aggregation',
        from: 'TradeAggregation',
        streamHandlerList: [],
        as: 'T',
        isUnidirectional: false
    },
    on: 'S.symbol == T.symbol',
    within: '\"2014-02-15 00:00:00 +05:30\", \"2014-03-16 00:00:00 +05:30\"',
    per: '\"days\"'
}
```

#### <a name="pattern-sequence">JSON Structure for `Pattern` & `Sequence` query input types:</a>
The JSON structure for both patterns & sequences are identical:
```
{
    type*: 'pattern|sequence',
    conditionList*: [
        {
            conditionId*: '',
            streamName*: '',
            streamHandlerList: {Stream Handler JSON}
        },
        ...
    ],
    logic*: ''
}
```

_**Example**_
```
from every event1=InStream[age < 100]<21:234> within 10 min ->
    every event2=InStream[age > 30] and event3=InStream[age < 50]->
    every not InStream[age >= 18] for 5 sec ->
    every not InStream[age < 18] and event6=InStream[age > 30]
select ...
```
_The above pattern input is defined by the following JSON structure:_
```
{
    type: 'pattern',
    conditionList: [
        {
            conditionId: 'event1',
            streamName: 'InStream',
            streamHandlerList: [
                {
                    type: 'FILTER',
                    value: 'age < 100'
                }
            ]
        },
        {
            conditionId: 'event2',
            streamName: 'InStream',
            streamHandlerList: [
                {
                    type: 'FILTER',
                    value: 'age > 30'
                }
            ]
        },
        {
            conditionId: 'event3',
            streamName: 'InStream',
            streamHandlerList: [
                {
                    type: 'FILTER',
                    value: 'age < 50'
                }
            ]
        },
        {
            conditionId: 'event4',
            streamName: 'InStream',
            streamHandlerList: [
                {
                    type: 'FILTER',
                    value: 'age >= 18'
                }
            ]
        },
        {
            conditionId: 'event5',
            streamName: 'InStream',
            streamHandlerList: [
                {
                    type: 'FILTER',
                    value: 'age < 18'
                }
            ]
        },
        {
            conditionId: 'event6',
            streamName: 'InStream',
            streamHandlerList: [
                {
                    type: 'FILTER',
                    value: 'age > 30'
                }
            ]
        }
    ],
    logic: 'every event1<21:234> within 10 min -> every event2 and event3 -> every not event4 for 5 sec -> every not event5 and event6'
}
```
**Note - If their is a `not` statement before a `conditionId` in the `logic` attribute, then that `conditionId` will not be displayed in the _source view_.**
_For an example:_
```
{
    type: 'sequence',
    conditionList: [
        {
            conditionId: 'e1',
            streamName*: 'InStream',
            streamHandlerList: [
                {
                    type: 'FILTER',
                    value: 'age >= 18'
                }
            ]
        },
        {
            conditionId: 'e2',
            streamName*: 'InStream',
            streamHandlerList: [
                {
                    type: 'FILTER',
                    value: 'age < 30'
                }
            ]
        }
    ],
    logic: 'every e1+ within 10 min, not e2 for 10 min'
}
```
The siddhi code for the above JSON would look like this:
```
from 
    every e1=InStream[age >= 18], 
    not InStream[age < 30] for 10 min
select ...
```
Notice that the second sequence `not InStream[age < 30] for 10 min` does not have the phrase 
`"e2="` reference like `e1` does because it has a `not` statement before it.

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
    eventType: 'current_events|expired_events|all_events'
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
    eventType: 'current_events|expired_events|all_events',
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
        eventType: 'all',
        on: 'RoomTypeTable.roomNo == roomNumber'
    },
    target: 'RoomTypeTable',
}
```

#### <a name="update">JSON structure for the `update` query output type:</a>
```
{
    eventType: 'current_events|expired_events|all_events',
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
        eventType: '',
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
    eventType: 'current_events|expired_events|all_events',
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
        eventType: '',
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
    queryLists: [
        {
            '<queryType>': [{Query JSON},...]
        },
        ...
    ],
    innerStreamList: [{Stream Definition JSON},...],
    partitionWith*: [
        {
            streamName*: '',
            expression*: ''
        },
        ..
    ],
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
    queryLists: [
        {
            '<queryType>': [{Query JSON},...]
        },
        ...
    ],
    streamList: [{#OutputStream JSON}],
    partitionWith: [
        {
            expression: 'roomNo >= 1030 as \'serverRoom\' or roomNo < 1030 and roomNo >= 330 as \'officeRoom\' or roomNo < 330 as \'lobby\''
            streamName: 'TempStream',
        }
    ],
    annotationList: ['@info(name=\'TestPartition\')']
}
```
