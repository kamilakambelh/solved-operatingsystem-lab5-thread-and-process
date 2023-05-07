Download Link: https://assignmentchef.com/product/solved-operatingsystem-lab5-thread-and-process
<br>
Goal: This lab helps student to distinguish the differences between thread and process. What is a thread?

Content In detail, this lab requires student practice with examples such as creating a multithread program, showing the memory region of threads.

<ul>

 <li>Write a multithread program</li>

 <li>Solve an example which can run in parallel using thread</li>

 <li>Show the differences between process and thread in term of memory region</li>

</ul>

Result After doing this lab, student can understand the difference of thread and process. Student can write a program with multiple thread created.

Requirement          Student need to review the theory of thread.

<h1>1 Stack</h1>

Stack is one of the most important memory region of a process. It is used to store temporary data used by the process (or thread). The name “stack” is used to described the way data put and retrieved from this region which is identical to stack data structure: The last item pushed to the stack is the first one to be removed (popped).

Stack organization makes it suitable from handling function calls. Each time a function is called, it gets a new stack frame. This is an area of memory which usually contains, at a minimum, the address to return when it complete, the input arguments to the function and space for local variables.

In Linux, stack starts at a high address in memory and grows down to increase its size. Each time a new function is called, the process will create a new stack frame for this function. This frame will be place right after that of its caller. When the function returns, this frame is clean from memory by shrinking the stack (stack pointer goes up). The following program illustrates to identify the relative location of stack frames create by nested function calls.

<em>Example: Trace function calls</em>

<table width="564">

 <tbody>

  <tr>

   <td width="564">#include &lt;stdio .h&gt; void func (unsigned long number) { unsigned long local_f = number;printf (“%2lu␣−−&gt;␣%p
” , local_f , &amp;local_f );local_f−−;if (local_f &gt; 0) { func(local_f );}}int main() { func(10);}</td>

  </tr>

 </tbody>

</table>

1

2

3

4

5

6

7

8

9

10

11

12

Similar to heap, stack has a pointer name stack pointer (as heap has program break) which indicate the top of the stack. To change stack size, we must modify the value of this pointer. Usually, the value of stack pointer is hold by stack pointer register inside the processor. Stack space is limited, we cannot extend the stack exceed a given size. If we do so, stack overflow will occurs and crash our program. To identify the default stack size, use the following command

<table width="564">

 <tbody>

  <tr>

   <td width="564">ulimit −s</td>

  </tr>

 </tbody>

</table>

1

Different from heap, data of stack are automatically allocated and cleaned through procedure invocation termination, Therefore, in C programming, we do not need to allocate and free local variables. In Linux, a process is permitted to have multiple stack regions. Each regions belongs to a thread.

<h1>2 Introduction to thread</h1>

A thread is a basic unit of CPU utilization; it comprises a thread ID, a program counter, a register set, and a stack. It shares with other threads belonging to the same process its code section, data section, and other operating-system resources, such as open files and signals. A traditional (or heavyweight) process has a single thread of control. If a process has multiple threads of control, it can perform more than one task at a time.

Figure 2.1: Single-threaded and multithreaded processes.

Figure 2.1 illustrates the difference between a traditional single-threaded process and a multithreaded process. The benefits of multithreaded programming can be broken down into four major categories:

<ul>

 <li>Responsiveness</li>

 <li>Resource sharing</li>

 <li>Economy</li>

 <li>Scalability</li>

</ul>

Q1. What resources are used when a thread is created? How do they differ from those used when a process is created?

Multicore programming Earlier in the history of computer design, in response to the need for more computing performance, single-CPU systems evolved into multi-CPU systems. A more recent, similar trend in system design is to place multiple computing cores on a single chip. Each core appears as a separate processor to the operating system.

Whether the cores appear across CPU chips or within CPU chips, we call these systems multicore or multiprocessor systems. Multithreaded programming provides a mechanism for more efficient use of these multiple computing cores and improved concurrency.

Figure 2.2: Parallel execution on a multicore system.

On a system with multiple cores, however, concurrency means that the threads can run in parallel, because the system can assign a separate thread to each core, as Figure 2.2 shown.

Q2. Is it possible to have concurrency but not parallelism? Explain.

<ul>

 <li>How to create multiple threads?</li>

</ul>

3.1 Thread libraries

A thread library provides the programmer with an API for creating and managing threads. There are two primary ways of implementing a thread library. Three main thread libraries are in use today: POSIX Pthreads, Windows, and Java. In this lab, we use POSIX Pthread on Linux and Mac OS to practice with multithreading programming.

Creating threads

