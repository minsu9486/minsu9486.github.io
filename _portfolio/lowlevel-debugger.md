---
title: "Low-level Debugger"
permalink: /portfolio/lowlevel-debugger/
date: 2020-04-05
excerpt: "Three types of low-level debuggers: crash handler, memory debugger, and profiler"
header:
  image: /assets/images/lowDebug_main_wide.png
  caption: "Low-level programming is too hard. :("
  teaser: /assets/images/lowDebug_teaser.png
company: "Academic Project"
platform: "Windows PC"
skills: "C++"
role: "Tool Engineer"
responsibilities: "Implementing easy-interpolating debuggers"
---

| |
| **Company**<br>&nbsp;&nbsp;&nbsp;{{page.company}}								|||**Engine**<br>&nbsp;&nbsp;&nbsp;{{page.engine}}
| **Platform**<br>&nbsp;&nbsp;&nbsp;{{page.platform}}							|||**Skills**<br>&nbsp;&nbsp;&nbsp;{{page.skills}}	
| **Role**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.role}}</span>	|||**Responsibilities**<br>&nbsp;&nbsp;&nbsp;<span style="color:red">{{page.responsibilities}}</span>

## Features
 - Crash handler
 - Memory debugger
 - Instrumented profiler

### Crash Handler
<figure class="half">
	<img src="/assets/images/lowDebug_crashHandler_1.png">
	<img src="/assets/images/lowDebug_crashHandler_2.png">
	<figcaption>It creates a dump file when an app is accidentally crashed. Using the dump file, It operates without the presence of a debugger and provides some form of debugging information (Mini Dump) that can be later analyzed with a debugger.</figcaption>
</figure>

### Memory Debugger
<figure>
  <img src="/assets/images/lowDebug_memoryDebugger_1.png">
  <figcaption>It records useful information in the event of heap related bugs. It detects the following heap related bugs and incorrect usages: memory leak, buffer read and write overflow, buffer read and writes after delete, double news, double deletes, mismatched new/delete[], deleting a non-heap allocated pointer.</figcaption>
</figure>

### Instrumented Profiler
<figure>
  <img src="/assets/images/lowDebug_profiler_output.png">
  <figcaption>The above image is a capture of what my profiler analyzed some fraction of Box2D. The type of the profiler I made is a manual instrumented profiler which is a method of manually injecting sections of code that is responsible to write out trace information.</figcaption>
</figure>

<figure>
  <img src="/assets/images/lowDebug_profiler_record_types.png">
  <figcaption>It records the following data: function name, hit count, percents per func, sec per func, inclusive cycles, exclusive cycles, max recursion count.</figcaption>
</figure>

Measuring super-accurate time is quite challenging in C++. Although *__rdtsc* (clock cycles) is primarily used in this profiler, it is, to be honest, not a good idea because OS scheduler could reschedule other threads, processes at arbitrary moments, or slow down the clock.

However, I believe using *__rdtsc* is enough for my academic purpose which is focused on making structure and understanding how a profiler is working on. To convert clock cycles to a human-understanding format (seconds), *sleep_for* is used as the following code is written:

```c++
Profiler::Profiler() {
 ...
 // Measure the speed of this processor
 uint64 cyclesMeasureStart = __rdtsc();
 // 100ms = 0.1s
 std::this_thread::sleep_for(std::chrono::milliseconds(100));
 uint64 cyclesMeasureEnd = __rdtsc();
 m_CyclesPerSec = cyclesMeasureEnd - cyclesMeasureStart;
}

void Profiler::PrintInfoToCSV(...) {
 ...
 // exclusiveClock is a number of clock cycles by __rdtsc
 // m_CyclesPerSec * 10.f = 1s
 float exclusiveSec = exclusiveClock / (m_CyclesPerSec * 10.f);
 ...
}
```

## Sorce Code
 - [CrashHandler.h](/scripts/ld-crash-handler.h/)
 - [MemoryDebugger.h](/scripts/ld-memory-debugger.h/)
 - [MemoryDebugger.cpp](/scripts/ld-memory-debugger.cpp/)
 - [Profiler.h](/scripts/ld-profiler.h/)
 - [Profiler.cpp](/scripts/ld-profiler.cpp/)