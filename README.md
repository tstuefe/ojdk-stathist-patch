# Always-on Statistical History for the OpenJDK

This is a patch against the OpenJDK which adds a statistical history to the OpenJDK. When added, there will be a new option:

```
-XX:[+-]EnableStatHist
```

as well as a new jcmd command:

```
jcmd <pid> VM.stathist
```

which will print out a statistical history like this one:

```
16541:
History:
------------system------------
           avail: Memory available without swapping
            swap: Swap space used
              si: Number of pages swapped in [delta]
              so: Number of pages pages swapped out [delta]
              pr: Number of tasks running
              pb: Number of tasks blocked
          cpu-us: Global cpu user time
          cpu-sy: Global cpu system time
          cpu-id: Global cpu idle time
          cpu-wa: Global cpu time spent waiting for IO
          cpu-st: Global cpu time stolen
          cpu-gu: Global cpu time spent on guest
-----------process------------
            virt: Virtual size
         rss-all: Resident set size, total
        rss-anon: Resident set size, anonymous memory
        rss-file: Resident set size, file mappings
         rss-shm: Resident set size, shared memory
            swdo: Memory swapped out
          cpu-us: Process cpu user time
          cpu-sy: Process cpu system time
           io-of: Number of open files
           io-rd: IO bytes read from storage or cache [delta]
           io-wr: IO bytes written [delta]
             thr: Number of native threads
-------------jvm--------------
       heap-comm: Java Heap Size, committed
       heap-used: Java Heap Size, used
       meta-comm: Meta Space Size, committed
       meta-used: Meta Space Size, used
            code: Code cache, committed
             mlc: Memory malloced by hotspot (requires NMT)
        jthr-num: Number of java threads
         jthr-nd: Number of non-demon java threads
         jthr-cr: Threads created [delta]
         jthr-st: Total reserved size of java thread stacks
         cls-num: Classes (instance + array)
          cls-ld: Class loaded [delta]
         cls-uld: Classes unloaded [delta]

[delta] values refer to the previous measurement.

Short Term Values:
                      ------------------system------------------ ---------------------process---------------------- --------------------------jvm--------------------------
                                              -------cpu--------       -------rss--------      -cpu- ----io----     --heap--- --meta---           ----jthr----- ----cls----
                      avail  swap si so pr pb us sy id  wa st gu virt  all  anon file shm swdo us sy of rd  wr  thr comm used comm used code mlc  num nd cr st  num  ld uld 

                Now   117.1g   2m  0  0  6  0                    36.3g 664m 621m  43m  0k   0k        4 11k  0k  52  96m  37m  50m  42m  34m 115m  10  1  0 51m 2701  0   0 
2018-11-14 15:17:10   117.2g   2m  0  0  1  0  1  0  99  0  0  0 36.3g 664m 621m  43m  0k   0k  0  0  3 34k 65k  52  96m  37m  50m  42m  34m 115m  10  1  0 51m 2701  0   0 
2018-11-14 15:16:55   117.2g   2m  0  0  1  0  0  0 100  0  0  0 36.3g 664m 621m  43m  0k   0k  0  0  3 11k  0k  52  96m  37m  50m  42m  34m 115m  10  1  0 51m 2701  0   0 
2018-11-14 15:16:40   117.2g   2m  0  0  1  0  0  0 100  0  0  0 36.3g 664m 621m  43m  0k   0k  0  0  3 11k  0k  52  96m  37m  50m  42m  34m 115m  10  1  0 51m 2701  0   0 
2018-11-14 15:16:25   117.2g   2m  0  0  1  0  0  0 100  0  0  0 36.3g 664m 621m  43m  0k   0k  0  0  3 11k  0k  52  96m  37m  50m  42m  34m 115m  10  1  0 51m 2701  0   0 
... (snip) ...
Mid Term Values:
                      ------------------system------------------ ----------------------process---------------------- --------------------------jvm--------------------------
                                              -------cpu--------       -------rss--------      -cpu- ----io-----     --heap--- --meta---           ----jthr----- ----cls----
                      avail  swap si so pr pb us sy id  wa st gu virt  all  anon file shm swdo us sy of rd   wr  thr comm used comm used code mlc  num nd cr st  num  ld uld 

2018-11-14 14:51:40   117.4g   2m  0  0  1  0  0  0 100  0  0  0 36.3g 664m 621m  43m  0k   0k  0  0  3 677k  0k  52  96m  37m  50m  42m  34m 115m  10  1  0 51m 2701  0   0 
2018-11-14 14:36:40   117.3g   2m  0  0  1  0  0  0 100  0  0  0 36.3g 664m 621m  43m  0k   0k  0  0  3 677k  0k  52  96m  37m  50m  42m  34m 115m  10  1  0 51m 2701  0   0 
2018-11-14 14:21:40   117.5g   2m  0  0  1  0  0  0 100  0  0  0 36.3g 664m 621m  43m  0k   0k  0  0  3 677k  0k  52  96m  37m  50m  42m  34m 115m  10  1  0 51m 2701  0   0 
2018-11-14 14:06:40   117.6g   2m  0  0  4  0  0  0  99  0  0  0 36.3g 664m 621m  43m  0k   0k  0  0  3 700k 64k  52  96m  37m  50m  42m  34m 115m  10  1  0 51m 2701  0   0 
2018-11-14 13:51:40   117.3g   2m  0  0  1  0  0  0 100  0  0  0 36.3g 664m 621m  43m  0k   0k  0  0  3 677k  0k  52  96m  37m  50m  42m  34m 115m  10  1  0 51m 2701  0   0 
...(snip)...
Long Term Values:
                      ------------------system------------------ ---------------------process---------------------- ---------------------------jvm----------------------------
                                              -------cpu--------       -------rss--------      -cpu- ----io----     --heap--- --meta---           ----jthr----- -----cls------
                      avail  swap si so pr pb us sy id  wa st gu virt  all  anon file shm swdo us sy of rd wr   thr comm used comm used code mlc  num nd cr st  num  ld   uld  

2018-11-13 14:51:36   118.5g   2m  0  0  1  0  0  0 100  0  0  0 36.3g 664m 621m  43m  0k   0k  0  0  3 5m   0k  52  96m  37m  50m  42m  34m 115m  10  1  0 51m 2701    0    0 
2018-11-13 12:51:36   118.6g   2m  3  0  1  0  0  0 100  0  0  0 36.3g 664m 621m  43m  0k   0k  0  0  3 5m   0k  52  96m  37m  50m  42m  34m 115m  10  1  0 51m 2701    0    0 
2018-11-13 10:51:36   118.7g   2m  0 24  1  0  3  1  96  0  0  0 36.3g 664m 621m  43m  0k   0k  0  0  3 5m 120k  52  96m  37m  50m  42m  34m 115m  10  1  2 51m 2701 3300 3346 
2018-11-13 08:51:35   118.7g   0k  0  0  1  0  1  0  99  0  0  0 36.3g 677m 633m  44m  0k   0k  0  0  3 5m   0k  52 108m  32m  51m  42m  34m 115m  10  1  0 51m 2747    0    0 
2018-11-13 06:51:35   118.4g   0k  0  0  1  0  0  0 100  0  0  0 36.3g 677m 633m  44m  0k   0k  0  0  3 5m   0k  52 108m  32m  51m  42m  34m 115m  10  1  0 51m 2747    0    0 
...(snip)...
```

