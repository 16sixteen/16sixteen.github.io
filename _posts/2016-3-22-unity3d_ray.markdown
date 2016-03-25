---
layout:     post
title:      "Unity3d学习--射线检测"
subtitle:   "射线检测 简单工厂管理"
date:       2016-3-22 20:22:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
published:   true
---

### 射线检测就是从一个点（Vector3）开始沿着一个方向（Vector3）发射一段距离，在轨迹之间与物体发生碰撞时，它就会停止。

{% highlight c++ %}

using UnityEngine;
using System.Collections;

public class ExampleClass : MonoBehaviour {
    void Update() {
        Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
        RaycastHit hit;
        if (Physics.Raycast(ray, out hit, 100))
            Debug.DrawLine(ray.origin, hit.point);   
    }
}

{% endhighlight %}

### 通过射线检测在一个平台上创建对象：

{% highlight c++ %}

void Update () {
        if (Input.GetMouseButtonDown (0)) {
            Ray ray = Camera.main.ScreenPointToRay (Input.mousePosition);
            RaycastHit hit;
            if (Physics.Raycast (ray, out hit,100)) {
                //平台的tag为Finish，射线检测可以通过碰撞物体的tag来分辨碰撞的是什么物体
                if(hit.collider.gameObject.tag == "Finish"){
                    GameObject cy = GameObject.CreatePrimitive (PrimitiveType.Cylinder);
                    cy.transform.position = hit.point;
                }
            }
        }
    }

{% endhighlight %}


### LayerMask的使用：

我们发现这样创建的物体会挡住我们的射线，如果我们想让射线穿越我们创建的物体，这样就要使用到layermask。

LayerMask类型其实是一个int型，我们可以通过```public static int NameToLayer(string s)```和```public static string LayerToName(int i)```来转换。

{% highlight c++ %}

//layermask的用法
1 << 10  打开第10的层。
~(1 << 10) 打开除了第10之外的层。
~(1 << 0) 打开所有的层。
(1 << 10) | (1 << 8) 打开第10和第8的层。

{% endhighlight %}

{% highlight c++ %}

using UnityEngine;
using System.Collections;

public class opacity_control : MonoBehaviour {
    private LayerMask mask;
    // Use this for initialization
    void Start () {
        mask = ~(1 << LayerMask.NameToLayer("cylinder"));
    }
    
    // Update is called once per frame
    void Update () {
        if (Input.GetMouseButtonDown (0)) {
            Ray ray = Camera.main.ScreenPointToRay (Input.mousePosition);
            RaycastHit hit;
            if (Physics.Raycast (ray, out hit,100,mask)) {
                if(hit.collider.gameObject.tag == "Finish"){
                    GameObject cy = GameObject.CreatePrimitive (PrimitiveType.Cylinder);
                    cy.transform.position = hit.point;
                    cy.layer = LayerMask.NameToLayer("cylinder");
                }
            }
        }
    }
}

{% endhighlight %}

这样，射线就会穿越cylinder层，和别的其它层发生碰撞。

![shoot_1](/img/unity3d/shoot_1.png)

### 自动回收

现在考虑让这些标记在一定时间后自动消失

{% highlight c++ %}

public class Disappear:MonoBehaviour{
        void Start(){
            //2秒后销毁游戏对象
            Destroy (gameObject, 2);
        }
    }

{% endhighlight %}

在创建对象的时候添加这个动作组件：

{% highlight c++ %}

cy.AddComponent<Disappear>();

{% endhighlight %}

### 创建与销毁的成本

我必须认识到，从资源创建对象的成本是最高的```GameObject instance = Instantiate(Resources.Load("enemy"));```,克隆的成本是较高的，在存储许可的条件下，缓存常用的对象，例如子弹等等。

我们使用简单工厂来负责创建和管理这些标记，工厂需要做到的工作是，当对象创建之后，2秒之后释放时不是销毁，而是透明化，并放到未使用列表中，当再次需要产生对象时，先检查工厂的未使用列表中有没有可用的，如果有，则恢复透明度，并放置在对应的位置，如果是第一次创建，那么就创建一个新的，否则就使用一个作为克隆模板。

{% highlight c++ %}

using UnityEngine;
using System.Collections;
using System.Collections.Generic;
using com.myFactory;

namespace com.myFactory{
    public class simple_Factory:System.Object{
        private static simple_Factory sf = null;
        public List<GameObject> unused = new List<GameObject>();
        private GameObject temp = null;
        public static simple_Factory getInstance(){
            if (sf == null) {
                sf = new simple_Factory();
            }
            return sf;
        }
        public void placeAttackMark(Vector3 p,LayerMask l){
            GameObject o;
            if (unused.Count != 0) {
                o = unused [0];
                //从未使用列表中移除
                unused.RemoveAt (0);
            } else if (temp != null) {
                //克隆
                o = GameObject.Instantiate (temp) as GameObject;
            } else {
                o = GameObject.CreatePrimitive(PrimitiveType.Cylinder);
                //使用透明材质
                o.renderer.material.shader = Shader.Find("Transparent/Diffuse");
                temp = o;
            }
            //加工，装配回收动作组件
            o.AddComponent<recovery> ();
            //设置位置
            o.transform.position = p;
            //设置layermask
            o.layer = l;
        }
        
        public void reuse(GameObject obj){
            unused.Add (obj);
        }
        
        public class recovery:MonoBehaviour{
            private float lastTime;
            void Start(){
                //初始化时间和透明度
                lastTime = 2.0f;
                Color c = gameObject.renderer.material.color;
                c.a = 255f;
                gameObject.renderer.material.color = c;
            }
            void Update(){
                lastTime -= Time.deltaTime;
                if (lastTime <= 0.0f) {
                    //透明度设为0
                    Color c = gameObject.renderer.material.color;
                    c.a = 0f;
                    gameObject.renderer.material.color = c;
                    //回收该对象并销毁该动作脚本
                    simple_Factory.getInstance().reuse(gameObject);
                    Destroy(this);
                }
            }
        }
        
    }
}


public class opacity_control : MonoBehaviour {
    private LayerMask mask;
    // Use this for initialization
    void Start () {
        mask = ~(1 << LayerMask.NameToLayer("cylinder"));
    }
    
    // Update is called once per frame
    void Update () {
        if (Input.GetMouseButtonDown (0)) {
            Ray ray = Camera.main.ScreenPointToRay (Input.mousePosition);
            RaycastHit hit;
            if (Physics.Raycast (ray, out hit,100,mask)) {
                if(hit.collider.gameObject.tag == "Finish"){
                    //直接调用工厂放置
                    simple_Factory.getInstance().placeAttackMark(hit.point,LayerMask.NameToLayer("cylinder"));
                }
            }
        }
    }

}

{% endhighlight %}


>参考资料：<br/>[[Unity3D]射线碰撞检测+LayerMask的使用 转](http://blog.sina.com.cn/s/blog_947a2cdd0102uz1k.html)

    FIN 12.09 3.25