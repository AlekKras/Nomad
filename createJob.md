Running a Job
To get started, we are going to use the example file example.nomad which you can see open in the editor window.

In this example job file, we have declared a single task 'redis' which is using the Docker driver to run the task. The primary way you interact with Nomad is with the run command. The run command takes a job file and registers it with Nomad. This is used both to register new jobs and to update existing jobs.

We can register our example job now:

<code> nomad run example.nomad </code>

We would get something like this:

<code>==> Monitoring evaluation "26cfc69e"
  Evaluation triggered by job "example"
  Allocation "8ba85cef" created: node "171a583b", group "cache"
  Evaluation status changed: "pending" -> "complete"
==> Evaluation "26cfc69e" finished with status "complete"</code>

Anytime a job is updated, Nomad creates an evaluation to determine what actions need to take place. In this case, because this is a new job, Nomad has determined that an allocation should be created and has scheduled it on our local agent.

To inspect the status of our job we use the status command:

<code> nomad status example </code>

and can get something like this:

<code>
ID            = example
Name          = example
Submit Date   = 04/02/18 19:14:43 UTC
Type          = service
Priority      = 50
Datacenters   = dc1
Status        = running
Periodic      = false
Parameterized = false

Summary
Task Group  Queued  Starting  Running  Failed  Complete  Lost
cache       0       0         1        0       0         0

Latest Deployment
ID          = 11c5cdc8
Status      = successful
Description = Deployment completed successfully

Deployed
Task Group  Desired  Placed  Healthy  Unhealthy
cache       1        1       1        0

Allocations
ID        Node ID   Task Group  Version  Desired  Status   Created At
8ba85cef  171a583b  cache       0        run      running  04/02/17 19:14:43 UTC
</code>

Here we can see that the result of our evaluation was the creation of an allocation that is now running on the local node.

An allocation represents an instance of Task Group placed on a node. To inspect an allocation we use the alloc-status command:

<code>
$ nomad alloc-status 8ba85cef
ID                  = 8ba85cef
Eval ID             = 61b0b423
Name                = example.cache[0]
Node ID             = 171a583b
Job ID              = example
Job Version         = 0
Client Status       = running
Client Description  = <none>
Desired Status      = run
Desired Description = <none>
Created At          = 04/02/19 19:14:43 UTC
Deployment ID       = fa882a5b
Deployment Health   = healthy

Task "redis" is "running"
Task Resources
CPU    Memory           Disk     IOPS  Addresses
2/500  6.3 MiB/256 MiB  300 MiB  0     db: 127.0.0.1:30329

Recent Events:
Time                   Type      Description
04/02/17 19:14:53 UTC  Started     Task started by client
04/02/17 19:14:43 UTC  Driver      Downloading image redis:3.2
04/02/17 19:14:43 UTC  Task Setup  Building Task Directory
04/02/17 19:14:43 UTC  Received    Task received by client
</code>

We can see that Nomad reports the state of the allocation as well as its current resource usage. By supplying the -stats flag, more detailed resource usage statistics will be reported.

To see the logs of a task, we can use the logs command:

<code>
$ nomad logs 8ba85cef redis
               _._
          _.-``__ ''-._
     _.-``    `.  `_.  ''-._           Redis 3.2.1 (00000000/0) 64 bit
 .-`` .-```.  ```\/    _.,_ ''-._
(    '      ,       .-`  | `,    )     Running in standalone mode
|`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
|    `-._   `._    /     _.-'    |     PID: 1
 `-._    `-._  `-./  _.-'    _.-'
|`-._`-._    `-.__.-'    _.-'_.-'|
|    `-._`-._        _.-'_.-'    |           http://redis.io
 `-._    `-._`-.__.-'_.-'    _.-'
|`-._`-._    `-.__.-'    _.-'_.-'|
|    `-._`-._        _.-'_.-'    |
 `-._    `-._`-.__.-'_.-'    _.-'
     `-._    `-.__.-'    _.-'
         `-._        _.-'
             `-.__.-'
...
</code>