pthread_create (thread , attr , start_routine , arg)

Initially, your main() program comprises a single, default thread. All other threads must be explicitly created by the programmer.

<ul>

 <li>thread: An opaque, unique identifier for the new thread returned by the subroutine.</li>

 <li>attr: An opaque attribute object that may be used to set thread attributes. You can specify a thread attributes object, or NULL for the default values.</li>

 <li>start: the C routine that the thread will execute once it is created.</li>

 <li>arg: A single argument that may be passed to textttstart_routine. It must be passed by reference as a pointer cast of type void. NULL may be used if no argument is to be passed.</li>

</ul>

<em>Example: Pthread Creation and Termination</em>

<table width="564">

 <tbody>

  <tr>

   <td width="564">#include &lt;pthread.h&gt;#include &lt;stdio .h&gt;#define NUM_THREADS 5void ∗PrintHello(void ∗threadid){ long tid ;tid = (long)threadid ; printf (“Hello␣World!␣It ’s␣me,␣thread␣#%ld!
” , tid );pthread_exit(NULL);}int main (int argc , char ∗argv []){ pthread_t threads [NUM_THREADS]; int rc ; long t ;for(t=0; t&lt;NUM_THREADS; t++){ printf (“In␣main:␣creating␣thread␣%ld
” , t ); rc = pthread_create(&amp;threads [ t ] ,NULL, PrintHello , (void ∗)t ); if (rc){ printf (“ERROR;␣return␣from␣pthread_create()␣is␣%d
” , rc ); exit(−1);}}<em>/</em>∗ <em>Last thing that main() should do </em>∗<em>/ </em>pthread_exit(NULL);}</td>

  </tr>

 </tbody>

</table>

1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

Passing argument to Thread We can pass a structure to each thread such as the example below. Using the previous example to implement this example:

<table width="564">

 <tbody>

  <tr>

   <td width="564">struct thread_data{ int thread_id; int sum;char ∗message;};struct thread_data thread_data_array[NUM_THREADS];void ∗PrintHello(void ∗thread_arg){</td>

  </tr>

 </tbody>

</table>

1

2

3

4

5

6

7

8

9

10

<table width="564">

 <tbody>

  <tr>

   <td width="564">struct thread_data ∗my_data;. . .my_data = (struct thread_data ∗) thread_arg;taskid = my_data−&gt;thread_id;sum = my_data−&gt;sum; hello_msg = my_data−&gt;message;. . .}int main (int argc , char ∗argv []){. . .thread_data_array[ t ]. thread_id = t ; thread_data_array[ t ].sum = sum; thread_data_array[ t ]. message = messages[ t ];rc = pthread_create(&amp;threads [ t ] , NULL, PrintHello ,(void ∗) &amp;thread_data_array[ t ]);. . .}</td>

  </tr>

 </tbody>

</table>

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

Joining and Detaching Threads “Joining” is one way to accomplish synchronization between threads, For example:

Figure 3.1: Joining threads.

<ul>

 <li>The pthread_join() subroutine blocks the calling thread until the specified threadid thread terminates.</li>

 <li>The programmer is able to obtain the target thread’s termination return status if it was specified in the target thread’s call to pthread_exit().</li>

 <li>A joining thread can match one pthread_join() call. It is a logical error to attempt multiple joins on the same thread.</li>

</ul>

3.2 Multithread programming

Problem:    Constructing a multithreaded program that calculates the summation of a non-negative integer in a separate thread.

<table width="564">

 <tbody>

  <tr>

   <td width="564">#include &lt;pthread.h&gt;#include &lt;stdio .h&gt;int sum; <em>/</em>∗ <em>this data is shared by the thread(s) </em>∗<em>/</em>void ∗runner(void ∗param); <em>/</em>∗ <em>threads                            call           this function </em>∗<em>/</em>int main(int argc , char ∗argv []){ pthread_t tid ; <em>/</em>∗ <em>the thread identifier </em>∗<em>/ </em>pthread_attr_t attr ; <em>/</em>∗ <em>set of thread attributes </em>∗<em>/</em>if (argc != 2) {fprintf (stderr ,”usage:␣a.out␣&lt;integer␣value&gt;
