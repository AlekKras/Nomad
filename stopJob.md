So far we've created, run and modified a job. The final step in a job lifecycle is stopping the job. This is done with the stop command:

<code>nomad stop example</code>

<code>==> Monitoring evaluation "ddc4eb7d"
  Evaluation triggered by job "example"
  Evaluation within deployment: "ec46fb3b"
  Evaluation status changed: "pending" -> "complete"
==> Evaluation "ddc4eb7d" finished with status "complete"</code>

When we stop a job, it creates an evaluation which is used to stop all the existing allocations. If we now query the job status, we can see it is now marked as dead (stopped), indicating that the job has been stopped and Nomad is no longer running it:

<code>nomad status example</code>

We can get the desired output with jobs stopped
