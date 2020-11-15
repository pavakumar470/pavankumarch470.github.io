---
published: true
---
Jstack is an very important tool in java which helps us in getting the more information on the java process which is progressing very slow or for the thread with no progress.<br/>
<br/>
Below are the few senior  when the java process is not progressing:<br/>
1.when the program is in the infinite loop.<br/>
2.when there is a dead lock between the multiple threads that are launched as part of the program.<br/>
3.when the program is waiting on the join(),wait() functions.<br/>

Analyzing the Jstack in each of the above scenario:<br/>
<br/>
when the program is in the infinite loop:<br/>
if any of the java program is having the loop as "while(true)" this loop will cause in the in-finite loop which will never terminate.
<br/>
then if take the Jstack on the PID we can see that that the thread will be in the runnable state forever with the below where it stack of the JStack it provides the details of the loop:
```java
"main" #1 prio=5 os_prio=0 tid=0x00007f3374009800 nid=0x12bb runnable [0x00007f337a198000]
   java.lang.Thread.State: RUNNABLE
        at loop.Loop.main(Loop.java:5)
```
<br/>
when there is a dead lock between the multiple threads that are launched as part of the program:
when ever the developer write a program they tend to use the multi threading which will help them in achieving the multiple tasks parallelly. Sometimes with this multi-threading there could be a situation where the one thread will be waiting for the other threads lock in this case the program will not do any thing each thread will be waiting for the other thread to release the lock.<br/>
below is the thread dump of such scenario:<br/>
```java
"Thread-1" #10 prio=5 os_prio=0 tid=0x00007febf00e7800 nid=0x758c waiting for monitor entry [0x00007febd7cfb000]
   java.lang.Thread.State: BLOCKED (on object monitor)
        at thread.TestThread$ThreadDemo2.run(TestThread.java:38)
        - waiting to lock <0x000000071945d788> (a java.lang.Object)
        - locked <0x000000071945d798> (a java.lang.Object)

"Thread-0" #9 prio=5 os_prio=0 tid=0x00007febf00e5800 nid=0x758a waiting for monitor entry [0x00007febd7dfc000]
   java.lang.Thread.State: BLOCKED (on object monitor)
        at thread.TestThread$ThreadDemo1.run(TestThread.java:23)
        - waiting to lock <0x000000071945d798> (a java.lang.Object)
        - locked <0x000000071945d788> (a java.lang.Object)
```
<br/>
in the above thread dump you can observe that "Thread-0" has obtained lock on "0x000000071945d788" and waiting to get the lock on "0x000000071945d798" and in the same time "Thread-1" has obtained the lock on "0x000000071945d798" and waiting to get the lock on "0x000000071945d788"

<br/>
when the program is waiting on the join(),wait():<br/>
this could happen if the program has used the join or wait functions un-conditionally or without any time limit. Below is the jstack of the thread which is waiting on the object in-finitely:<br/>
```java
"Thread-0" #9 prio=5 os_prio=0 tid=0x00007f84d80f6000 nid=0x3bba in Object.wait() [0x00007f84bf4f3000]
   java.lang.Thread.State: WAITING (on object monitor)
        at java.lang.Object.wait(Native Method)
        - waiting on <0x0000000719405d30> (a java.lang.Thread)
        at java.lang.Thread.join(Thread.java:1252)
        - locked <0x0000000719405d30> (a java.lang.Thread)
        at java.lang.Thread.join(Thread.java:1326)
        at demo.DeadlockDemo.run(DeadlockDemo.java:13)
```

