
> Update 2019-02-28: the statistical history now lives in an own branch in the OpenJDK sandbox repository, branch "stuefe-statistical-history":
> http://hg.openjdk.java.net/jdk/sandbox/shortlog/9653470b7294
> 
> This is the new master for this feature and I will stop updating these patch files.


# Always-on Statistical History for the OpenJDK

This is a patch against the OpenJDK which adds a statistical history to the OpenJDK. When added, there will be a new option:

```
-XX:[+-]EnableStatHist
```

as well as a new jcmd command:

```
jcmd <pid> VM.stathist

Syntax : VM.stathist [options]

Options: (options must be specified using the <key> or <key>=<value> syntax)
        scale : [optional] Memory usage in which to scale. Valid values are: k, m, g (fixed scale) or "dynamic" for a dynamically chosen scale. (STRING, dynamic)
        no-legend : [optional] Omit legend. (BOOLEAN, false)
        raw : [optional] Print raw values (debug only). (BOOLEAN, false)
        csv : [optional] CSV format. (BOOLEAN, false)

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

Full example output [here](examples/stathist-volker.txt). 

---

In addition to jcmd, this history is printed as part of the hs-err file.

---

This is a rolling history, spanning up to 10 days, of a number of key values. Key values range from JVM specifics like heap size, metaspace size, number of threads etc, to platform specifics like memory footprint, cpu load, io- and swapping activity etc.

A periodic tasks collects those values, in - by default - 15 second intervals. They are then fed into a FIFO spanning 10 days. To save memory that FIFO is downsampled in two steps, so we have the last n hours in high resolution and the last n days in low resolution. All these parameters are configurable.

The statistical history is cheap enough to be *always on*, by default. That way, if a problem occurs at a customer site, we immediately see developments spanning the last 10 days, without having to reproduce the issue.

## Runtime costs?

Small-ish. The feature uses up ~80KB memory. CPU load is not measurable (DaCapo benchmarks do not twitch.)

## Why not in the OpenJDK?

This feature was has been proposed to the OpenJDK community for upstream inclusion, see http://mail.openjdk.java.net/pipermail/serviceability-dev/2018-November/025909.html .

It was rejected since it conflicts with JFR (Java Flight Recorder) and the JMC (Java Mission Control):

Both JFR and JMC have been open sourced with JDK 11 and since then are freely available. The consensus is that we do not want two backends collecting statisticals. We rather concentrate our efforts on one which does it really well, which going forward should be JFR.

This decision makes sense and therefore this patch will not be brought upstream.

--

However, this patch may still be valuable to some people:

- you are a downstream maintainer and want to add this feature to older releases which have no JFR/JMC. Or, you need to exclude JFR/JMC from your build for whatever reason and want a cheap monitoring solution (please note though that this feature cannot compete with JFR).
- for experimentation and playing around.

The patch itself is quite simple and non-invasive, so it can be maintained downstream. Almost all code resides in new files, and the interface toward the VM is slim.


## Patch files

- Patch based on jdk/jdk tip (currently jdk12): [stathist.patch](stathist.patch)
- Patch based on jdk/jdk11u: [stathist-11.patch](stathist-11.patch)
- Patch based on jdk/jdk8u: [stathist-8.patch](stathist-8.patch)
