---
title: "LanguageManager.cpp"
layout: splash
excerpt: "LINE: JoJo's Bizarre Adventure SS"
---

***

~~~cs
//
//  LanguageManager.cpp
//  Taro
//
//  Created by Funny Gloo on 2015. 12. 7..
//  Minsu Kang
//

#include "LanguageManager.hpp"
#include "LocalUserData.hpp"
#include "GameDefine.h"
#include "CCCommon.h"
#include "cocos2d.h"

LanguageManager::LanguageManager(void)
{
    m_pJsonData = new JSON::Value();
    m_prevLanguage = TL_NONE;
    m_currLanguage = TL_NONE;
}

LanguageManager::~LanguageManager(void)
{
    bisqueBase::Data::yajlJson::ValueMediator::deleteValue(*m_pJsonData);
    delete m_pJsonData;
    m_pJsonData = NULL;
    
    if(m_pInstance != NULL)
        delete m_pInstance;
}

// Unicode typically refers to UTF-16 (for BMP, Basic Multilingual Plane)
void LanguageManager::parseLanguageData(void)
{
    // Already parsed
    if(m_prevLanguage == m_currLanguage)
        return;
    else if(m_prevLanguage != TL_NONE)
    {
        bisqueBase::Data::yajlJson::ValueMediator::deleteValue(*m_pJsonData);
        delete m_pJsonData;
        m_pJsonData = NULL;
        
        m_pJsonData = new JSON::Value();
    }
    
    if(m_currLanguage == TL_ENG)      // us
        JSON::parseFile(*m_pJsonData, "en_out.json");
    else if(m_currLanguage == TL_TAI) // tw
        JSON::parseFile(*m_pJsonData, "zh_out.json");
    else if(m_currLanguage == TL_THA) // th
        JSON::parseFile(*m_pJsonData, "th_out.json");
}

std::string LanguageManager::findText(const char* key) const
{
    return JSON::getString(*m_pJsonData, key);
}

std::string LanguageManager::addLanguageTag(const char* targetName, const int extenLength, const char* us)
{
    if(targetName == NULL || std::strlen(targetName) == 0)
    {
        BQ_INDEPENDENCE_LOG_WARNING("LanguageManager", "addLanguageTag : No Target Name [%s]",
                                    targetName);
#if defined(DEBUG) || defined(BUILD_PRE)
        cocos2d::CCMessageBox(ccsf("addLanguageTag : The name is EMPTY! [%s]", targetName),
                              "Invalid TargetName!");
#endif
        return targetName;
    }
    
    std::string tagedFileName(targetName);
    std::string extension(targetName);

    // Copy only get the name except the extension
    tagedFileName.erase(tagedFileName.end() - extenLength, tagedFileName.end());
    // Copy only the extension
    extension.erase(extension.begin(), extension.end() - extenLength);
    
    // Add the language tag
    if(m_currLanguage == TL_TAI)
        tagedFileName.insert(tagedFileName.length(), "_tw");
    else if(m_currLanguage == TL_THA)
        tagedFileName.insert(tagedFileName.length(), "_th");
    else
        tagedFileName.insert(tagedFileName.length(), us);

    // Add the extension
    tagedFileName.insert(tagedFileName.size(), extension);
    
    return tagedFileName;
}

std::string LanguageManager::subLanguageTag(const char* targetName)
{
    if(targetName == NULL || std::strlen(targetName) == 0)
    {
        BQ_INDEPENDENCE_LOG_WARNING("LanguageManager", "subLanguageTag : No Target Name [%s]",
                                    targetName);
#if defined(DEBUG) || defined(BUILD_PRE)
        cocos2d::CCMessageBox(ccsf("subLanguageTag : The name is EMPTY! [%s]", targetName),
                              "Invalid TargetName!");
#endif
        return targetName;
    }
    
    std::string tagedFileName(targetName);
    std::string extension(targetName);
    
    // Copy only get the name except the extension
    tagedFileName.erase(tagedFileName.end() - 4, tagedFileName.end());
    // Copy only the extension
    extension.erase(extension.begin(), extension.end() - 4);
    
    std::string lastThreeChars(tagedFileName);
    lastThreeChars.erase(lastThreeChars.begin(), lastThreeChars.end() - 3);
    
    if(lastThreeChars == "_us" || lastThreeChars == "_tw" || lastThreeChars == "_th")
    {
        std::string unTagedFileName(tagedFileName);
        unTagedFileName.erase(unTagedFileName.end() - 3, unTagedFileName.end());
        unTagedFileName += extension;
        
        return unTagedFileName;
    }
    
    return std::string(targetName);
}

// Because the original word was japanese...
std::string LanguageManager::replaceTime(std::string time)
{
    std::string word(time);
    word.assign(word.end() - 6, word.end() - 3);
    
    std::string number(time);
    
    if(word == "分")
    {
        number.erase(number.end() - 6, number.end());
        
        if(m_currLanguage == TL_TAI)
            word = "分鐘前";
        else if(m_currLanguage == TL_THA)
            word = "นาที";
        else // if(m_currLanguage == TL_ENG)
            word = "m ago";
    }
    else if(word == "間")
    {
        number.erase(number.end() - 9, number.end());
        
        if(m_currLanguage == TL_TAI)
            word = "小時前";
        else if(m_currLanguage == TL_THA)
            word = "ชั่วโมง";
        else // if(m_currLanguage == TL_ENG)
            word = "h ago";
    }
    else //  if(word == "日前")
    {
        number.erase(number.end() - 6, number.end());
        
        if(m_currLanguage == TL_TAI)
            word = "天前";
        else if(m_currLanguage == TL_THA)
            word = "วัน";
        else // if(m_currLanguage == TL_ENG)
            word = "d ago";
    }
    
    return number + word;
}

bool LanguageManager::isDormantUser(std::string time)
{
    std::string word(time);
    std::string number(time);
    
    if(m_currLanguage == TL_TAI)
    {
        word.assign(word.end() - 6, word.end());
        if(word == "天前")
            number.erase(number.end() - 6, number.end());
        else
            return false;
    }
    else if(m_currLanguage == TL_THA)
    {
        word.assign(word.end() - 6, word.end());
        if(word == "วัน")
            number.erase(number.end() - 6, number.end());
        else
            return false;
    }
    else // if(m_currLanguage == TL_ENG)
    {
        word.assign(word.end() - 5, word.end());
        if(word == "d ago")
            number.erase(number.end() - 5, number.end());
        else
            return false;
    }
    
    int restDay = cocos2d::CCString::createWithFormat("%s", number.c_str())->intValue();
    if(restDay < 30)
        return false;
    else
        return true;
}

void LanguageManager::setLanguage(kTagLanguage tag)
{
    m_prevLanguage = m_currLanguage;
    m_currLanguage = tag;
}

LanguageManager* LanguageManager::m_pInstance = NULL;
~~~