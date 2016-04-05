---
layout:     post
title:      "Unity3d-打飞碟"
subtitle:   " "
date:       2016-04-03 11:21:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

最终效果：

![shootFly_v1](/img/unity3d/shootFly/finish.png)

总共有6个文件：Base.cs,action.cs,factory.cs,GameModel.cs,judge.cs,UserInterface.cs

1.<strong>judge.cs</strong>：负责的是管理各种规则（变量），以及round更新之后，各种规则（变量）变化的函数。

{% highlight c++ %}

//judge.cs
using UnityEngine;
using System.Collections;

namespace com.MyJudge{
    public class Judge{
        //决定总共的轮数
        //决定每一轮的飞碟个数
        //每一个飞碟的得分
        public int totalRound = 5;
        public int totalFly = 10;
        public int getPoint = 10;
        //飞碟发射位置
        public Vector3 flypos = new Vector3(-3,-2,-10);
        //飞碟发射方向
        public Vector3 flyDir = Vector3.one;
        //飞碟发射速度
        public float flyspeed = 5f;
        //飞碟发射数目
        public int flynum = 1;
        //子弹速度
        public float bulletspeed = 200f;
        //新关卡更新属性
        public void updateRound(){
            //flypos = new Vector3 (-2, 1, -10);
            flyDir = Vector3.one;
            flyspeed += 1f;
            getPoint += 10;
        }
    }
}

public class judge : MonoBehaviour {}

{% endhighlight %}


2.<strong>factory.cs</strong>：负责的是飞碟的管理和生产的单例简单工厂。

在管理飞碟方面，使用一个列表来存放所有的飞碟。之前使用的方法是使用的是2个列表，一个用于存放已使用飞碟，一个用于存放未使用飞碟。这一次作业中，使用id来管理，并使用gameobject中的activeInHierarchy来判断是否可用。根据id来管理的好处非常的多，速度必然是提升的，其次是没有了我之前作业中的频繁的列表增删操作，使用id只有简单的读取和对对象属性设置的操作。

{% highlight c++ %}

//factory.cs
using UnityEngine;
using System.Collections;
using System.Collections.Generic;
using com.MyShootFly;
using com.MyFactory;

namespace com.MyFactory{
public class DiskFactory:System.Object{
    //单例
    public static DiskFactory diskFactory = null;
    //飞碟存放列表
    private List<GameObject> diskList = new List<GameObject>();
    //预设设置对象
    public GameObject disk;
    public static DiskFactory getInstance(){
        if (diskFactory == null) {
            diskFactory = new DiskFactory();
        }
        return diskFactory;
    }
    //返回可使用id
    public int GetDisk(){
        //判断是否存在空闲飞碟
        for (int i = 0; i < diskList.Count; i++) {
            if(!diskList[i].activeInHierarchy){
                diskList[i].SetActive(true);
                return i;
            }
        }
        //没有空闲飞碟，使用预设创建一个新的飞碟,并加上刚体组件
        GameObject obj = GameObject.Instantiate (disk) as GameObject;
        obj.AddComponent<Rigidbody> ();
        diskList.Add (obj);
        return diskList.Count - 1;
    }
    //根据id返回相应的飞碟
    public GameObject GetDiskObject(int id){
        return diskList [id];
    }
    //根据id来释放飞碟，重复利用
    public void Free(int id){
        Rigidbody rb = diskList [id].GetComponent<Rigidbody> ();
        rb.velocity = Vector3.zero;
        diskList [id].transform.position = new Vector3(0,1,-9);
        rb.velocity = Vector3.zero;
        diskList [id].SetActive (false);
    }
}
}
public class factory : MonoBehaviour {
    public GameObject diskTemp;
    // Use this for initialization
    void Start () {
        DiskFactory.getInstance ().disk = diskTemp;
    }
}


{% endhighlight %}

3.<strong>action.cs</strong>:是一个负责给对象加装动作的简单单例工厂，子弹出膛后的回收动作，子弹与飞碟的触发动作，飞碟落地回收的动作，都由动作工厂进行加工装配

{% highlight c++ %}

