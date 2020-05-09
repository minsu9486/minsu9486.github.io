---
title: "Profiler.cpp"
permalink: /scripts/ld-profiler.cpp/
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

#include <fstream> // csv
#include <algorithm>
#include <intrin.h> // rdtsc
#include <thread> // std::this_thread::sleep_for
#include <chrono> // std::chrono::seconds

#include <stdio.h>
#include <time.h> // clock

#include "Profiler.h"

static Profiler* g_Profiler = nullptr;
static bool g_ProgramExit = false;

Profiler* Profiler::GetInstance()
{
	if (g_Profiler == nullptr && !g_ProgramExit)
		g_Profiler = new Profiler();

	return g_Profiler;
}

Profiler::Profiler()
{
	m_NodeRoot = nullptr;
	m_NodeCurr = nullptr;

	m_CyclesStart = 0;
	m_CyclesTotal = 0;

	// Measure the speed of this processor
	uint64 cyclesMeasureStart = __rdtsc();
	std::this_thread::sleep_for(std::chrono::milliseconds(100)); // 0.1s
	uint64 cyclesMeasureEnd = __rdtsc();
	m_CyclesPerSec = ((float)cyclesMeasureEnd - (float)cyclesMeasureStart);
}

Profiler::~Profiler()
{
}

void Profiler::Init(FunctionNode* root)
{
	if (m_NodeRoot != nullptr)
	{
		printf("ERROR, Init: rootNode is re-initialized! \n");
		return;
	}

	m_NodeRoot = root;
	m_CyclesStart = root->cyclesStart;
}

void Profiler::Destroy(const uint64 cycles)
{
	if (g_ProgramExit == true)
	{
		printf("ERROR, Destroy: This function is called again!\n");
		return;
	}

	m_CyclesTotal = cycles - m_CyclesStart;

	{
		// get the current time for the file name
		time_t t = time(0);
		std::tm* timeinfo = localtime(&t);

		// init the file name by the given time
		char charBuff[128];
		snprintf(charBuff, sizeof(charBuff), "profiler_%04d%02d%02d_%02d%02d%02d.csv",
			timeinfo->tm_year + 1900, timeinfo->tm_mon + 1, timeinfo->tm_mday,
			timeinfo->tm_hour, timeinfo->tm_min, timeinfo->tm_sec);
		std::string csvFileName = charBuff;

		// create csv file
		std::ofstream csvFile(csvFileName.c_str());

		PrintHeader(csvFile, 0);
		PrintInfoToCSV(csvFile, m_NodeRoot, 0); // print all info
		csvFile.close();
	}

	g_ProgramExit = true;
	delete g_Profiler;
	g_Profiler = nullptr;
}

// find the given name from ONLY its parent
FunctionNode* Profiler::FindByName(FunctionNode* node, const std::string name)
{
	if (node == nullptr)
		return nullptr;

	if (name == node->functionName)
		return node;

	for (auto& child : node->children)
	{
		if (name == child->functionName)
			return child;
	}

	return nullptr;
}

void Profiler::Enter(const std::string functionName)
{
	const uint64 cycles = __rdtsc();

	// The first calling, need to init
	if (m_NodeCurr == nullptr)
	{
		m_NodeCurr = new FunctionNode();
		m_NodeCurr->cyclesStart = cycles;
		m_NodeCurr->functionName = functionName;
		m_NodeCurr->callCount++;

		this->Init(m_NodeCurr);
	}
	else
	{
		// check if it is a recursive functionFindByID
		if (functionName == m_NodeCurr->functionName)
		{
			m_NodeCurr->recursionCount++;
			m_NodeCurr->recursionCountMax = std::max(m_NodeCurr->recursionCountMax, m_NodeCurr->recursionCount);
		}
		else
		{
			// Otherwise, find the matching child node
			FunctionNode* nextNode = FindByName(m_NodeCurr, functionName);

			// If it's a new function...
			if (nextNode == nullptr)
			{
				nextNode = new FunctionNode();
				m_NodeCurr->children.push_back(nextNode);

				nextNode->functionName = functionName;

				m_NodeCurr->nodeNext = nextNode;
				nextNode->nodePrev = m_NodeCurr;
			}

			nextNode->cyclesStart = cycles;
			m_NodeCurr = nextNode;
		}

		m_NodeCurr->callCount++;
	}
}

