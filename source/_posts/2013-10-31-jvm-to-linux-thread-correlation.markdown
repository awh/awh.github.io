---
layout: post
title: "JVM to Linux thread correlation"
date: 2013-10-31 12:13
comments: true
categories: 
---
It is possible to correlate the output of <code>ps</code> with <code>jstack</code> to determine which JVM thread is consuming CPU:
## Determine the PID and LWP of the offending thread
Execute the following command:
    ps -eLo pid,lwp,pcpu,comm | grep java
and note the PID and LWP of the thread which is consuming the CPU, in this case we can see LWP 30375 in PID 30358 is consuming 99.2%:

       PID   LWP %CPU COMMAND
     30358 30358  0.0 java
     30358 30359  0.0 java
     30358 30360  0.0 java
     30358 30361  0.0 java
     30358 30362  0.0 java
     30358 30363  0.0 java
     30358 30364  0.0 java
     30358 30365  0.0 java
     30358 30366  0.0 java
     30358 30367  0.0 java
     30358 30368  0.0 java
     30358 30369  0.0 java
     30358 30370  0.0 java
     30358 30371  0.0 java
     30358 30372  0.0 java
     30358 30373  0.0 java
     30358 30374  0.0 java
     30358 30375 99.2 java
     30358 30403  0.0 java

## Correlate with jstack
Execute the following command, substituting the PID found above (if the Java process is running as root you will need to sudo):

    jstack 30358

This will produce output of the following form. The `nid=0x76a7` below is the hexadecimal representation of the busy LWP above (30375):

    ...
    ...
    ...
    "Busy thread" prio=10 tid=0x00007f2f780ad000 nid=0x76a7 runnable [0x00007f2f6ea65000]
       java.lang.Thread.State: RUNNABLE
            at Main$BusyThread.consumeAllTheCycles(Main.java:34)
            at Main$BusyThread.run(Main.java:30)
    
    "Sleepy thread 3" prio=10 tid=0x00007f2f780aa800 nid=0x76a6 waiting on condition [0x00007f2f6eb66000]
       java.lang.Thread.State: TIMED_WAITING (sleeping)
            at java.lang.Thread.sleep(Native Method)
            at Main$SleepyThread.sleepyTeim(Main.java:16)
            at Main$SleepyThread.run(Main.java:10)
    
    "Sleepy thread 2" prio=10 tid=0x00007f2f780a8800 nid=0x76a5 waiting on condition [0x00007f2f6ec67000]
       java.lang.Thread.State: TIMED_WAITING (sleeping)
            at java.lang.Thread.sleep(Native Method)
            at Main$SleepyThread.sleepyTeim(Main.java:16)
            at Main$SleepyThread.run(Main.java:10)
    
    "Sleepy thread 1" prio=10 tid=0x00007f2f780a6800 nid=0x76a4 waiting on condition [0x00007f2f6ed68000]
       java.lang.Thread.State: TIMED_WAITING (sleeping)
            at java.lang.Thread.sleep(Native Method)
            at Main$SleepyThread.sleepyTeim(Main.java:16)
            at Main$SleepyThread.run(Main.java:10)
    ...
    ...
    ...

In this example, we can see that the thread named <code>Busy thread</code> is the offender; the stack trace immediately below it can be used to determine exactly where in the code the problem lies - `consumeAllTheCycles()`!
