---
title: "LineTranslator.cpp"
permalink: /scripts/jj-line-translator.cpp/
layout: splash
excerpt: "LINE: JoJo's Bizarre Adventure SS"
---

***

~~~cs
//
//  LineTranslator.cpp
//  Taro
//
//  Created by Funnygloo on 2015. 12. 15..
//  Minsu Kang
//

#include "LineTranslator.hpp"

#include "LanguageManager.hpp"
#include "PersonalDatabase.h"
#include "TutorialDatabase.h"
#include "StartScene.h"
#include "UserDeckData.h"

#if defined(BQ_ARCH_ANDROID)
#include <base/platform/android/BQ_android.h>
#endif

LineTranslator::LineTranslator(void)
{
#if defined(BQ_ARCH_ANDROID)
    m_pJavaVm = BQ_android_get_VM();
    m_pJavaVm->GetEnv((void**)&m_pJniEnv, JNI_VERSION_1_4);
    
    m_pJClass = new jclass();
    *m_pJClass = m_pJniEnv->FindClass("com/linecorp/LGJOTW/LGJOTW");
#endif
}

LineTranslator::~LineTranslator(void)
{
#if defined(BQ_ARCH_ANDROID)
    delete m_pJClass;
    m_pJavaVm->DestroyJavaVM();
#endif
    
    if(m_pInstance != NULL)
        delete m_pInstance;
}

.
.
.

void LineTranslator::lineLoginWithoutView(bool isLine)
{
#if defined(BQ_ARCH_ANDROID)
    JavaVM* vm = BQ_android_get_VM();
    JNIEnv* env;
    vm->GetEnv((void**)&env, JNI_VERSION_1_4);
    
    jclass cls = env->FindClass("com/linecorp/LGJOTW/LGJOTW");
    jmethodID mid = env->GetStaticMethodID(cls, "lineLoginWithoutView", "(Z)V");
    env->CallStaticVoidMethod( cls, mid, (jboolean)isLine);
#endif
}

void LineTranslator::lineSetUUID(std::string uuid)
{
#if defined(BQ_ARCH_ANDROID)
    JavaVM* vm = BQ_android_get_VM();
    JNIEnv* env;
    vm->GetEnv((void**)&env, JNI_VERSION_1_4);
    
    jclass cls = env->FindClass("com/linecorp/LGJOTW/LGJOTW");
    jmethodID mid = env->GetStaticMethodID(cls, "lineSetUUID", "(Ljava/lang/String;)V");
    env->CallStaticVoidMethod( cls, mid, env->NewStringUTF(uuid.c_str()));
#endif
}

void LineTranslator::lineDeleteAuthInfo(bool isLine)
{
#if defined(BQ_ARCH_ANDROID)
    JavaVM* vm = BQ_android_get_VM();
    JNIEnv* env;
    vm->GetEnv((void**)&env, JNI_VERSION_1_4);
    
    jclass cls = env->FindClass("com/linecorp/LGJOTW/LGJOTW");
    jmethodID mid = env->GetStaticMethodID(cls, "lineDeleteAuthInfo", "(Z)V");
    env->CallStaticVoidMethod( cls, mid, (jboolean)isLine);
#endif
}

.
.
.

void LineTranslator::setLineGamePush(bool isLine)
{
#if defined(BQ_ARCH_ANDROID)
    JavaVM* vm = BQ_android_get_VM();
    JNIEnv* env;
    vm->GetEnv((void**)&env, JNI_VERSION_1_4);
    
    jclass cls = env->FindClass("com/linecorp/LGJOTW/LGJOTW");
    jmethodID mid = env->GetStaticMethodID(cls, "setLineGamePush", "(Z)V");
    env->CallStaticVoidMethod( cls, mid, (jboolean)isLine);
#endif
}

const std::string LineTranslator::getProductsInfo(std::string productsID)
{
#if defined(BQ_ARCH_ANDROID)
    JavaVM* vm = BQ_android_get_VM();
    JNIEnv* env;
    vm->GetEnv((void**)&env, JNI_VERSION_1_4);

    jclass cls = env->FindClass("com/linecorp/LGJOTW/LGJOTW");
    jmethodID mid = env->GetStaticMethodID(cls, "lineGetProductsInfo", "(Ljava/lang/String;)Ljava/lang/String;");
    jstring jstr = (jstring)(env->CallStaticObjectMethod(cls, mid, env->NewStringUTF(productsID.c_str())));
    
    const char* returnValue = m_pJniEnv->GetStringUTFChars(jstr, 0);
    std::string output = std::string(returnValue);
    
    m_pJniEnv->ReleaseStringUTFChars(jstr, returnValue);
    
    return output;
#endif
}

void LineTranslator::doInAppPurchase(std::string data)
{
#if defined(BQ_ARCH_ANDROID)
    JavaVM* vm = BQ_android_get_VM();
    JNIEnv* env;
    vm->GetEnv((void**)&env, JNI_VERSION_1_4);
    
    jclass cls = env->FindClass("com/linecorp/LGJOTW/LGJOTW");
    jmethodID mid = env->GetStaticMethodID(cls, "lineDoInAppPurchase", "(Ljava/lang/String;)V");
    env->CallStaticVoidMethod(cls, mid, env->NewStringUTF(data.c_str()));
#endif
}