void Profiler::Exit()
{
	const uint64 cycles = __rdtsc();

	if (m_NodeCurr->recursionCount > 0)
	{
		m_NodeCurr->recursionCount--;
		return;
	}

	m_NodeCurr->cyclesTotal += (cycles - m_NodeCurr->cyclesStart);
	m_NodeCurr = m_NodeCurr->nodePrev;

	if (m_NodeCurr == nullptr)
		this->Destroy(cycles);

}

void Profiler::PrintInfoToCSV(std::ofstream& csvFile, FunctionNode* node, const unsigned depth)
{
	if (node == nullptr)
		return;

	// indentation 
	for (unsigned i = 0; i < depth; ++i)
		csvFile << ", ";

	std::string nameWithoutComma = node->functionName;
	size_t commaPos = 0;
	while (1) // remove all commas from the function name
	{
		commaPos = nameWithoutComma.find(',');
		if (commaPos != std::string::npos)
			nameWithoutComma.replace(commaPos, 1, " ");
		else
			break;
	}
	csvFile << nameWithoutComma.c_str() << ", ";
	csvFile << node->callCount << ", ";

	//  Percents per Func by its exclusive time
	uint64 exclusiveClock = GetExclusiveTime(node);
	float exclusivePer = (float(exclusiveClock) / float(m_CyclesTotal)) * 100.f;
	exclusivePer = std::roundf(exclusivePer * 10000.f) / 10000.f; // for human-friendly number
	csvFile << exclusivePer << "per, ";
	
	// Sec per Func by its exclusive time 
	float exclusiveSec = (float)exclusiveClock / (m_CyclesPerSec * 10.f); // exclusiveClock / cycles of 1s
	exclusiveSec = std::roundf(exclusiveSec * 10000.f) / 10000.f; // for human-friendly number
	csvFile << exclusiveSec << "s, ";

	csvFile << node->cyclesTotal << ", ";       // Inclusive Cycles
	csvFile << exclusiveClock << ", ";          // Exclusive Cycles
	csvFile << node->recursionCountMax << ", "; // Max recursion Count

	csvFile << "\n"; // done for the info of the given node

	// children info
	if (!node->children.empty())
	{
		csvFile << "\n"; // a new line for its children
		PrintHeader(csvFile, depth + 1); // header for its children

		for (auto& child : node->children)
			PrintInfoToCSV(csvFile, child, depth + 1);

		csvFile << "\n"; // done for the children info of the given node
	}
}

void Profiler::PrintHeader(std::ofstream& csvFile, const unsigned depth)
{
	// indentation 
	for (unsigned i = 0; i < depth; ++i)
		csvFile << ", ";

	// head
	csvFile << "Function Name, Hit Count, Percents per Func, Sec per Func, Inclusive Cycles, Exclusive Cycles, Max Recursion Count\n";
}

// Return its exclusive time.
// No data: scan all children, initializing each child's exclusive time
uint64 Profiler::GetExclusiveTime(FunctionNode* node)
{
	if (node->cyclesExclusive != 0)
		return node->cyclesExclusive;

	if(node->children.empty())
		return node->cyclesTotal;

	uint64 accumulatedTime = 0;
	for (auto& child : node->children)
	{
		child->cyclesExclusive = GetExclusiveTime(child);
		accumulatedTime += child->cyclesTotal;
	}

	node->cyclesExclusive = node->cyclesTotal - accumulatedTime;
	return node->cyclesExclusive;
}

// This function is to test a recursive situation 
void Profiler::ProfilerTestFuncRecursion(const unsigned count)
{
	Profiler::GetInstance()->Enter(__FUNCSIG__);

	if (count == 0)
	{
		Profiler::GetInstance()->Exit();
		return;
	}

	ProfilerTestFuncRecursion(count - 1);

	Profiler::GetInstance()->Exit();
}

// This function is to test a special case where the same function is called by
// different parents 
void Profiler::ProfilerTestFuncSameButDiffParent()
{
	Profiler::GetInstance()->Enter(__FUNCSIG__);

	Profiler::GetInstance()->Exit();
}
~~~