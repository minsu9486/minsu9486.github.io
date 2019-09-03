---
title: "LineTranslator.h"
permalink: /scripts/jj-line-translator.h/
layout: splash
excerpt: "LINE: JoJo's Bizarre Adventure SS"
---

***

~~~cpp
//
//  LineTranslator.hpp
//  Taro
//
//  Created by Funnygloo on 2015. 12. 15..
//  Minsu Kang
//

#ifndef LineTranslator_h
#define LineTranslator_h

#include "taroJson.h"

#if defined(BQ_ARCH_ANDROID)
#include "jni.h"
#endif

class LineTranslator
{
public:
    LineTranslator(void);
    ~LineTranslator(void);
    
    static LineTranslator* getInstance()
    {if(m_pInstance == NULL) m_pInstance = new LineTranslator(); return m_pInstance;}
    static void releaseInstance()
    {if(m_pInstance == NULL) delete m_pInstance;}
    
    void lineLogin(void);
    void lineLeadLineLogin(void);
    void lineLoginWithoutView(bool isLine);
    void lineVerify(void);
    void lineSetUUID(std::string uuid);
    void lineDeleteAuthInfo(bool isLine);
    void lineShowBoard(std::string category);
    
    const std::string getCdnHost(void);
    const std::string getGameHost(void);

    int getLoginType(void);
    int getBillingCommand(void);
    
    const std::string getLGCorePhase(void);
    const std::string getAccessToken(void);
    const std::string getVerifyToken(void);
    const std::string getMid(void);
    const std::string getDeviceVersion(void);
    
    const std::string getFriends(const int start, const int display);
    const std::string getGameFriends(const int start, const int display);
    
    void setLineGamePush(bool isOn);
    
    // Billing Prossess
    const std::string getProductsInfo(std::string productsID);
    void doInAppPurchase(std::string data);
    
    void networkDisconnectedMessageBox(void);
    void closeAppMessageBox(void);
    
    // Growhty
    void lineGrowthyStart(void);
    void lineGrowthyUserInfo(std::string jsonData, bool isUpdate);
    void lineGrowthyMoneyLog(std::string jsonData);
    void lineGrowthyPlayEndLog(std::string jsonData);
    
    // MAT
    void matEventLogLevelAchieved(void);
    
private:
    const std::string callReturnString(const char* functionName);
    
    static LineTranslator* m_pInstance;
    
#if defined(BQ_ARCH_ANDROID)
    JavaVM* m_pJavaVm;
    JNIEnv* m_pJniEnv;
    jclass* m_pJClass;
#endif
};

#endif /* LineTranslator_hpp */
~~~