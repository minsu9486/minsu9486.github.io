---
title: "UtilFunc.cpp"
layout: splash
excerpt: "LINE: JoJo's Bizarre Adventure SS"
---

***

~~~cs
//
//  UtilFunc.cpp
//  Taro
//
//  Created by mikata shigenori on 13/01/29.
//  Created by Funnygloo on 2015. 12. 15..
//  Minsu Kang
//

#include "UtilFunc.h"
#include "BQListView.h"
#include "BQScrollBar.h"
#include "Debug.h"
#include "DebugMenu.h"
#include "ActionExt.h"
#include "GameDefine.h"
#include <base/data/BQUUID.h>
#include <base/device/BQAppPlatform.h>
#include "utf8.h"

using namespace std;
using namespace cocos2d;

.
.
.

std::string UtilFunc::cutAndAddEllipsis(const std::string targetStr, const int maxUTF8Leng)
{
    // Nickname length check
    std::string arrangedLineName = replaceEmojiToSqare(targetStr);
    
    int utf8Len = UtilFunc::strlen_utf8(arrangedLineName.c_str());
    if(utf8Len >= maxUTF8Leng) // Max length
    {
        // Calcuate the 14th UTF-8
        utf8Len = 0;
        int uniLen = 0;
        char* s = new char[std::strlen(arrangedLineName.c_str()) + 1]; // +1 for NULL
        std::strcpy(s, arrangedLineName.c_str());
        
        char* temp = s;
        while (*temp)
        {
            if((*temp++ & 0xc0) != 0x80)
                ++utf8Len;
            
            if(utf8Len < maxUTF8Leng)
                ++uniLen;
        }
        
        // Cut till 14th and add "..."
        arrangedLineName.erase(arrangedLineName.begin() + uniLen, arrangedLineName.end());
        arrangedLineName.insert(uniLen, "...");
        
        delete [] s;
    }
    
    return arrangedLineName;
}

std::string UtilFunc::cutAndAddNewLine(const std::string targetStr, const int maxCharLeng)
{
    CCLog("=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=");
    CCLog("=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=");
    
    std::string outputStr = "";
    
    if(targetStr.length() == 0)
        return outputStr;
    
    std::string sourceStr(targetStr);
    CCLog("Origin Discription : %s", sourceStr.c_str());
    
    std::vector<std::string> sentences;
    std::string::iterator firstSent = sourceStr.begin();
    
    // Loop the all characters to store each sentence
    for(std::string::iterator it = sourceStr.begin(); it != sourceStr.end(); ++it)
    {
        // Catch new-line
        if((*it) == '\n')
        {
            std::string sentence(firstSent, it);
            sentences.push_back(sentence);
            
            firstSent = it + 1; // Ignore the next white-space
        }
        // Catch the end
        else if(it + 1 == sourceStr.end())
        {
            std::string sentence(firstSent, it + 1);
            sentences.push_back(sentence);
        }
    }
    
    // Loop the all found sentence
    for(std::vector<std::string>::iterator it = sentences.begin(); it != sentences.end(); ++it)
    {
        CCLog("=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=");
        
        int charLeng = (*it).length();
        
        CCLog("Origin Sentence : %s", (*it).c_str());
        
        std::string editedStr = "";
        std::string restStr = *it;
        while(charLeng > maxCharLeng)
        {
            std::string::iterator endIt = restStr.begin();
            int spaceLeng = maxCharLeng;
            
            endIt += maxCharLeng;   // Serch from the max char length
            while ((*endIt) != ' ') // To find the first met sapce char
            {
                // No Space Char!
                if(restStr.begin() == endIt)
                    return sourceStr;
                
                --endIt;
                --spaceLeng;
            }
            
            std::string tempStr(restStr.begin(), endIt);
            tempStr.insert(tempStr.end(), '\n');
            editedStr.insert(editedStr.length(), tempStr.c_str());
            
            restStr   = std::string(endIt + 1, restStr.end());
            charLeng -= (spaceLeng + 1);
            ++endIt; // Remove space char
        }
        
        editedStr += restStr; // Last string
        CCLog("Edited Sentence : %s", editedStr.c_str());
        
        // Ignore the first sentence
        if(sentences.begin() != it)
            outputStr.insert(outputStr.end(), '\n');
        
        // Add the edited sentence
        outputStr.insert(outputStr.length(), editedStr.c_str());
        
        CCLog("=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=");
    }
    
    CCLog("Edited Discription : %s", outputStr.c_str());
    
    return outputStr;
}

const unsigned int UtilFunc::utf8ToHexNum(const unsigned char symbol[])
{
    return (symbol[0] * 16777216) + (symbol[1] * 65536) + (symbol[2] * 256) + symbol[3];
}