This is a rolling history, spanning up to 10 days, of a number of key values. Key values range from JVM specifics like heap size, metaspace size, number of threads etc, to platform specifics like memory footprint, cpu load, io- and swapping activity etc.

A periodic tasks collects those values, in - by default - 15 second intervals. They are then fed into a FIFO spanning 10 days. To save memory that FIFO is downsampled in two steps, so we have the last n hours in high resolution and the last n days in low resolution. All these parameters are configurable.

## Runtime costs?

Almost nil. The feature uses up ~80KB memory. CPU load is neglectible (DaCapo benchmarks do not twitch.)

## Motivation

This feature has been really popular with support over the years. Be it that the VM is starved for resources by the OS, that we have some slowly developing leak situation or a fast native leak etc: these values are a first and easy way to get a first stab at a situation, before we start more expensive analysis.

The explicit design goal of this history was to be very cheap - cheap enough to be *always on* and getting forgotten. It is, in our port, enabled by default. That way, if a problem occurs at a customer site, we immediately see developments spanning the last 10 days, without having to reproduce the issue.

It is also robust enough to be usable during error reporting without endangering the error reporting process or falsifying the picture.

## Why not in the OpenJDK?

This feature was has been proposed to the OpenJDK community for upstream inclusion, see http://mail.openjdk.java.net/pipermail/serviceability-dev/2018-November/025909.html .

It has been rejected however since the features it provides are a domain of JFR (Java Flight Recorder) and the JMC (Java Mission Control). Both JFR and JMC have been open sourced with JDK 11 and are since then freely available - in former releases they had been paid features of the Oracle JDK.

The consensus is that we do not want two backend colslecting statistical data, but rather concentrate our efforts on one which does it really well. Should JFR lack capabilities this  Statistics History offers, we want to rather improve JFR instead of introducing a second solution.

The author fully understand and support this decision. However, he still thinks this patch to be valuable for the following cases:

- you are a downstream OpenJDK maintainer and want to add this feature to older releases which have no JFR/JMC.
- Or maybe you want to exclude JFR/JMC from your build and need a cheap monitoring solution. Please note though that this feature is by no means an fully fledged alternative to JFR.
- for research, fun and profit.

The patch itself is really simple and non-invasive, so it should be easy to maintain downstream. Almost all code resides in new files, and the interface toward the VM is minimal.

I will keep these patches up-to-date - one version for jdk8u, jdk11u and jdk/jdk tip  - as long as I think it makes sense.

