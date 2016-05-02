---
layout:     post
title:      "Unity3d-粒子光环"
subtitle:   "ParticleRing"
date:       2016-05-02 20:01:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

这次要使用unity3d仿制[这个网站](http://i-remember.fr/en)上的光环的效果：

![effect](/img/unity3d/particle_ring/webSiteEffect.png)

1.要使用粒子系统，第一步就是创建一个粒子系统。

![p_system](/img/unity3d/particle_ring/particleSystem.png)

2.粒子移动的轨迹，我们使用数学方法实现，在update中每帧更新粒子的位置。因此将粒子的初速度设为0，并把emission Rate设为0，因为希望粒子有不同的大小，因此设置粒子的初始大小在一个范围内随机产生。

![p_setting](/img/unity3d/particle_ring/particleSetting.png)

3.我们只需要确定半径，就可以将粒子放置在圆的轨道上，随机产生一个角度就可以将粒子随机放置在轨道上

![1](/img/unity3d/particle_ring/1.png)

4.新建C#脚本，并获取粒子系统，设置发射数目，新建例子，设置初始位置，给粒子系统设置粒子

{% highlight c++ %}

    public ParticleSystem particleSystem;
    private ParticleSystem.Particle[] particleRing;
    public int particleNum = 200;
    public float radius = 5.0f;

    void Start () {
        particleRing = new ParticleSystem.Particle[particleNum];
        particleSystem.maxParticles = particleNum;
        particleSystem.Emit(particleNum);
        particleSystem.GetParticles(particleRing);
        for (int i = 0; i < particleNum; i++) {

            float angle = Random.Range(0.0f, 360.0f);
            float rad = angle / 180 * Mathf.PI;
            particleRing[i].position = new Vector3(radius * Mathf.Cos(angle), radius * Mathf.Sin(angle), 0.0f);
        }
        particleSystem.SetParticles(particleRing,particleNum);
    }

{% endhighlight %}

效果如图：

![effect1](/img/unity3d/particle_ring/effect1.png)

5.将粒子从圆轨道扩展为圆环轨道

![ring](/img/unity3d/particle_ring/2.png)

{% highlight c++ %}

    public ParticleSystem particleSystem;
    private ParticleSystem.Particle[] particleRing;
    public int particleNum = 200;
    public float radius = 5.0f;
    public float maxRadius = 8.0f;
    // Use this for initialization
    void Start () {
        particleRing = new ParticleSystem.Particle[particleNum];
        particleSystem.maxParticles = particleNum;
        particleSystem.Emit(particleNum);
        particleSystem.GetParticles(particleRing);
        for (int i = 0; i < particleNum; i++) {
            float r = Random.Range(radius, maxRadius);
            float angle = Random.Range(0.0f, 360.0f);
            float rad = angle / 180 * Mathf.PI;
            particleRing[i].position = new Vector3(r * Mathf.Cos(angle), r * Mathf.Sin(angle), 0.0f);
        }
        particleSystem.SetParticles(particleRing,particleNum);
    }

{% endhighlight %}

效果如下：

![effect2](/img/unity3d/particle_ring/effect2.png)

6.让粒子运动起来（仔细观察网站的运动效果，其实是很多部分的粒子，每一部分的粒子的运动是一致的，旋转的角度，顺时针还是逆时针）：

{% highlight c++ %}

//在初始化的时候记录下粒子的半径和角度
particleAngle[i] = angle;
particleR[i] = r;

//update
    void Update () {
        //设置为5部分的粒子，能被2整除的部分逆时针旋转，否则顺时针
        for(int i = 0;i < particleNum; i++) {
            if(i%2 == 0) {
                particleAngle[i] += (i % level+1) * speed;
            } else {
                particleAngle[i] -= (i % level+1) * speed;
            }
            particleAngle[i] = particleAngle[i] % 360;
            float rad = particleAngle[i] / 180 * Mathf.PI;
            particleRing[i].position = new Vector3(particleR[i] * Mathf.Cos(rad), particleR[i] * Mathf.Sin(rad), 0f);
        }
        particleSystem.SetParticles(particleRing, particleNum);
    }

{% endhighlight %}

效果如图：

![](/img/unity3d/particle_ring/1.gif)

7.现在的问题在于粒子的分布非常平均，仔细观察网站上的光环，效果是在圆环中部的粒子更多，在内圈和外圈的是游离的粒子。所以，我们要做的是将内外的部分粒子分布在中心，我们更改一下半径的随机函数。

{% highlight c++ %}

float midR = (maxRadius + radius)/2;
//最小半径随机扩大
float rate1 = Random.Range(1.0f, midR / radius);
//最大半径随机缩小
float rate2 = Random.Range(midR / maxRadius, 1.0f);
float r = Random.Range(radius*rate1, maxRadius*rate2);

{% endhighlight %}

效果如图：

![effect3](/img/unity3d/particle_ring/effect3.png)

8.将粒子大小调为0.05-0.15，粒子数调为10000，最大半径调为10.0

![3](/img/unity3d/particle_ring/2.gif)

9.收缩效果:实现思路：记录收缩后的位置和收缩前的位置，在每一个update里根据按下的按钮调节每一个粒子的半径。

{% highlight c++ %}

//收缩前和后位置的初始化
particleR[i] = r;
circleR[i] = r;
collectR[i] = r - 1.5f * (r / radius);
if (collectR[i] < radius+0.5f) {
    float midRadius = radius + 0.25f;
    float minRate = Random.Range(1.0f, midRadius / radius);
    collectR[i] = Random.Range(radius*minRate, (radius + 0.5f));
}
//触发函数
public void Collect() {
    collect = true;
}
public void outCollect() {
    collect = false;
}

{% endhighlight %}

在update的循环里加入判断

{% highlight c++ %}

    void Update () {

        for(int i = 0;i < particleNum; i++) {
            if (collect == true) {
                if (particleR[i] > collectR[i]) {
                    particleR[i] -= collectSpeed *(particleR[i]/collectR[i]) * Time.deltaTime;
                }
            } else {
                if (particleR[i] < circleR[i]) {
                    particleR[i] += collectSpeed * (circleR[i] / particleR[i]) * Time.deltaTime;
                }else if(particleR[i] > circleR[i]) {
                    particleR[i] = circleR[i];
                }
            }
    }

{% endhighlight %}

最终效果：

![](/img/unity3d/particle_ring/4.gif)

{% highlight c++ %}

//Particle_ring.cs
using UnityEngine;
using System.Collections;

public class Particle_ring : MonoBehaviour {
    public ParticleSystem particleSystem;
    private ParticleSystem.Particle[] particleRing;
    public int particleNum = 10000;
    public float radius = 5.0f;
    public float maxRadius = 10.0f;
    public int level = 5;
    private float[] particleAngle;
    private float[] particleR;
    private float[] collectR;
    private float[] circleR;
    float collectSpeed = 2.0f;
    private bool collect = false;
    public float speed = 0.1f;

    void Start () {
        particleRing = new ParticleSystem.Particle[particleNum];
        particleAngle = new float[particleNum];
        particleR = new float[particleNum];
        collectR = new float[particleNum];
        circleR = new float[particleNum];
        particleSystem.maxParticles = particleNum;
        particleSystem.Emit(particleNum);
        particleSystem.GetParticles(particleRing);
        for (int i = 0; i < particleNum; i++) {
            float midR = (maxRadius + radius)/2;
            //最小半径随机扩大
            float rate1 = Random.Range(1.0f, midR / radius);
            //最大半径随机缩小
            float rate2 = Random.Range(midR / maxRadius, 1.0f);
            float r = Random.Range(radius*rate1, maxRadius*rate2);
            float angle = Random.Range(0.0f, 360.0f);
            particleAngle[i] = angle;
            particleR[i] = r;
            circleR[i] = r;
            collectR[i] = r - 1.5f * (r / radius);
            if (collectR[i] < radius+0.5f) {
                float midRadius = radius + 0.25f;
                float minRate = Random.Range(1.0f, midRadius / radius);
                collectR[i] = Random.Range(radius*minRate, (radius + 0.5f));
            }
            float rad = angle / 180 * Mathf.PI;
            particleRing[i].position = new Vector3(r * Mathf.Cos(angle), r * Mathf.Sin(angle), 0.0f);
        }
        particleSystem.SetParticles(particleRing,particleNum);
    }
    
    
    void Update () {
        //设置为5部分的粒子，能被2整除的部分逆时针旋转，否则顺时针
        for(int i = 0;i < particleNum; i++) {
            if (collect == true) {
                if (particleR[i] > collectR[i]) {
                    particleR[i] -= collectSpeed *(particleR[i]/collectR[i]) * Time.deltaTime;
                }
            } else {
                if (particleR[i] < circleR[i]) {
                    particleR[i] += collectSpeed * (circleR[i] / particleR[i]) * Time.deltaTime;
                }else if(particleR[i] > circleR[i]) {
                    particleR[i] = circleR[i];
                }
            }
            if (i%2 == 0) {
                particleAngle[i] += (i % level+1) * speed;
            } else {
                particleAngle[i] -= (i % level+1) * speed;
            }
            particleAngle[i] = particleAngle[i] % 360;
            float rad = particleAngle[i] / 180 * Mathf.PI;
            particleRing[i].position = new Vector3(particleR[i] * Mathf.Cos(rad), particleR[i] * Mathf.Sin(rad), 0f);
        }
        particleSystem.SetParticles(particleRing, particleNum);
    }

    public void Collect() {
        collect = true;
    }
    public void outCollect() {
        collect = false;
    }
}


{% endhighlight %}

    FIN 5.2/23.50