std::string UtilFunc::replaceEmojiToSqare(const std::string targetStr)
{
    std::string output;
    
    std::string validStr;
    utf8::replace_invalid(targetStr.begin(), targetStr.end(), std::back_inserter(validStr), 9633);
    
    char* str   = (char*)validStr.c_str();    // utf8 string
    char* str_i = str;                        // string iterator
    char* end   = str + std::strlen(str) + 1; // end iterator
    
    unsigned char symbol[5] = {0, 0, 0, 0, 0};
    
    BQ_INDEPENDENCE_LOG_DEBUG("UtilFunc", "replaceEmojiToSqare : %s", targetStr.c_str());
    
    do
    {
        BQ_INDEPENDENCE_LOG_DEBUG("UtilFunc", "----------------------------------------");
        
        uint32_t code = utf8::next(str_i, end);
        if(code == 0)
            continue;
        
        BQ_INDEPENDENCE_LOG_DEBUG("UtilFunc", "code : %u", code);
        utf8::append(code, symbol);
        
        BQ_INDEPENDENCE_LOG_DEBUG("UtilFunc", "replaceEmojiToSqare : %3u %3u %3u %3u %3u",
                                  symbol[0], symbol[1], symbol[2], symbol[3], symbol[4]);
        
        // Distingush
        if(isEmoji(symbol))
            output += "□";
        else
            output += ccsf("%c%c%c%c", symbol[0], symbol[1], symbol[2], symbol[3], symbol[4]);
        
        // Clean
        symbol[0] = '\0';
        symbol[1] = '\0';
        symbol[2] = '\0';
        symbol[3] = '\0';
        symbol[4] = '\0';
        
    } while(str_i < end);
    
    return output;
}

const bool UtilFunc::isEmoji(const unsigned char symbol[])
{
    if(symbol[0] != '\0' && symbol[1] != '\0' && symbol[2] != '\0')
    {
        // emoticons
        if(symbol[0] == '\xF0' && symbol[1] == '\x9F')
            return true;
        
        // unkown
        if(symbol[0] == '\xF3' && symbol[1] == '\xBE')
            return true;
    }
    
//    if((symbol[0] == '\xE2' || symbol[0] == '\xF0' || symbol[0] == '\xF3' || symbol[0] == '\xE3')
//       && (symbol[0] != '\0' && symbol[1] != '\0' && symbol[2] != '\0'))
//        return true;
    
//    const unsigned char emoticonsF[4] = {'\xF0', '\x9F', '\x98', '\x80'};
//    const unsigned char emoticonsL[4] = {'\xF0', '\x9F', '\x99', '\x8F'};
//    
//    const unsigned int numEmoF = utf8ToHexNum(emoticonsF);
//    const unsigned int numEmoL = utf8ToHexNum(emoticonsL);
//
//    if(numEmoF <= symbol && symbol <= numEmoL)
//        return true;
//    
//    const unsigned char dingbatsF[4] = {'\xE2', '\x9C', '\x82', '\0'};
//    const unsigned char dingbatsL[4] = {'\xE2', '\x9E', '\xB0', '\0'};
//    
//    const unsigned char transportAMapF[4] = {'\xF0', '\x9F', '\x9A', '\x80'};
//    const unsigned char transportAMapL[4] = {'\xF0', '\x9F', '\x9B', '\x85'};
//    
//    const unsigned char enclosedCharF[4] = {'\xF0', '\x9F', '\x85', '\x80'};
//    const unsigned char enclosedCharL[4] = {'\xF0', '\x9F', '\x89', '\x91'};
//    
//    const unsigned char uncategorizedF[4] = {'\xE2', '\x84', '\xA2', '\0'};
//    const unsigned char uncategorizedL[4] = {'\xE2', '\xAD', '\x95', '\0'};
//    
//    const unsigned char uncategorizedF[4] = {'\xE3', '\x80', '\xB0', '\0'};
//    const unsigned char uncategorizedL[4] = {'\xE3', '\x8A', '\x99', '\0'};
//    
//    const unsigned char uncategorizedF[4] = {'\xF0', '\x9F', '\x80', '\x84'};
//    const unsigned char uncategorizedL[4] = {'\xF0', '\x9F', '\x97', '\xBF'};
//    
//    const unsigned char otherAdditionalSymbolsF[4] = {'\xF0', '\x9F', '\x8C', '\x8D'};
//    const unsigned char otherAdditionalSymbolsL[4] = {'\xF0', '\x9F', '\x95', '\xA7'};
    
    return false;
}

.
.
.

~~~