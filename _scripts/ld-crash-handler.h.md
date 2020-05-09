---
title: "CrashHandler.h"
permalink: /scripts/ld-crash-handler.h/
layout: splash
excerpt: "Low-level Debugger"
---

***

~~~c++
// USAGE: Add the below lines on the top of the body of main.cpp
//
//  // sets the minimum size of the stack
//  ULONG size = 1024 * 16;         // 1 MB * 16
//  SetThreadStackGuarantee(&size); // extends the stack to the given size
//
//  // enables an application to supersede the top-level exception handler
//  SetUnhandledExceptionFilter(ExceptionFilter_Callback);

#pragma once

#include <windows.h> // for Dbghelp.h
#include <Dbghelp.h> // use minidump

#pragma comment(lib, "dbghelp.lib") // link with dbghelp.lib

bool WriteDump(EXCEPTION_POINTERS* pException)
{
  char dumpName[MAX_PATH];
  char* appName = dumpName + GetModuleFileNameA(GetModuleHandleA(0), dumpName, MAX_PATH);
  SYSTEMTIME currTime;
  GetSystemTime(&currTime);
  wsprintfA(appName - strlen(".exe"), // only get its name, not the extension
    "_%4d%02d%02d_%02d%02d%02d.dmp",  // to make a unique file name, use the current time
    currTime.wYear, currTime.wMonth, currTime.wDay,
    currTime.wHour, currTime.wMinute, currTime.wSecond);

  // file handle of where to save to
  HANDLE hFile = CreateFileA(dumpName, GENERIC_WRITE, FILE_SHARE_READ, 0, CREATE_ALWAYS, FILE_ATTRIBUTE_NORMAL, 0);
  if (hFile == INVALID_HANDLE_VALUE)
  {
    printf("Creating Dump File is failed.\n");
    return false;
  }

  // since debugging is used for a crash
  MINIDUMP_EXCEPTION_INFORMATION exceptionInfo;
  exceptionInfo.ThreadId = GetCurrentThreadId();
  // comes from the exception
  exceptionInfo.ExceptionPointers = pException;
  // used when writing a dump for a different process
  exceptionInfo.ClientPointers = FALSE;

  MiniDumpWriteDump(
    GetCurrentProcess(),
    GetCurrentProcessId(),
    hFile,
    //  Little Dump: Partial information but still useful
    MINIDUMP_TYPE(MiniDumpNormal),
    pException ? &exceptionInfo : nullptr,
    nullptr,
    nullptr);

  CloseHandle(hFile);

  return true;
}

LONG CALLBACK ExceptionFilter_Callback(EXCEPTION_POINTERS* pException)
{
  // creates a file and write the dump on it
  if(WriteDump(pException) == false)
    printf("Logging Minidump is failed.\n");

  // do not return EXCEPTION_EXECUTE_HANDLER
  // the crash handler does NOT inhibit normal behavior
  return EXCEPTION_CONTINUE_SEARCH; // it means obeying the SetErrorMode flags
}
~~~