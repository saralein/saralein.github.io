---
layout: post
title:  "Memory in Java"
date:   2018-03-02 10:00:00 -0600
categories: jekyll update
---

Last week I set up VisualVM for some memory profiling on my server. I haven't truly explored Java memory allocation before, so I was unfamiliar and curious what some of the terms I was seeing meant. Mostly, what are heap memory and metaspace?

<img src="/images/heap.png" style="width: 75%; margin: auto; display: block;">

<img src="/images/metaspace.png" style="width: 75%; margin: auto; display: block;">

### Heap Memory

Java applications are allocated a limited amount of memory when starting up by the Java Virtual Machine (JVM). Prior to JDK 8, this memory was divided into heap and PermGen regions.

Heap memory is the storage for Java objects--class instances, arrays, etc. While max heap size is determined at launch, it may increase or decrease within the specific max bounds as the application runs.  For example, in the diagram above, we see the heap size in orange. It initially starts around 125 MB, and increases to 235 MB as requests come in to the server (still well below the max amount). Heap memory may also be tweaked to be of a fixed size. 

If you're interesting in checking out heap size specifications, you can use the following command on Unix/Linux:

`java -XX:+PrintFlagsFinal -version | grep HeapSize`

I checked it out, and can see that for my server the starting heap size was around 135 MB (not 125 as noted above).

<img src="/images/heap-size.png" style="width: 75%; margin: auto; display: block;">

We can also see the max heap size appears exactly as listed in VisualVM.

Heap space is further structured into regions called generations. As objects exist for longer periods, they may progress from a young generation to an older generation in heap memory. What about objects which are more permanent? For these, permanent generation (PermGen) was used.

### Permanent Generation (PermGen)

As mentioned above, PermGen was the second region of memory prior to JDK 8. When running an application, the JVM uses internal representations of its classes containing all necessary class metadata--hierarchy information, method data, etc. PermGen was the area where this metadata existed. Since PermGen size was allocated upon JVM launch, it was common for PermGen size to be insufficient, resulting in a `Java.Lang.OutOfMemoryError: PermGen` error. The PermGen maximum could be increased by specifying JVM parameters, but it was incapable of auto-increasing. This made it harder to tune.

### Metaspace

As mentioned a few times above, PermGen no longer exists as of JDK 8. It has instead been replaced by metaspace. How is it different than PermGen? Unlike PermGen, metaspace is does not reside in the Java heap. Metaspace is instead allocated from native memory. What does this mean? The max space available for metadata is now the total available system memory. Metaspace by default now auto-increases size up to the amount allowed by the underlying operating system.  (Remember that in contrast, PermGen always has a fixed maximum size specified by JVM parameters.)

Similar to heap information above, you can checkout metaspace specifications on Unix/Linux using the folllowing:

java -XX:+PrintFlagsFinal -version | grep Metaspace

You'll see something like this:

<img src="/images/metaspace-size.png" style="width: 75%; margin: auto; display: block;">

**Size**: Metaspace size can be fixed with the `-XX:MaxMetaspaceSize=<NNN>` where `<NNN>` is the maximum amount of space to be allocated for metadata. As mentioned above, the max space available could be the total available system memory. This is the default if the flag is not set.

**Garbage collection**: The initial amount of space for metadata can be set with the `-XX:MetaspaceSize=<NNN>` flag. Garnage collection occurs when metaspace grows to this metaspace size.

The frequency of garbage collection can be increased or decreased using two additional flags: `-XX:MinMetaspaceFreeRatio` and `-XX:MaxMetaspaceFreeRatio`. I'm still working on my understanding of these two flags.
