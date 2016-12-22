# SGE jobs that need to wait on other jobs

SGE is a commonly used (and old!) job queuing framework. It does a lot. Here is one of the common, moderately complex use cases: make multiple jobs but have some wait on the others to run.

A poor solution to this problem is to have the BASH script that submits jobs block with `-sync y` on the tasks that need to run first. This works but results in a lot of processes stalling on your local OS. It can also be problematic if the script dies or otherwise is terminated before all jobs are queued.

The better solution is to capture job ids and use `-hold_jid job_id1,job_id2,...` for the jobs that need to block. This puts all jobs on SGE and delegates the task of run order.

## Example
Use these three tasks as the example. The first two do nothing but wait. The third notes that everything is done, but only should run after the others finish.

```bash
#!/bin/bash
# wait_20.sh
sleep 20
echo "Slept 20" >> /home/jfalkner/ouput.txt
```

```bash
#!/bin/bash
# wait_10.sh
sleep 10
echo "Slept 10" >> /home/jfalkner/ouput.txt
```

```bash
#!/bin/bash
# done.sh
echo "Done" >> /home/jfalkner/ouput.txt
```
Here is the final script that launches qsub and has tasks appropriately wait. Notice the awk use to parse the needed job id.

```
#!/bin/bash

JOB1="$(qsub ./wait_30.sh | awk 'match($0,/[0-9]+/){print substr($0, RSTART, RLENGTH)}')"

JOB2="$(qsub ./wait_10.sh | awk 'match($0,/[0-9]+/){print substr($0, RSTART, RLENGTH)}')"

qsub -hold_jid $JOB1,$JOB2 ./done.sh
```

When running this, you'll see that the third job is kept in a hold state while the first two finish.

```
4731959 0.00000 wait_30.sh itg          qw    12/21/2016 20:13:50                                    1
4731960 0.00000 wait_10.sh itg          qw    12/21/2016 20:13:50                                    1
4731961 0.00000 done.sh    itg          hqw   12/21/2016 20:13:50                                    1
```

You'll also find that the output in `/home/jfalkner/ouput.txt` always ends up being the following. Even though the `done.sh` takes no time to run.

```
Slept 10
Slept 30
Done
```
