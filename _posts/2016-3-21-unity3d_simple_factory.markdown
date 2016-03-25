---
layout:     post
title:      "Unity3d学习--简单工厂"
subtitle:   "单例模式 简单工厂"
date:       2016-3-21 23:49:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/solvingReport.jpg"
published:   true
---

unity3d中的动作简单工厂，动作简单工厂的作用是给一个游戏对象添加用户希望的动作组件，通过```AddComponent<>()```来以组件的形式给对象添加动作，动作完成之后会自动销毁，这样我们可以通过简单的动作组合得到更复杂的动作，例如一个复杂动作的实质可以是一个简单动作完成之后再进行一个简单动作。

![action_manger.png](/img/unity3d/Action_Manager.png)

从图里可以看到，ActionManager是一个单例工厂，单例模式可以方便我们随时获得这个类的实例而不用每个脚本中都新建一个工厂实例。这个单例工厂实现了IU3dActionCompleted接口，IU3dActionCompleted接口中的方法是一个回调方法，在动作完成之后都会调用这个方法，然后工厂中关联了多个U3dAction，这些action就是我们工厂加工的组件。U3dAction又继承自MonoBehaviour，这样才能作为一个脚本组件挂载在游戏对象上。然后U3dAction又有多个子类，我们可以把动作分成多个类别。


### 1.完成工厂单例和接口：

{% highlight c++ %}

namespace com.MyAction{
    public interface IU3dActionCompleted{
        void onCompleted ();
    }
    public class Action_Manager:System.Object{
        private static Action_Manager action_manager = null;
        public static Action_Manager getInstance(){
            if (action_manager == null) {
                action_manager = new Action_Manager();
            }
            return action_manager;
        }
        
    }
}

{% endhighlight %}

### 2.完成动作类：

{% highlight c++ %}

namespace com.MyAction.ex{
    public class U3dAction:MonoBehaviour{}
    public class U3dActionAuto:U3dAction{}
    public class U3dActionman:U3dAction{}
}

{% endhighlight %}

### 3.添加新动作：

{% highlight c++ %}

public class MoveToAction :  U3dActionAuto {
        public Vector3 target;
        public int speed;
        private IU3dActionCompleted monitor = null;
        public void setting(Vector3 target, int speed, IU3dActionCompleted monitor){
            this.target = target;
            this.speed = speed;
            this.monitor = monitor;
        }
        void Update () {
            float step = speed * Time.deltaTime;
            transform.position = Vector3.MoveTowards(transform.position, target, step);
            //动作完成后回调完成函数
            if (transform.position == target) {
                if (monitor != null) {
                    monitor.OnActionCompleted(this);
                }
                Destroy(this);
            }
        }
    }

{% endhighlight %}

### 4.在单例中添加加装组件的函数

{% highlight c++ %}

public U3dAction ApplyMoveToAction(GameObject obj, Vector3 target, int speed, IU3dActionCompleted completed){
            MoveToAction ac = obj.AddComponent <MoveToAction> ();
            ac.setting (target, speed, completed);
            return ac;
        }

{% endhighlight %}

### 5.在脚本中使用：(让脚本继承接口，这样就可以将脚本自身作为参数，通过接口多态实现自定义的回调函数)

{% highlight c++ %}

public class u3dAction : MonoBehaviour,IU3dActionCompleted {
    public Vector3 vc;
    public GameObject cube;
    void Start(){
        Action_Manager.getInstance ().ApplyMoveToAction (cube, vc, 1, this);
    }
    private void OnActionCompleted(){
        print ("done");
    }
}

{% endhighlight %}


下面是上课课后作业写过的一个例子：

{% highlight c++ %}

//U3dActions.cs
using UnityEngine;
using System.Collections;
using Com.Mygame;

namespace Com.Mygame {
    public class ActionManager :System.Object {
        private static ActionManager _instance;
        //获得单例
        public static ActionManager GetInstance(){
            if (_instance == null) {
                _instance = new ActionManager();
            }
            return _instance;
        }
        //给obj挂载MoveToAction，并初始化MoveToAction
        public U3dAction ApplyMoveToAction(GameObject obj, Vector3 target, int speed, IU3dActionCompleted completed){
            MoveToAction ac = obj.AddComponent <MoveToAction> ();
            ac.setting (target, speed, completed);
            return ac;
        }
        //重载
        public U3dAction ApplyMoveToAction(GameObject obj, Vector3 target, int speed) {
            return ApplyMoveToAction (obj, target, speed, null);
        }
        //新添加MoveToTargetAction的应用方法
        public U3dAction ApplyMoveToTargetAction(GameObject obj, GameObject target,int speed, IU3dActionCompleted completed){
            MoveToTargetAction ac = obj.AddComponent<MoveToTargetAction> ();
            ac.setting (target, speed, completed);
            return ac;
        }
        //新添加MoveToAB_Action
        public U3dAction ApplyMoveToAB_Action(GameObject obj,Vector3 targetA,Vector3 targetB,int speed,IU3dActionCompleted completed){
            MoveToAB_Action ac = obj.AddComponent<MoveToAB_Action> ();
            ac.setting (targetA, targetB, speed, completed);
            return ac;
        }
        //新添加MoveToTargetAB_Action
        public U3dAction ApplyMoveToTargetAB_Action(GameObject obj, GameObject targetA, GameObject targetB,int speed, IU3dActionCompleted completed){
            MoveToTargetAB_Action ac = obj.AddComponent<MoveToTargetAB_Action> ();
            ac.setting (targetA, targetB, speed, completed);
            return ac;
        }
    }

