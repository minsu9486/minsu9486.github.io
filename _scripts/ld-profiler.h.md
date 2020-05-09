---
title: "Profiler.h"
permalink: /scripts/ld-profiler.h/
layout: splash
excerpt: "Low-level Debugger"
---

***

~~~c++
// USAGE:
//  - Just adding *Enter* and *Exit* are required on where you want to analyze.
//    I recommend put a *Enter* function on the very beginning of each target
//    function and correspondingly put a *Exit* function on the very end of
//    each target function.
//  - It generates the output file as csv format. Reading the csv file can be
//    a little hard...

#ifndef PROFILER_H
#define PROFILER_H

typedef unsigned __int64 uint64;

#include <vector>
#include <string>

struct FunctionNode
{
	FunctionNode()
	{
		callCount = 0;
		recursionCount = 0;
		recursionCountMax = 0;

		cyclesStart = 0;
		cyclesTotal = 0;
		cyclesExclusive = 0;

		nodePrev = nullptr;
		nodeNext = nullptr;
	}

	// ID for each function
	// it's not unique! Ref. ProfilerTestFuncSameButDiffParent and FindByName
	std::string functionName;

	unsigned callCount;
	unsigned recursionCount;
	unsigned recursionCountMax;
	uint64 cyclesStart;
	uint64 cyclesTotal;
	// excluding nested function calls
	// warning! this will be calculated in the last step, PrintInfoToCSV
	uint64 cyclesExclusive;

	FunctionNode* nodePrev;
	FunctionNode* nodeNext;
	std::vector<FunctionNode*> children;
};

class Profiler
{
public:
	static Profiler* GetInstance();

	// Use __FUNCSIG__ for its ID
	// it's not unique! Ref. ProfilerTestFuncSameButDiffParent and FindByName
	void Enter(const std::string ID);
	void Exit();

	// Test functions
	void ProfilerTestFuncRecursion(const unsigned count);
	void ProfilerTestFuncSameButDiffParent();

private:
	Profiler();
	~Profiler();
	void Init(FunctionNode* root);
	void Destroy(const uint64 cycles);

	// search from m_NodeCurr ONLY to m_NodePrev (parent)
	FunctionNode* FindByName(FunctionNode* node, const std::string name);

	// CSV
	void PrintInfoToCSV(std::ofstream& csvFile, FunctionNode* function, const unsigned depth);
	void PrintHeader(std::ofstream& csvFile, const unsigned depth);
	uint64 GetExclusiveTime(FunctionNode* node); // to get the exclusive time

	FunctionNode* m_NodeRoot;
	FunctionNode* m_NodeCurr;

	uint64  m_CyclesStart;
	uint64  m_CyclesTotal;
	float  m_CyclesPerSec; // to mesure the speed of this processor
};

#endif
~~~