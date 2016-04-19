---
layout:     post
title:      "Cocos2d-x createWithTexture"
subtitle:   "cocos2d-x CC_RECT_PIXELS_TO_POINTS"
date:       2016-04-19 16:45:00
categories: cocos2d-x
tags:       cocos2d-x
author:     "SixTeen"
header-img: "img/cocos2dx.png"
---

SpriteFrame::createWithTexture的定义：

{% highlight c++ %}

SpriteFrame* SpriteFrame::createWithTexture(Texture2D *texture, const Rect& rect)
{
    SpriteFrame *spriteFrame = new (std::nothrow) SpriteFrame();
    spriteFrame->initWithTexture(texture, rect);
    spriteFrame->autorelease();
    
    return spriteFrame;
}

bool SpriteFrame::initWithTexture(Texture2D* texture, const Rect& rect)
{
    Rect rectInPixels = CC_RECT_POINTS_TO_PIXELS(rect);
    return initWithTexture(texture, rectInPixels, false, Vec2::ZERO, rectInPixels.size);
}

#define CC_RECT_PIXELS_TO_POINTS(__rect_in_pixels__)                                                                        \
    Rect( (__rect_in_pixels__).origin.x / CC_CONTENT_SCALE_FACTOR(), (__rect_in_pixels__).origin.y / CC_CONTENT_SCALE_FACTOR(),    \
            (__rect_in_pixels__).size.width / CC_CONTENT_SCALE_FACTOR(), (__rect_in_pixels__).size.height / CC_CONTENT_SCALE_FACTOR() )

{% endhighlight %}

可以看到，rectInPixels就是我们从我们的素材中截取的像素,我们输入的rect会经过```CC_RECT_POINTS_TO_PIXELS```的处理，将RECT中的数字转换成像素对应的数字，这个在win32上运行转化前和转化后是没有区别的，但是今天使用win10应用运行的时候，出现了转换后实际截取比原先预料截取要大的情况。要解决这个问题非常简单，我们只需要在使用像素作为单位的时候，将像素RECT转换成对应的RECT，那么之后RECT再次转换成像素RECT就是我们想得到的。

{% highlight c++ %}

auto frame0 = SpriteFrame::createWithTexture(texture, CC_RECT_PIXELS_TO_POINTS(Rect(0, 0, 113, 113)));

{% endhighlight %}

    FIN 4.20/1.31