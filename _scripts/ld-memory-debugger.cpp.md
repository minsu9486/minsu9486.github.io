---
title: "MemoryDebugger.cpp"
permalink: /scripts/ld-memory-debugger.cpp/
layout: splash
excerpt: "Low-level Debugger"
---

***

~~~c++
// USAGE: Add the below line on the last line of the body of main.cpp
//
//  MemoryDebugger::GetInstance()->Destroy();

#include "MemoryDebugger.h"
#include "mallocator.h"
#include "StackTrace.h"

// Calling 'VirtualFree' without the MEM_RELEASE flag might free memoty but not address descriptors (VADs).
#pragma warning(disable : 6250)

struct AllocationInfo
{
  AllocationInfo(void* _ptr, bool _isArray) : ptr(_ptr), isArray(_isArray) {};
  ~AllocationInfo() { ptr = nullptr; }

  void* ptr = nullptr;
  bool isArray = false;
};

typedef std::vector<AllocationInfo, Mallocator<AllocationInfo> > AllocationList;
AllocationList allocations;

void CheckDeleteTypeMatch(const AllocationList::iterator allocListIt, const bool _isArray)
{
  if ((*allocListIt).isArray != _isArray)
  {
    MemoryDebugger::GetInstance()->Log("%s\n", "Mismatched delete is detected!");
    CONTEXT* c = (CONTEXT*)std::malloc(sizeof(CONTEXT));
    if (c != nullptr)
    {
      GET_CONTEXT(*c);
      StackTrace(c);

      MemoryDebugger::GetInstance()->Log("  %s\n", (resultList.end() - 1)->c_str());
      resultList.pop_back();
    }

    MemoryDebugger::GetInstance()->CloseFile();
    __debugbreak();
  }
}

void PrintDeleteAbuseInfo()
{
  MemoryDebugger::GetInstance()->Log("%s\n", "Delete abuse is detected!");
  CONTEXT* c = (CONTEXT*)std::malloc(sizeof(CONTEXT));
  if (c != nullptr)
  {
    GET_CONTEXT(*c);
    StackTrace(c);

    MemoryDebugger::GetInstance()->Log("  %s\n", (resultList.end() - 1)->c_str());
    MemoryDebugger::GetInstance()->Log("  %s\n", "A breakpoint will be triggered.");
    resultList.pop_back();
  }

  MemoryDebugger::GetInstance()->CloseFile();
  __debugbreak();
}

// To prevent buffer overflow
void* PageAlignedAllocate(size_t size) {

  size_t AllocationSize = 4096;

  // only return an address when the given size is zero
  if (size != 0)
  {
    float logsize = log((float)size);
    float log2 = log(2.f);
    float ciellog = ceil(logsize / log2);
    AllocationSize = unsigned(pow(2, ciellog));

    // don't need to allocate
    if (AllocationSize == 0)
      return NULL;

    AllocationSize = AllocationSize < 4096 ? 4096 : AllocationSize;
  }

  // reserves (the given size * 2) of address space.
  void* p = VirtualAlloc(0, AllocationSize * size_t(2), MEM_RESERVE, PAGE_NOACCESS);
  // only commits the first given size.
  p = VirtualAlloc(p, AllocationSize, MEM_COMMIT, PAGE_READWRITE);
  return (unsigned char*)p + (AllocationSize - size);
}

// to prevent accesssing after delete
void PageAlignedDecommit(void* p) {
  // MEM_DECOMMIT: the function decommits all memory pages that contain one or
  //               more bytes in the range from the lpAddress parameter to
  //               (lpAddress+dwSize)
  // ref. https://docs.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-virtualfree
  VirtualFree(p, 4096, MEM_DECOMMIT);
}

void* operator new(size_t size) noexcept(false)
{
  void* ptr = PageAlignedAllocate(size);
  if (ptr)
  {
    CONTEXT* c = (CONTEXT*)std::malloc(sizeof(CONTEXT));
    if (c != nullptr)
    {
      GET_CONTEXT(*c);
      StackTrace(c);

      allocations.push_back(AllocationInfo(ptr, false));
    }

    return ptr;
  }
  else
    throw std::bad_alloc{};
}

void* operator new(size_t size, const std::nothrow_t&) throw ()
{
  void* ptr = PageAlignedAllocate(size);
  if (ptr)
  {
    CONTEXT* c = (CONTEXT*)std::malloc(sizeof(CONTEXT));
    if (c != nullptr)
    {
      GET_CONTEXT(*c);
      StackTrace(c);

      allocations.push_back(AllocationInfo(ptr, false));
    }

    return ptr;
  }
  else
    return nullptr;
}

