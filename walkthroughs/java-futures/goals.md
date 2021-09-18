
Concept 
    Concurrent vs parallel

#### Parallel work-intensive
* Each CPU/Thread would be fully utilized
* CPI intensive tasks --> parallel up to the cpu capacity (count) of the server
* spread tasks across cpu's
* assumes the process is splittable into sub-tasks that can be done in parallel
* depending on the type - some lag between - could use pool of threads
* 

#### Concurrent
* We are waiting for a task to finish - not calculating anything
* Goin parallel does not make sense because each thread is just sending one request and is then stuck waiting
* We want to be able to send the task, then move on, doing other important work until the 'server' has our answer.


#### Async server services
* Not too common in a microservice environment because it requires the client and server to share memory.  The service immediately returns a "future" and then goes about doing the work leaving the client to do other stuff.
See this in some async SDK's (AWS) that return a future