    //异常
    public class U3dActionException : System.Exception {}
    //接口
    public interface IU3dActionCompleted {
        void OnActionCompleted (U3dAction action);
    }
    //超类
    public class U3dAction : MonoBehaviour {
        public void Free(){}
    }
    public class U3dActionAuto : U3dAction {}
    public class U3dActionMan : U3dAction {}

    //MoveToAction
    public class MoveToAction :  U3dActionAuto {
        public Vector3 target;
        public int speed;
        //完成动作时用于调用完成函数
        private IU3dActionCompleted monitor = null;
        
        public void setting(Vector3 target, int speed, IU3dActionCompleted monitor){
            this.target = target;
            this.speed = speed;
            this.monitor = monitor;
        }
        
        void Update () {
            float step = speed * Time.deltaTime;
            transform.position = Vector3.MoveTowards(transform.position, target, step);
            
            if (transform.position == target) { // Auto Destroy After Completed
                if (monitor != null) {
                    monitor.OnActionCompleted(this);
                }
                Destroy(this);
            }
        }
    }
    //MoveToTargetAction
    public class MoveToTargetAction:U3dActionAuto{
        public GameObject target;
        public int speed;

        private IU3dActionCompleted monitor = null;

        public void setting(GameObject target, int speed, IU3dActionCompleted monitor){
            this.target = target;
            this.speed = speed;
            this.monitor = monitor;
        }

        void Update () {
            float step = speed * Time.deltaTime;
            transform.position = Vector3.MoveTowards(transform.position, target.transform.position, step);
            if (transform.position == target.transform.position) { // Auto Destroy After Completed
                if (monitor != null) {
                    monitor.OnActionCompleted(this);
                }
                Destroy(this);
            }
        }
    }
    //MoveToAB_Action
    public class MoveToAB_Action:U3dActionAuto{
        public Vector3 targetA;
        public Vector3 targetB;
        public int speed;

        private IU3dActionCompleted monitor = null;

        public void setting(Vector3 targetA,Vector3 targetB, int speed, IU3dActionCompleted monitor){
            this.targetA = targetA;
            this.targetB = targetB;
            this.speed = speed;
            this.monitor = monitor;
        }
        void Start(){
            MoveToAction ac = gameObject.AddComponent<MoveToAction> ();
            ac.setting (targetA, speed, monitor);
        }

        void Update () {
            if (transform.position == targetB) {
                if (monitor != null) {
                    monitor.OnActionCompleted(this);
                }
                MoveToAction ac = gameObject.AddComponent<MoveToAction> ();
                ac.setting (targetA, speed, monitor);
            }
            else if (transform.position == targetA) {
                if (monitor != null) {
                    monitor.OnActionCompleted(this);
                }
                MoveToAction ac = gameObject.AddComponent<MoveToAction> ();
                ac.setting (targetB, speed, monitor);
            }
        }
    }
    //MoveToTargetAB_Action
    public class MoveToTargetAB_Action:U3dActionAuto{
        public GameObject targetA;
        public GameObject targetB;
        public int speed;
        
        private IU3dActionCompleted monitor = null;
        
        public void setting(GameObject targetA,GameObject targetB, int speed, IU3dActionCompleted monitor){
            this.targetA = targetA;
            this.targetB = targetB;
            this.speed = speed;
            this.monitor = monitor;
        }
        void Start(){
            MoveToTargetAction ac = gameObject.AddComponent<MoveToTargetAction> ();
            ac.setting (targetA, speed, monitor);
        }
        
        void Update () {
            if (transform.position == targetB.transform.position) {
                if (monitor != null) {
                    monitor.OnActionCompleted(this);
                }
                MoveToTargetAction ac = gameObject.AddComponent<MoveToTargetAction> ();
                ac.setting (targetA, speed, monitor);
            }
            else if (transform.position == targetA.transform.position) {
                if (monitor != null) {
                    monitor.OnActionCompleted(this);
                }
                MoveToTargetAction ac = gameObject.AddComponent<MoveToTargetAction> ();
                ac.setting (targetB, speed, monitor);
            }
        }
    }
    
}

public class U3dActions : MonoBehaviour, IU3dActionCompleted {
    
    public GameObject cube;
    public GameObject x;
    
    public void OnActionCompleted (U3dAction action){}
    
    // Use this for initialization
    void Start () {
        ActionManager.GetInstance ().ApplyMoveToAction (cube, x.transform.position,1,this);
        
    }
    
    // Update is called once per frame
    void Update () {
        
    }
}


{% endhighlight %}