//action.cs
using UnityEngine;
using System.Collections;
using com.MyFactory;
using com.MyShootFly;
using com.MyAction;
using com.MyJudge;
namespace com.MyAction{
    public class ActionManager{
        public static ActionManager actionManager = null;
        public static ActionManager getInstance(){
            if (actionManager == null) {
                actionManager = new ActionManager();
            }
            return actionManager;
        }
        //飞碟回收动作
        public class freeAction:MonoBehaviour{
            public int disk_id;
            public void setting(int id){
                disk_id = id;
            }
            public void Update(){
                if (gameObject.transform.position.y < -12) {
                    DiskFactory.getInstance().Free(disk_id);
                    Destroy(this);
                }
            }
        }
        //子弹碰撞和回收动作
        public class bulletAction:MonoBehaviour{
            private Judge judge;
            //因为使用的是同一颗子弹，因此防止子弹中同时存在多个动作组件造成多次事件触发
            public void Start(){
                if (gameObject.GetComponents<bulletAction> ().Length > 1) {
                    Destroy(this);
                }
            }
            public void Update(){
                if (gameObject.transform.position.y < -12) {
                    gameObject.transform.position = new Vector3 (0, -1, -11);
                    gameObject.rigidbody.velocity = Vector3.zero;
                    Destroy(this);
                }
            }
            public void OnTriggerEnter(Collider collider){
                SceneController.getInstance ().addScore (judge);
                gameObject.transform.position = new Vector3 (0, -1, -11);
                gameObject.rigidbody.velocity = Vector3.zero;
                int id = collider.gameObject.GetComponent<freeAction>().disk_id;
                DiskFactory.getInstance().Free(id);
                Destroy(this);
            }
            public void setting(Judge j){
                judge = j;
            }
        }
        public void ApplyBulletAction(GameObject bullet,Judge judge){
            bulletAction ba = bullet.AddComponent<bulletAction> ();
            ba.setting (judge);
        }
        public void ApplyFlyAction(int id,Vector3 flyDir,Vector3 flypos,float flySpeed){
            GameObject obj = DiskFactory.getInstance ().GetDiskObject (id);
            obj.transform.position = flypos;
            freeAction fa = obj.AddComponent<freeAction> ();
            fa.setting (id);
            Rigidbody rb = obj.GetComponent<Rigidbody> ();
            rb.AddForce (flyDir*Random.Range(flySpeed,flySpeed*3),ForceMode.Impulse);
        }
    }
}

public class action : MonoBehaviour {}

{% endhighlight %}

4.<strong>UserInterface.cs</strong>:处理用户输入，对用户输出执行相应的函数。

{% highlight c++ %}

//UserInterface.cs
using UnityEngine;
using System.Collections;
using com.MyShootFly;

public class UserInterface : MonoBehaviour {
    public IUserInterface userInterface;
    // Update is called once per frame
    void Start(){
        userInterface = SceneController.getInstance () as IUserInterface;
    }
    void Update () {
        if (Input.GetMouseButtonDown (0)) {
            userInterface.mouseLeftKey();
        }
        if (Input.GetKeyDown ("space")) {
            userInterface.spaceKey();
        }
    }

}


{% endhighlight %}

5.<strong>GameModel.cs</strong>：游戏核心代码，负责显示游戏信息，游戏信息使用ui text来显示，uitext的优点在于他不需要像GUI那样需要在ONGUI中渲染，可以写在update中，而且更加美观，而且调试方便，可以在图形界面上直接观察到并实时调整位置。

![GameModel](/img/unity3d/shootFly/GameModel.png)

{% highlight c++ %}

