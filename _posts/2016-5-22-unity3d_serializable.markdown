---
layout:     post
title:      "Unity3d-文本控制版打飞碟"
subtitle:   "序列化 文本控制 json"
date:       2016-05-22 13:16:00
categories: Unity3d
tags:       Unity3d
author:     "SixTeen"
header-img: "img/unity3d.jpg"
published:   true
---

### 序列化

先看看序列化的概念：

>序列化 就是把一个内存对象变为与地址无关的可传输的数据格式，通常是文本格式；反序列化反之。

所谓的内存对象就是我们游戏运行时，保存在内存中的对象，例如我们在运行时产生的类都是保存在内存中的，序列化就是把这些保存在内存中的数据变成可以传输的文本格式，例如json，xml等等。反序列化则是把json，xml等等的传输文本格式重新转化为内存数据。

### 改进打飞碟游戏

我之前的[打飞碟游戏](/unity3d/unity3d_shootFly_v1)，所有和场景有关的数据都保存在judge.cs中的Judge类中，然后在GameModel中实例化Judge类，之后所有需要应用到规则的都使用这个实例中的数据。说到这里，我们的改进方案非常简单：将Judge类变成可序列化的类即可。

#### 序列化的格式：

在需要实例化的类上加上[System.Serializable]即可。

