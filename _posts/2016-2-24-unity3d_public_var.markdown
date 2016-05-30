---
layout:     post
title:      "Unity3d学习总汇"
subtitle:   " "
date:       2016-2-24 13:38:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

>## 索引：<br/><a href="#01">C#公有变量</a><br/><a href="#02">C#的获取组件</a><br/><a href="#03">C#用代码改变材质</a><br/><a href="#04">Vector3</a><br/><a href="#05">Transform.Translate和Transform.Rotate</a><br/><a href="#06">Time,Random</a><br/><a href="#07">单例模式</a><br/><a href="#08">C# Collection使用</a><br/><a href="#09">双击检测</a><br/><a href="#10">foreach</a><br/><a href="#11">unity3d 角色控制器 卡墙和弹飞问题</a><br/>



<br/><br/><br/><br/>

## <a name="01"/>在C#代码中使用的公有变量可以在Inspector中直接更改

![public_var](/img/unity3d/public_var.png)
![public_var_inspector](/img/unity3d/public_var_inspector.png)
![public_var_change](/img/unity3d/public_var_change.png)

然而这个更改不会更改你的脚本中的变量的值，只改变挂载的那个的值，最终实际应用到游戏的值是inspector上的值（对应的挂载对象）：

![public_var](/img/unity3d/public_var.png)

## <a name="02"/>C#的获取组件

获取对象

```GameObject myGameObject = GameObject.Find("object_name");```

```myGameObject.GetComponent<component_name>()```

## <a name="03"/>C#用代码改变材质

获取材质：```gameObject.renderer.material```
给材质添加颜色： ```gameObject.renderer.material.color = Color.red```

## <a name="04"/>Vector3

|<strong>Static variables</strong>|<strong>Represent</strong>|
|back|Shorthand for writing Vector3(0, 0, -1).|
|down|Shorthand for writing Vector3(0, -1, 0).|
|forward|Shorthand for writing Vector3(0, 0, 1).|
|left|Shorthand for writing Vector3(-1, 0, 0).|
|one|Shorthand for writing Vector3(1, 1, 1).|
|right|Shorthand for writing Vector3(1, 0, 0).|
|up|Shorthand for writing Vector3(0, 1, 0).|
|zero|Shorthand for writing Vector3(0, 0, 0).|

在unity3d中的坐标是:

![vector3_position](/img/unity3d/vector3_position.png)

![vector3_object](/img/unity3d/vector3_object.png)

![vector3_position_1](/img/unity3d/vector3_position_1.png)

one单位向量，zero是（0，0，0）

Vector3里的变量x,y,z分别代表x,y,z轴上的坐标。

## <a name="05"/>transform.Translate和Transform.Rotate

向Vector3这个参数方向移动Vector3长度的距离:

```transform.Translate(Vector3.forward * Time.deltaTime);```

参数作为法向量，按左手法则决定旋转方向：

```transform.Rotate(Vector3.right * Time.deltaTime);```

选转的中心为父节点，如果没有父节点，就自转。

## <a name="06"/>Time & Random 

<strong>Time.deltaTime</strong> The time in seconds it took to complete the last frame (Read Only).


<strong>Range</strong>   Returns a random float number between and min [inclusive] and max [inclusive] (Read Only).

```Random.Range(0,10);//0-9```

## <a name="07"/>单例模式

[单例模式](/algorithm/singleton)

## <a name="08"/>C# Collection使用

[collection使用](/unity3d/unity3d_collection)

## <a name="09"/>双击检测

{% highlight c++ %}
//double_click.cs
using UnityEngine;
using System.Collections;

public class double_click : MonoBehaviour {

    private float doubleClickStart = 0;
    private float doubleClickGap = 0.2f;
    //点击鼠标左键的时候，将doubleClickStart设为当前的时间，
    //如果再次点击的间隔少于doubleclickGap，返回true
    bool doubleClick(){
        if (Input.GetKeyUp (KeyCode.Mouse0)) {
            if (Time.time - doubleClickStart < doubleClickGap) {
                return true;
            }else{
                doubleClickStart = Time.time;
                return false;
            }
        }
        return false;
    }
    // Update is called once per frame
    void Update () {
        if (doubleClick ()) {
            print (1);
        }
    }
}

{% endhighlight %}

## <a name="10"/>foreach

foreach 中不能进行元素的删减

## <a name="11"/>角色控制器 卡墙和弹飞问题

卡墙的问题我们可以用到自带的角色控制器中的AdvanceSettings中的shellOffset来解决。设置为0.1或者以上就可以解决卡墙问题。

{% highlight c# %}

        [Serializable]
        public class AdvancedSettings
        {
            public float groundCheckDistance = 0.01f; // distance for checking if the controller is grounded ( 0.01f seems to work best for this )
            public float stickToGroundHelperDistance = 0.5f; // stops the character
            public float slowDownRate = 20f; // rate at which the controller comes to a stop when there is no input
            public bool airControl; // can the user control the direction that is being moved in the air
            [Tooltip("set it to 0.1 or more if you get stuck in wall")]
            public float shellOffset = 0.1f; //reduce the radius by that ratio to avoid getting stuck in wall (a value of 0.1f is nice)
        }

{% endhighlight %}

弹飞的问题是因为在碰撞后y位置发生改变，我们在rigidbody中锁定y，就可以不让物理效果改变我们的y坐标，锁定的坐标只能由代码来改变。

![freeze_position](/img/unity3d/freeze_position.png)