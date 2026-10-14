Chapter 7 — CPU Scheduling Analysis

Q1 — FIFO Scheduling

1. Experiment Setup

* Scheduling algorithm: FIFO
* Job lengths: 100, 200, 300
* Command: python3 ./ext/ostep-homework/cpu-sched/scheduler.py -l 100,200,300 -p FIFO -c

2. Predicted Results

Assuming all jobs arrive at time 0 and there is no context-switching overhead:

* Average response time: 133.33
* Average turnaround time: 333.33
* Average wait time: 133.33

3. Actual Results

* Average response time: 133.33
* Average turnaround time: 333.33
* Average wait time: 133.33
* Raw output: data/q1_fifo.txt

4. Analysis

FIFO executes jobs in their arrival order until each job finishes. Job 0 runs from time 0 to 100, Job 1 runs from time 100 to 300, and Job 2 runs from time 300 to 600.

The response times are 0, 100, and 300, respectively. The turnaround times are 100, 300, and 600. Therefore, the average response time is 133.33 and the average turnaround time is 333.33.

FIFO is simple to implement, but short jobs may wait a long time behind longer jobs.

5. Conclusion

The simulator results match the predicted values. This experiment demonstrates how FIFO scheduling can cause longer waiting times for jobs that appear later in the queue.
Q2 — SJF Scheduling

1. Experiment Setup

* Scheduling algorithm: SJF
* Job lengths: 100, 200, 300
* Command: python3 ./ext/ostep-homework/cpu-sched/scheduler.py -l 100,200,300 -p SJF -c

2. Actual Results

* Average response time: 133.33
* Average turnaround time: 333.33
* Average wait time: 133.33
* Raw output: data/q1_sjf.txt

3. Analysis

The SJF algorithm selects the shortest available job first. Since the job lengths are already ordered from shortest to longest (100, 200, 300), the execution order is the same as FIFO for this workload.

Therefore, the average response time is 133.33, the average turnaround time is 333.33, and the average wait time is 133.33.

4. Conclusion

For this workload, SJF and FIFO produce the same results because the jobs are already arranged in increasing order of execution length.