{% highlight c# %}

[System.Serializable]
public class Judge{
	//....
}

{% endhighlight %}

#### 测试

我们在Assets中新建一个文件夹Json，在Json文件夹中新建一个judge.json文件，并加入下面的内容：

{% highlight c# %}

{
"totalRound" : 5,
"totalFly" : 10,
"getPoint" : 10,
"flypos" : {"x":-3,"y":-2,"z":-10},
"flyDir" : {"x":1,"y":1,"z":1},
"flyspeed" : 20.0,
"flynum" : 1,
"bulletspeed" : 200.0
}

{% endhighlight %}

Vector3在Unity引擎里也是序列化的类型，我们使用嵌套的json可以初始化vector3.```"flypos" : {"x":-3,"y":-2,"z":-10}```。

### 修改GameModel

现在我们已经序列化了Judge，以及编写了一个Json文件，我们现在需要做的就是将GameModel中实例化的Judge改成从Json中反序列化。

{% highlight c# %}

	//private Judge judge = new Judge();
	private Judge judge;
    string json;
    private bool completeLoad = false;//是否完成数据的读写

    void loadJson(string url) {
        this.StartCoroutine(getData(url));//使用协程获取数据
    }

    IEnumerator getData(string url) {
        WWW www = new WWW(url);
        yield return www;//成功获取数据后
        json = www.text.ToString();
        judge = JsonUtility.FromJson<Judge>(json);//使用json反序列化一个Judge类
        completeLoad = true;//完成数据读写
    }

	void Awake () {
		//...
	    string jsonUrl = "file://" + Application.dataPath + "/Json/" + "judge.json";
	    loadJson(jsonUrl);//从对应路径中获取json，并实例化judge
	}

	public void Update(){
        if (!completeLoad) { return; }
        //没有完成数据读取则不进行游戏逻辑，因为游戏逻辑中需要用到我们读取的数据
        //...
    }

{% endhighlight %}

### 加入版本号

现在加入一个版本号，用于检测是否需要更新版本。

在Judge类中加入多一个字段：

{% highlight c# %}

    [System.Serializable]
	public class Judge{
        //版本号
        public float version;
        //...
    }

{% endhighlight %}

在界面中加入：

![](/img/unity3d/serializable/1.jpg)

{% highlight c# %}
	
	//点击按钮触发这个检测更新函数
    public void checkUpdate() {
        string checkUrl = "http://localhost:8080/version";
        this.StartCoroutine(updateVersion(checkUrl));
    }

    //通过协程从网站上获取版本号，与本地版本号对比，如果不同，则进行更新
    IEnumerator updateVersion(string url) {
        WWW www = new WWW(url);
        yield return www;
        if(judge.version != www.text.ToString()) {
            completeLoad = false;
            string updateUrl = "http://localhost:8080/Judge";
            loadJson(updateUrl);
        }
    }

{% endhighlight %}

版本号连接的信息：

![](/img/unity3d/serializable/3.jpg)

新版本的详细信息：

![](/img/unity3d/serializable/2.jpg)

最终效果：

![](/img/unity3d/serializable/1.gif)

### 更新本地文件

最后我们要做的就是将更新后的信息保存到本地文件，我们使用协程完成这个工作。更新过程中退出会造成文件损坏，所以加一个文字提示更新结束。

{% highlight c# %}

	using System.IO;

    IEnumerator updateVersion(string url) {
        WWW www = new WWW(url);
        yield return www;
        if(judge.version != www.text.ToString()) {
            completeLoad = false;
            updateFile = true;
            string updateUrl = "http://localhost:8080/Judge";
            loadJson(updateUrl);
            while (!completeLoad) {
                yield return null;
            }//判断是否完成更新，完成更新后写入本地文件
            this.StartCoroutine(writeFile());
        }
    }

    //使用StreamWriter来进行文件覆盖
    IEnumerator writeFile() {
        string jsonUrl = "Assets/Json/" + "judge.json";
        StreamWriter sw;
        sw = new StreamWriter(jsonUrl, false);
        //将新的版本信息转换成json格式的string
        string json = JsonUtility.ToJson(judge);
        //Debug.Log(json);
        //写入流
        sw.WriteLine(json);
        //关闭流
        sw.Close();
        //销毁流
        sw.Dispose();
        yield return null;
        updateFile = false;
    }

    public void Update(){
        if (updateFile) {
            counting.text = "更新中";
        }
    }

{% endhighlight %}

#### 效果

更新版本前：

![](/img/unity3d/serializable/4.png)

更新版本后：

![](/img/unity3d/serializable/5.png)

### 完整的GameModel和Judge

{% highlight c# %}

//judge.cs
using UnityEngine;
using System.Collections;

namespace com.MyJudge{
    [System.Serializable]
	public class Judge{
        //版本号
        public string version;
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

{% highlight c# %}

//GameModel.cs
using UnityEngine;
using System.Collections;
using com.MyFactory;
using com.MyAction;
using com.MyShootFly;
using com.MyJudge;
using UnityEngine.UI;
using System.IO;

public class GameModel:MonoBehaviour{
	public Text counting;
	public Text Round;
	public Text Score;
    public Text Version;
	public GameObject bullet;
	private Rigidbody rb;
	private int count = 3;
	private float countTime = 0f;
	private float lastcountTime = 0f;
	private Judge judge;
    private bool completeLoad = false;
    private bool updateFile = false;


    void loadJson(string url) {
        this.StartCoroutine(getData(url));
    }

    IEnumerator getData(string url) {
        WWW www = new WWW(url);
        yield return www;
        string json = www.text.ToString();
        judge = JsonUtility.FromJson<Judge>(json);
        completeLoad = true;
    }

    void Awake () {
		//给子弹添加刚体
		rb = bullet.AddComponent<Rigidbody> ();
        string jsonUrl = "file://" + Application.dataPath + "/Json/" + "judge.json";
        loadJson(jsonUrl);
	}

    public void checkUpdate() {
        string checkUrl = "http://localhost:8080/version";
        this.StartCoroutine(updateVersion(checkUrl));
    }

    IEnumerator updateVersion(string url) {
        WWW www = new WWW(url);
        yield return www;
        if(judge.version != www.text.ToString()) {
            completeLoad = false;
            updateFile = true;
            string updateUrl = "http://localhost:8080/Judge";
            loadJson(updateUrl);
            while (!completeLoad) {
                yield return null;
            }
            this.StartCoroutine(writeFile());
        }
    }

    IEnumerator writeFile() {
        string jsonUrl = "Assets/Json/" + "judge.json";
        StreamWriter sw;
        sw = new StreamWriter(jsonUrl, false);
        string json = JsonUtility.ToJson(judge);
        Debug.Log(json);
        sw.WriteLine(json);
        sw.Close();
        sw.Dispose();
        yield return null;
        updateFile = false;
    }

	public void Update(){
        if (updateFile) {
            counting.text = "更新中";
        }
        if (!completeLoad) { return; }
		SceneController sc = SceneController.getInstance ();
		//实时更新回合和分数
		Round.text = "Round:" + sc.getRound ().ToString ();
		Score.text = "Score:" + sc.getScore ().ToString ();
        Version.text = "Version:" + judge.version;
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
		bullet.GetComponent<Rigidbody>().velocity = Vector3.zero;
		ActionManager.getInstance ().ApplyBulletAction (bullet,judge);
		Ray ray = Camera.main.ScreenPointToRay (Input.mousePosition);
		rb.AddForce (ray.direction*judge.bulletspeed, ForceMode.Impulse);
	}
}

{% endhighlight %}

	FIN 5.22/18.57