void* operator new[](size_t size) noexcept(false)
{
  void* ptr = PageAlignedAllocate(size);
  if (ptr)
  {
    CONTEXT* c = (CONTEXT*)std::malloc(sizeof(CONTEXT));
    if (c != nullptr)
    {
      GET_CONTEXT(*c);
      StackTrace(c);

      allocations.push_back(AllocationInfo(ptr, true));
    }

    return ptr;
  }
  else
    throw std::bad_alloc{};
}

void* operator new[](size_t size, const std::nothrow_t&) throw ()
{
  void* ptr = PageAlignedAllocate(size);
  if (ptr)
  {
    CONTEXT* c = (CONTEXT*)std::malloc(sizeof(CONTEXT));
    if (c != nullptr)
    {
      GET_CONTEXT(*c);
      StackTrace(c);

      allocations.push_back(AllocationInfo(ptr, true));
    }

    return ptr;
  }
  else
    return nullptr;
}

void operator delete(void* ptr) throw ()
{
  if (ptr == nullptr)
    return;

  int index = 0;
  AllocationList::iterator I = allocations.begin();
  for (; I != allocations.end(); ++I, ++index)
  {
    if ((*I).ptr == ptr)
      break;
  }

  if (I == allocations.end())
  {
    PrintDeleteAbuseInfo();
    return;
  }
  else
  {
    CheckDeleteTypeMatch(I, false);

    allocations.erase(I);
    resultList.erase(resultList.begin() + index);

    return PageAlignedDecommit(ptr);
  }
}

void operator delete(void* ptr, const std::nothrow_t&) throw ()
{
  if (ptr == nullptr)
    return;

  int index = 0;
  AllocationList::iterator I = allocations.begin();
  for (; I != allocations.end(); ++I, ++index)
  {
    if ((*I).ptr == ptr)
      break;
  }

  if (I == allocations.end())
  {
    PrintDeleteAbuseInfo();
    return;
  }
  else
  {
    CheckDeleteTypeMatch(I, false);

    allocations.erase(I);
    resultList.erase(resultList.begin() + index);

    return PageAlignedDecommit(ptr);
  }
}

void operator delete[](void* ptr) throw ()
{
  if (ptr == nullptr)
    return;

  int index = 0;
  AllocationList::iterator I = allocations.begin();
  for (; I != allocations.end(); ++I, ++index)
  {
    if ((*I).ptr == ptr)
      break;
  }

  if (I == allocations.end())
  {
    PrintDeleteAbuseInfo();
    return;
  }
  else
  {
    CheckDeleteTypeMatch(I, true);

    allocations.erase(I);
    resultList.erase(resultList.begin() + index);

    return PageAlignedDecommit(ptr);
  }
}

void operator delete[](void* ptr, const std::nothrow_t&) throw ()
{
  if (ptr == nullptr)
    return;

  int index = 0;
  AllocationList::iterator I = allocations.begin();
  for (; I != allocations.end(); ++I, ++index)
  {
    if ((*I).ptr == ptr)
      break;
  }

  if (I == allocations.end())
  {
    PrintDeleteAbuseInfo();
    return;
  }
  else
  {
    CheckDeleteTypeMatch(I, true);

    allocations.erase(I);
    resultList.erase(resultList.begin() + index);

    return PageAlignedDecommit(ptr);
  }
}

///////////////////////////////////////////////////////////////////////////////
///////////////////////////////////////////////////////////////////////////////

MemoryDebugger* pMemoryDebugger = NULL;
MemoryDebugger* MemoryDebugger::GetInstance()
{
  if (pMemoryDebugger == NULL)
  {
    void* buffer = malloc(sizeof(MemoryDebugger));

    pMemoryDebugger = new (buffer) MemoryDebugger();

  }

  return pMemoryDebugger;
}

void MemoryDebugger::Init()
{
  MemoryDebugger::GetInstance();
}

void MemoryDebugger::Destroy()
{
  if (pMemoryDebugger)
    pMemoryDebugger->~MemoryDebugger();
}

MemoryDebugger::MemoryDebugger()
{
  // No need to close the file manually, as it does so upon destruction
  outfile.open("leaks.log");
}

MemoryDebugger::~MemoryDebugger()
{
  if (allocations.size() != 0)
  {
    Log("%s\n", "Memory leaks are detected:");

    for (auto& remainedResult : resultList)
      Log("  %s\n", remainedResult.c_str());

    for (auto& allocInfo : allocations)
    {
      if (allocInfo.isArray)
        delete[] allocInfo.ptr;
      else
        delete allocInfo.ptr;
    }

    allocations.clear();
    resultList.clear();

    Log("  %s\n", "All memory leaks have been deallocated by Memory Debugger.");

  }
  else
    Log("%s\n", "No memory leak.");
}
~~~