# Distributed Algorithms, Chapter 5

## Coordinated Attack Problem

The approach of proof is common. You might have seen a similar one before. However, you should review the assumption of the problem. I guess you misunderstand it at the first time.

+ The link might be fail. And there is no guarantee that it will eventually recover.
+ However, the discision must eventually made.
>> That means, if there is a call like wait_until_a_message_come() must be wrong. Because it probably never returns, and not get dicision. A run can be constructed by "all message after the r rounds are lost"
+ The assumption has the concession that 
>> If there is a message lost, both of p1 and p2 can have the decision 0 even if both of you initialized as 1. Yet p1 and p2 still have to give the same decision.
+ Another thing you have to know is that you do not know by yourself whether your message is successfully delivered or not.

A possible algorithm should be some sentences like that:
>> recv(msg) ->
>>   msg of pattern X ->
>>   msg of pattern Y ->
>>   no msg received or msg of dummy or else ->
>> end
Well, I have to say it is a pratical pattern in Erlang.
** You should have the action based on the received message. **
** You should _not_ wait the give pattern message to be received. **