//GameModel.cs
using UnityEngine;
using System.Collections;
using com.MyFactory;
using com.MyAction;
using com.MyShootFly;
using com.MyJudge;
using UnityEngine.UI;
public class GameModel:MonoBehaviour{
    public Text counting;
    public Text Round;
    public Text Score;
    public GameObject bullet;
    private Rigidbody rb;
    private int count = 3;
    private float countTime = 0f;
    private float lastcountTime = 0f;
    private Judge judge = new Judge();
    // Use this for initialization
    void Awake () {
        //给子弹添加刚体
        rb = bullet.AddComponent<Rigidbody> ();
    }
    public void Update(){
        SceneController sc = SceneController.getInstance ();
        //实时更新回合和分数
        Round.text = "Round:" + sc.getRound ().ToString ();
        Score.text = "Score:" + sc.getScore ().ToString ();
        //如果点击了发射飞碟，那么进行倒计时
        if (sc.counting&&sc.fly == true) {
            countTime += Time.deltaTime;
            counting.text = count.ToString ();
            //每120帧进行一次计数
            if(countTime - lastcountTime > 120*Time.deltaTime){
                lastcountTime = countTime;
                count--;
            }
            //倒数结束时，根据规则产生发射飞碟
            if(count <= 0){
                count = 3;
                sc.launchFly(judge);
                sc.counting = false;
            }
        } else if (!sc.counting) {
            //没有进行倒数时，不显示
            sc.fly = false;
            counting.text = "";
        }
        //点击鼠标左键后进行射击
        if (Input.GetMouseButtonDown (0)&&sc.shooting) {
            shoot ();
        }
        //根据规则不断检查是否符合进入下一轮的条件
        if (sc.init (judge)) {
            judge.updateRound();
        }
    }
    public void shoot(){
        //初始化子弹，将子弹移动到点击处，组装子弹动作，发射
        bullet.transform.position = gameObject.transform.position;
        bullet.rigidbody.velocity = Vector3.zero;
        ActionManager.getInstance ().ApplyBulletAction (bullet,judge);
        Ray ray = Camera.main.ScreenPointToRay (Input.mousePosition);
        rb.AddForce (ray.direction*judge.bulletspeed, ForceMode.Impulse);
    }
}

{% endhighlight %}

6.<strong>Base.cs</strong>：里面包含了接口部分，还有场景控制部分，场景控制主要是根据发射的飞碟数目来调整回合数，记录分数，并且实现用户部分需要使用的接口

{% highlight c++ %}

//Base.cs
using UnityEngine;
using System.Collections;
using System.Collections.Generic;
using com.MyShootFly;
using com.MyFactory;
using com.MyAction;
using com.MyJudge;

namespace com.MyShootFly{
    public class CodeBase{}
    public interface IUserInterface{
        //用户按空格后，321倒数3秒，飞碟飞出（物理引擎控制）
        //点击鼠标，子弹飞出。飞碟落地，或被击中，则准备下一次射击。
        //2个接口
        //空格
        void spaceKey ();
        void mouseLeftKey();
    }
    public interface IQueryGameStatus{
        bool gameStatus ();
    }

    public interface IJudeEvent{}

    public class SceneController:System.Object,IUserInterface,IQueryGameStatus,IJudeEvent{
        public static SceneController sceneController = null;
        public bool shooting = false;
        public bool counting = false;
        public bool fly = false;
        private int Round = 1;
        private int Score = 0;
        private int Trial = 0;
        public GameModel nowGame;
        public float lastShootTime = 0f;
        public float lastCountTime = 0f;

        public static SceneController getInstance(){
            if (sceneController == null) {
                sceneController = new SceneController();
            }
            return sceneController;
        }
        public int getRound(){
            return Round;
        }
        public int getScore(){
            return Score;
        }
        //加分
        public void  addScore(Judge judge){
            Score += judge.getPoint;
        }
        //判断是否应该符合进入下一轮
        public bool init(Judge judge){
            if (Round <= judge.totalRound && Trial == judge.totalFly) {
                //nowGame.setting(judge.flyDir,judge.flypos,judge.flyspeed);
                Round ++;
                Trial = 0;
                return true;
            }
            return false;
        }
        //制作飞碟并组装动作
        public void launchFly(Judge judge){
            DiskFactory df = DiskFactory.getInstance ();
            ActionManager am = ActionManager.getInstance ();
            am.ApplyFlyAction (df.GetDisk (),judge.flyDir,judge.flypos,judge.flyspeed);
        }
        public void spaceKey(){
            counting = true;
            fly = true;
            Trial++;
        }
        public void mouseLeftKey(){
            if (Time.time - lastShootTime > 0.3f) {
                shooting = false;
            }
            if (!shooting) {
                lastShootTime = Time.time;
                shooting = true;
            }
        }
        public bool gameStatus(){
            if (!counting)
                return true;
            else return false;
        }
    }
}

public class Base : MonoBehaviour {}

{% endhighlight %}

总结：1.将规则分离出来。这是非常有意思的，因为这意味着我们在写游戏逻辑部分的时候，可以不用将心思分散在游戏规则上，而且在修改的时候，直接就可以修改，非常方便。2.工厂的使用。已经连续几周的作业用到了工厂。工厂能帮我们管理产生游戏对象，非激活的对象还能暂时存储在缓存中，减少创建的开销，动作工厂，对象工厂分工合作，非常好用。