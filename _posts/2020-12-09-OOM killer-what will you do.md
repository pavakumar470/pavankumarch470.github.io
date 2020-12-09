---
published: false
---
when ever there is a process running the best practice is to the amount of memroy it would require but most of the times. But most of the times programmers might request more memory than it would require which will make machine run out of memroy and will make the oom killer to kill the process which is holding more amount of memroy. and the process will be killed on the oom_score.<br>
<br/><br/>
for example when we are running the below program/application it will try to get all the memroy which is available:<br/>
```java
#include <stdio.h>
 #include <stdlib.h>

 int main (void) {  
         int n = 0;  

         while (1) {  
                 if (malloc(1<<20) == NULL) {  
                         printf("malloc failure after %d MiB\n", n);  
                         return 0;  
                 }  
                 printf ("got %d MiB\n", ++n);  
         }  
 }  
```
<br/>

when we run the above application it will try to grab all the memory that is available and once the system has no more memory OOM killer will come into picture and it will try to kill the application which is occupying huge memory.<br/>

you see the below error where it has killed the a.out process as the system is running out of memory:<br/>
```java
kernel: Out of memory: Kill process 19841 (a.out) score 661 or sacrifice child
kernel: Killed process 19841 (a.out) total-vm:4292749476kB, anon-rss:16767120kB, file-rss:80kB, shmem-rss:0kB
```
<br/>
but it is not acceptable that every time one or the other process will use lot of memory and some of the process will be killed based on badness score.<br/>

So if you are running the applications on the Linuxthen we can limit the amout of memory that is being used by using the "overcommit_memory" and "overcommit_ratio" values in the linux.<br/>

where we set the below values:<br/>
```java
echo 2 > /proc/sys/vm/overcommit_memory
echo 75 > /proc/sys/vm/overcommit_ratio
```
<br/>
with the below formula we can get the memory limit that is being set on the machine:<br/>
```java
Memory Allocation Limit = Swap Space + RAM * (Overcommit Ratio / 100)
```