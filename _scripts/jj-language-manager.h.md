---
title: "LanguageManager.h"
layout: splash
excerpt: "LINE: JoJo's Bizarre Adventure SS"
---

***

~~~cpp
//
//  LanguageManager.hpp
//  Taro
//
//  Created by Funny Gloo on 2015. 12. 7..
//  Minsu Kang
//

#ifndef LanguageManager_hpp
#define LanguageManager_hpp

#include "JSON.h"

class LanguageManager
{
public:
    LanguageManager(void);
    ~LanguageManager(void);
    
    enum kTagLanguage
    {
        TL_NONE,
        TL_THA,     // Thai (th)
        TL_ENG,     // Englush (us)
        TL_TAI      // Chinese (tw)
    };
    
    static LanguageManager* getInstance()
    {if(m_pInstance == NULL) m_pInstance = new LanguageManager(); return m_pInstance;}
    static void releaseInstance()
    {if(m_pInstance == NULL) delete m_pInstance;}
    
    void parseLanguageData(void);
    
    std::string findText(const char* key) const;
    std::string addLanguageTag(const char* targetName, const int extenLength = 4, const char* us = "_us");
    std::string subLanguageTag(const char* targetName);
    std::string replaceTime(std::string time);
    bool isDormantUser(std::string time);
    
    void setLanguage(kTagLanguage tag);
    inline kTagLanguage getLanguage(void) {return m_currLanguage;}
    
private:
    static LanguageManager* m_pInstance;
    
    JSON::Value* m_pJsonData;
    kTagLanguage m_prevLanguage;
    kTagLanguage m_currLanguage;
};

#endif /* LanguageManager_hpp */
~~~