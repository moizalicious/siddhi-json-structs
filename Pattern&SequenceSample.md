# Pattern And Sequence
New proposed JSON design which will be generic for both patterns and sequences:
```
{
    type*: 'pattern|sequence',
    eventList*: [
        {
            eventRefence: '',
            streamName*: '',
            filter: ''
        },
        ...
    ],
    usage*: ''
}
```

**_Example_**
```
from every event1=InStream[age < 100]<21:234> within 10 min ->
    every event2=InStream[age > 30] and event3=InStream[age < 50]->
    every not InStream[age >= 18] for 5 sec ->
    every not InStream[age < 18] and event6=InStream[age > 30]
select ...
```

The above pattern input is defined by the following JSON structure:
```
{
    type: 'pattern',
    eventList: [
        {
            eventRefence: 'event1',
            streamName: 'InStream',
            filter: 'age < 100'
        },
        {
            eventRefence: 'event2',
            streamName: 'InStream',
            filter: 'age > 30'
        },
        {
            eventRefence: 'event3',
            streamName: 'InStream',
            filter: 'age < 50'
        },
        {
            eventRefence: 'event4',
            streamName: 'InStream',
            filter: 'age >= 18'
        },
        {
            eventRefence: 'event5',
            streamName: 'InStream',
            filter: 'age >= 18'
        },
        {
            eventRefence: 'event5',
            streamName: 'InStream',
            filter: 'age >= 18'
        }
    ],
    usage: 'every event1<21:234> within 10 min -> every event2 and event3 -> every not event4 for 5 sec -> every not event5 and event6'
}
```
