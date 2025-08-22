CONTRIBUTIONS

TODO: write a brief summary of how each team member contributed to
the project.

REPORT

Our computational approach is to recursively split the array and delegate these subarrays to child processes using fork(), continuing until the subarray size is less than or equal to the threshold. These leaf processes at the bottom of the recursion then perform a sequential sort using qsort. Afterward, the sorted results from the child processes are returned to their parent process, which then merges them. In this manner, the results are propagated layer by layer up to the top. Processes at the same level of this hierarchy execute concurrently, which is the essence of parallelism, ultimately accomplishing a parallel merge sort of the initial large array.

Running Results:

Test run with threshold 2097152
real    0m0.330s
user    0m0.273s
sys     0m0.031s

Test run with threshold 1048576
real    0m0.212s
user    0m0.285s
sys     0m0.064s
Test run with threshold 524288

real    0m0.156s
user    0m0.314s
sys     0m0.089s
Test run with threshold 262144

real    0m0.121s
user    0m0.315s
sys     0m0.098s
Test run with threshold 131072

real    0m0.120s
user    0m0.418s
sys     0m0.086s
Test run with threshold 65536

real    0m0.123s
user    0m0.395s
sys     0m0.201s
Test run with threshold 32768

real    0m0.127s
user    0m0.452s
sys     0m0.244s

Test run with threshold 16384
real    0m0.139s
user    0m0.475s
sys     0m0.385s

Firstly,we all know that 'real' means the total wall-clock time elapsed from the program's start to its finish which is is the measure of performance. 'user' means that the total CPU time spent executing the program's code in user mode, and "sys" means that the total CPU time spent in kernel mode(includes the time that the system call consume).

When threshold=2097152(it means the process will sort by complete sequential sort),the "sys" is 0.015s.This is the typical single-process computation,because it spends very little time on system calls

As the threshold was halved to 1,048,576, the real time dropped dramatically from 0.332s to 0.205s.It cut the time by a third! it shows that the power of Parallel computing emerged!
At the same time, the 'sys' increased significantly, reflecting the overhead incurred by the system calls

Next, the trend for 'real' in the overall experiment results is a decrease first, reaching its minimum value when the threshold equals 131072, and then slowly increases. Meanwhile, the trend for 'sys' continuously increases.

This shows that the optimization effect of parallel computing is limited.

As for why 'real' slowly increases, my guess is that as the threshold is repeatedly halved, The amount of  processes will grow exponentially. This eventually causes the CPU to spend a large amount of resources on the system management of these child processes. In the end, we can even see that 'sys' reaches a surprising 0.749s! In contrast, the resources the CPU spends on the actual sorting calculations become less, which ultimately leads to an increase of  'real'.