# Queue in BEAM

## Why queue?
There are many queues in the BEAM. As the most common concurrent datastructure, we need analyse the its specific character in it.

## Characters

+ Multi producer but single consumer
Message queue: messages to a single process comes from many other process, but only the destinate process can receive/select the message.

Run queue of scheduler: processes from other schedulers can be inserted into a specific run queue, but only this scheduler can select one to run.

There are differences between these two: I think the most important one is that the messages should keep order.

Other queues: delayed deallocation queue

+ lock free
The scheduling of processes and the communiating by messages are widely used in Erlang. The insert fail path is so short, you hardly catch the sense of punishment of the insert failure(compare with an aborted transaction in DBMS).

+ combined with managed process progress, no ref count
Without a the managed processes, you may hesitate when to free an element. There might be an element you think you can free an consumed element. However, a producer may desperately trying to append his production to this obsoleted element. This occassion can be avoided by set a mark on the current consumed element, hold it until "two day later"

## impelementation
1. header and tail locate in two seperate cache line
2. use marker/sentinel to simplify the logic of the lock free enqueue and dequeue

## reference
comment in source code of queue