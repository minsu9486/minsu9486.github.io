---
title: "MemoryDebugger.h"
permalink: /scripts/ld-memory-debugger.h/
layout: splash
excerpt: "Low-level Debugger"
---

***

~~~c++
// USAGE: Add the below line on the last line of the body of main.cpp
//
//  MemoryDebugger::GetInstance()->Destroy();

#include <new>
#include <fstream>  // ofstream
#include <stdarg.h> // va_* for the Log function

// Inconsistent annotation for 'new': this instance has no annotations.
#pragma warning(disable : 28251)

void* operator new(size_t size, const char* file, unsigned int line) noexcept(false);
void* operator new(size_t size, const std::nothrow_t&) throw ();
void* operator new[](size_t size, const char* file, unsigned int line) noexcept(false);
void* operator new[](size_t size, const std::nothrow_t&) throw ();
void operator delete(void* ptr, const char* file, unsigned int line) throw ();
void operator delete(void* ptr, const std::nothrow_t&) throw ();
void operator delete[](void* ptr, const char* file, unsigned int line) throw ();
void operator delete[](void* ptr, const std::nothrow_t&) throw ();

class MemoryDebugger
{
public:
  static MemoryDebugger* GetInstance();
  static void Init();
  static void Destroy();
  ~MemoryDebugger();

  int Log(const char* fmt, ...)
  {
    char buffer[4096];
    va_list args;
    va_start(args, fmt);
    int rc = vsnprintf(buffer, sizeof(buffer), fmt, args);
    va_end(args);

    printf("%s", buffer);
    outfile << buffer;

    return rc;
  }

  // Manually close the file in case of __debugbreak() 
  void CloseFile()
  {
    outfile.close();
  }

private:
  MemoryDebugger();
  std::ofstream outfile;
};
~~~