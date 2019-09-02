---
title: "UtilFunc.h"
layout: splash
excerpt: "LINE: JoJo's Bizarre Adventure SS"
---

***

~~~cpp
//
//  UtilFunc.h
//  Taro
//
//  Created by mikata shigenori on 13/01/29.
//  Created by Funnygloo on 2015. 12. 15..
//  Minsu Kang
//

#ifndef __ui_test__UtilFunc__
#define __ui_test__UtilFunc__

.
.
.

/*!
 @brief よく使いそうな処理を関数化して皆で便利に使いたい
 */
class UtilFunc
{

.
.
.
    
    // UTF-8
    static std::string cutAndAddEllipsis(const std::string targetStr, const int maxUTF8Leng);
    static std::string cutAndAddNewLine(const std::string targetStr, const int maxCharLeng);
    
    // Emoji
    static std::string replaceEmojiToSqare(const std::string targetStr);
    static const unsigned int utf8ToHexNum(const unsigned char symbol[]);
    static const bool isEmoji(const unsigned char symbol[]);
    
.
.
.
    
private:
    UtilFunc(){}
    virtual ~UtilFunc(){}
};

.
.
.

#endif /* defined(__ui_test__UtilFunc__) */
~~~