void LineTranslator::networkDisconnectedMessageBox(void)
{
#if defined(BQ_ARCH_ANDROID)
    JavaVM* vm = BQ_android_get_VM();
    JNIEnv* env;
    vm->GetEnv((void**)&env, JNI_VERSION_1_4);
    
    jclass cls = env->FindClass("com/linecorp/LGJOTW/LGJOTW");
    jmethodID mid = env->GetStaticMethodID(cls, "networkDisconnectedMessageBoxOnUiThread", "()V");
    env->CallStaticVoidMethod( cls, mid);
#endif
}

void LineTranslator::closeAppMessageBox(void)
{
#if defined(BQ_ARCH_ANDROID)
    JavaVM* vm = BQ_android_get_VM();
    JNIEnv* env;
    vm->GetEnv((void**)&env, JNI_VERSION_1_4);
    
    jclass cls = env->FindClass("com/linecorp/LGJOTW/LGJOTW");
    jmethodID mid = env->GetStaticMethodID(cls, "closeAppMessageBoxOnUiThread", "()V");
    env->CallStaticVoidMethod( cls, mid);
#endif
}

const std::string LineTranslator::callReturnString(const char* functionName)
{
#if defined(BQ_ARCH_ANDROID)
    jmethodID mid = m_pJniEnv->GetStaticMethodID(*m_pJClass, functionName, "()Ljava/lang/String;");
    jstring jstr = (jstring)(m_pJniEnv->CallStaticObjectMethod(*m_pJClass, mid));
    
    const char* chars = m_pJniEnv->GetStringUTFChars(jstr, 0);
    std::string returnStr = std::string(chars);
    
    m_pJniEnv->ReleaseStringUTFChars(jstr, chars);
    
    return returnStr;
#endif
}

.
.
.

void LineTranslator::matEventLogLevelAchieved(void)
{
#if defined(BQ_ARCH_ANDROID)
    JavaVM* vm = BQ_android_get_VM();
    JNIEnv* env;
    vm->GetEnv((void**)&env, JNI_VERSION_1_4);
    
    jclass cls = env->FindClass("com/linecorp/LGJOTW/LGJOTW");
    jmethodID mid = env->GetStaticMethodID(cls, "matEventLogLevelAchieved", "()V");
    env->CallStaticVoidMethod( cls, mid);
#endif
}

#if defined(BQ_ARCH_ANDROID)
extern "C"
{
    JNIEXPORT void JNICALL Java_com_linecorp_LGJOTW_LGJOTW_removeUUID(JNIEnv* env, jobject thiz);
    JNIEXPORT void JNICALL Java_com_linecorp_LGJOTW_LGJOTW_resetAllTutorial(JNIEnv* env, jobject thiz);
    JNIEXPORT jint JNICALL Java_com_linecorp_LGJOTW_LGJOTW_getCurrLanguage(JNIEnv* env, jobject thiz);
    
    JNIEXPORT void JNICALL Java_com_linecorp_LGJOTW_LGJOTW_returnToTitleScreenWihtDeckUpdate(JNIEnv* env, jobject thiz);
    JNIEXPORT void JNICALL Java_com_linecorp_LGJOTW_LGJOTW_returnToSkippedTitleScreen(JNIEnv* env, jobject thiz);
};

JNIEXPORT void JNICALL Java_com_linecorp_LGJOTW_LGJOTW_removeUUID(JNIEnv* env, jobject thiz)
{
    BQ_INDEPENDENCE_LOG_DEBUG("LineTranslator", "removeUUID");
    
    // Delete local data
    PersonalDatabase::getInstance()->removeUUID();
}

JNIEXPORT void JNICALL Java_com_linecorp_LGJOTW_LGJOTW_resetAllTutorial(JNIEnv* env, jobject thiz)
{
    BQ_INDEPENDENCE_LOG_DEBUG("LineTranslator", "resetAllTutorial");
    
    TutorialDatabase::getInstance()->resetTutee();
}

JNIEXPORT jint JNICALL Java_com_linecorp_LGJOTW_LGJOTW_getCurrLanguage(JNIEnv* env, jobject thiz)
{
    BQ_INDEPENDENCE_LOG_DEBUG("LineTranslator", "getCurrLanguage : %d", (int)LanguageManager::getInstance()->getLanguage());
    
    return (int)LanguageManager::getInstance()->getLanguage();
}

JNIEXPORT void JNICALL Java_com_linecorp_LGJOTW_LGJOTW_returnToTitleScreenWihtDeckUpdate(JNIEnv* env, jobject thiz)
{
    BQ_INDEPENDENCE_LOG_DEBUG("LineTranslator", "returnToTitleScreenWihtDeckUpdate");
    
    UserDeckData::sharedUserDeckData()->setUpdateNeeded(true);
    cocos2d::CCDirector::sharedDirector()->replaceScene(StartScene::scene());
}

JNIEXPORT void JNICALL Java_com_linecorp_LGJOTW_LGJOTW_returnToSkippedTitleScreen(JNIEnv* env, jobject thiz)
{
    BQ_INDEPENDENCE_LOG_DEBUG("LineTranslator", "returnToSkippedTitleScreen");
    
    StartScene::sFlagSkipFrontStep = true;
    cocos2d::CCDirector::sharedDirector()->replaceScene(StartScene::scene());
}
#endif

LineTranslator* LineTranslator::m_pInstance = NULL;
~~~