”);return −1;}if (atoi(argv [1]) &lt; 0) { fprintf (stderr ,”%d␣must␣be␣&gt;=␣0
” , atoi(argv [1]));return −1;}<em>/</em>∗ <em>get the default attributes </em>∗<em>/ </em>pthread_attr_init(&amp;attr ); <em>/</em>∗ <em>create the thread </em>∗<em>/ </em>pthread_create(&amp;tid , &amp;attr , runner , argv [1]);<em>/</em>∗ <em>wait for the thread to exit </em>∗<em>/ </em>pthread join(tid ,NULL);printf (“sum␣=␣%d
” ,sum);}<em>/</em>∗ <em>The thread will begin control in this function </em>∗<em>/ </em>void ∗runner(void ∗param){int i , upper = atoi(param);sum = 0; for ( i = 1; i &lt;= upper; i++)sum += i ;pthread exit (0);}</td>

  </tr>

 </tbody>

</table>

1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

36

37

38

39

<h1>4 Exercise (Required)</h1>

Problem 1 (5 points) An interesting way of calculating pi is to use a technique known as Monte Carlo, which involves randomization. This technique works as follows: Suppose you have a circle inscribed within a square, as shown in Figure 4.1.

Figure 4.1: Monte Carlo technique for calculating pi.

(Assume that the radius of this circle is 1.) First, generate a series of random points as simple (x, y) coordinates. These points must fall within the Cartesian coordinates that bound the square. Of the total number of random points that are generated, some will occur within the circle. Next, estimate pi by performing the following calculation:

pi = 4 x (number of points in circle ) / (total number of points)

As a general rule, the greater the number of points, the closer the approximation to pi. However, if we generate too many points, this will take a very long time to perform our approximation. Solution for this problem is to carry out point generation and calculation concurrently. Suppose the number of points to be generated is <em>nPoints</em>. We create <em>N </em>separate threads and have each thread to create only <em>nPoints / N </em>points and count the number of points fall into the circle. After all threads have done their job we then get the total number of points inside the circle by combining the results from each thread. Since the total number of points has been generated equal to <em>nPoint</em>, the results of this method is equivalent to that of running a single process program. Furthermore, as threads run concurrently and the number of points each thread has to handle is much fewer than that of a singe process program, we can save a lot of time.

Write two programs implementing algorithm describe above (one serial version and one multi-thread version). The program takes the number of points to be generated from user then creates multiple threads to approximate pi. Put all of your code in two files named <em>“pi_serial.c” </em>and <em>“pi_multi-thread.c”</em>. The number of points is passed to your program as an input parameter. For example, if your executable file is <em>pi </em>then to have your program calculate pi by generating one million points, we will use the follows command:

./ pi_serial 1000000

./pi_multi−thread 1000000

Requirement: The multi-thread version must have some speed-up compared to the serial version. There are at least 2 targets in the Makefile <em>pi_serial </em>and <em>pi_multi-thread </em>to compile the two program.

Problem 2 (5 points)             Given the content of a C source file named <em>“code.c”</em>

<table width="564">

 <tbody>

  <tr>

   <td width="564">#include &lt;stdio .h&gt;#include &lt;pthread.h&gt; void ∗ hello(void ∗ tid) { printf (“Hello␣from␣thread␣%d
” , (int)tid );}int main() { pthread_t tid [10]; int i ;for ( i = 0; i &lt; 10; i++) {pthread_create(&amp;tid [ i ] , NULL, hello , (void∗) i );} pthread_exit(NULL)}</td>

  </tr>

 </tbody>

</table>

1

2

3

4

5

6

7

8

9

10

11

12

13

Since threads run concurrently, the output produced by threads will appear randomly and in an unpredictable way. Modify this program to have those threads to print their thread ID in ascending order every time we run the program as follows:

<table width="564">

 <tbody>

  <tr>

   <td width="564"><em>## ./code</em>Hello from thread 0Hello from thread 1Hello from thread 2Hello from thread 3Hello from thread 4Hello from thread 5Hello from thread 6Hello from thread 7Hello from thread 8Hello from thread 9</td>

  </tr>

 </tbody>

</table>

1

2

3

4

5

6

7

8

9

10

11

Write a Makefile to compile the program. The executable file name is <em>code</em>.

Please remember to put all of your source code in a ZIP file. You must submit your ZIP file to E-learning.

<h1>Revision History</h1>

<table width="371">

 <tbody>

  <tr>

   <td width="79">Revision</td>

   <td width="68">Date</td>

   <td width="114">Author(s)</td>

   <td width="111">Description</td>

  </tr>

  <tr>

   <td width="79">1.0</td>

   <td width="68">22.09.16</td>

   <td width="114">Phuong-DuyNguyen, DucHai Nguyen, Minh ThanhChung</td>

   <td width="111">created</td>

  </tr>

  <tr>

   <td width="79">2.0</td>

   <td width="68">10.03.19</td>

   <td width="114">TK Pham</td>

   <td width="111">Change Exercises</td>

  </tr>

 </tbody>

</table>


