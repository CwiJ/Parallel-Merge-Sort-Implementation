# Parallel-Merge-Sort-Implementation

## This report details a parallel merge sort in C using memory-mapped I/O and a fork/join model. The program sorts 64-bit integers from a file. Performance analysis reveals significant speedup with increased parallelism, but also shows that excessive process management overhead eventually degrades performance.

## 1. Project Introduction
The goal of this project was to implement an efficient, in-place parallel sorting algorithm for large data files. The chosen approach was a merge sort, a divide-and-conquer algorithm well-suited for parallelization.

To achieve high performance and handle large files that might not fit comfortably in RAM, two core Unix features were employed:

Memory-Mapped I/O (mmap): Instead of manually reading the file into memory buffers, the file was mapped directly into the process's address space. This provides a seamless and efficient way to manipulate file data as if it were a standard array. A key benefit is that this mapping is inherited by all child processes, allowing them to work on the same data concurrently without extra setup.

Process-Based Parallelism (fork): The "divide" step of the merge sort was parallelized using the fork() system call. This creates a tree of processes where each node is responsible for sorting a specific segment of the array.

## 2. Implementation Strategy
Our computational approach is to recursively split the array and delegate these subarrays to child processes using fork(), continuing until the subarray size is less than or equal to the threshold. These leaf processes at the bottom of the recursion then perform a sequential sort using qsort. Afterward, the sorted results from the child processes are returned to their parent process, which then merges them. In this manner, the results are propagated layer by layer up to the top. Processes at the same level of this hierarchy execute concurrently, which is the essence of parallelism, ultimately accomplishing a parallel merge sort of the initial large array.

## 3. Experimental Results
The program was tested on a 16MB file of random 64-bit integers. The threshold for switching to a sequential sort was varied to control the degree of parallelism. The time utility was used to measure performance.

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

## 4. Analysis
The performance metrics are defined as follows:

real: The total wall-clock time, which is the primary measure of performance.

user: The total CPU time spent executing the program's code in user mode, summed across all processes.

sys: The total CPU time spent in kernel mode performing system calls (e.g., fork()).

The initial run with a threshold of 2,097,152 serves as our sequential baseline. The low sys time (0.031s) is typical for a single-process application.

As the threshold was halved to 1,048,576, initiating parallel execution, the real time dropped dramatically from 0.330s to 0.212s. This significant performance gain demonstrates the power of parallel processing. Concurrently, the sys time increased, reflecting the overhead from the system calls required to manage the new child processes.

The real time continued to decrease, reaching an optimal point of 0.120s at a threshold of 131,072. Beyond this, further decreasing the threshold caused the real time to plateau and eventually increase. This illustrates the law of diminishing returns.

The reason for this performance degradation is evident in the sys time, which grew continuously. As the threshold is repeatedly halved, the number of processes grows exponentially. This eventually causes the CPU and operating system to spend a large amount of resources on the management of these child processes (known as context switching). At the lowest threshold, the sys time of 0.385s shows that system overhead has become the dominant factor, negating the benefits of further parallelization and leading to an increase in real time.

## 5. Conclusion
This project successfully implemented a parallel merge sort algorithm. The experimental results confirm that a fork/join model can significantly reduce the total execution time for a computationally intensive task. However, the analysis also clearly shows that there is a critical trade-off between the degree of parallelism and the overhead of process management. Excessive parallelization leads to a state where system overhead outweighs computational gains, ultimately degrading overall performance.
