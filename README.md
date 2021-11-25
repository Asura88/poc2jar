## 2021.11.25更新v0.46
增加druid未授权漏洞利用，可以查看到jdbc链接、数据库用户名、sql语句、访问的uri、SESSION值
![image](https://user-images.githubusercontent.com/48286013/143398014-b8641072-5503-4437-a270-af172fe81d15.png)
简单说下为什么要收集这些信息，jdbc的数据库名称、用户名、密码都有可能是一样的，这里收集也是为了站点的信息收集；

sql语句的一些变量可以收集起来进行fuzz，针对同一类开发团队来说，以及一些路由的可能语句；

uri信息这里做了jdbc执行次数的降序排列，也就是最上面的可能就是系统执行sql语句的路由，可以重点看下；

SESSION值，大家应该都知道，看SESSION的有效性了；

发现python模块下的脚本可能会未响应，后续增加脚本的延时跳出未响应状态

## 2021.11.20更新v0.45

1.base64解码优化，遇到奇数编码会报错，由于没有处理好报错问题，导致程序出错，优化如下：

（如该种解码只出现了三种情况，其他情况报错忽略）

![image](https://user-images.githubusercontent.com/48286013/142726745-70f9dad9-9bc2-4c50-b4e9-d720f8e96d40.png)


## 2021.11.19更新v0.44
1.base64模块解码加入分段猜解，类似base64编码中可能存在部分非base64的加密部分，剔除后进行base64解密。参考(https://xz.aliyun.com/t/7779)

测试base64：
RuYW1lPVRfWlBHTF9ER1dSU0pHWlNYJklzaGFzWVBYSEJ5ZmlsZWQ9JnNlbGVjdGZpZWxkSWRzPSwmc2VsZWN0ZmllbGRJZHNWYWx1ZT0sY29td2lzZWR1Y29td2lzZWR1c3VibWl0PXRydWUmeXB4aHZhbHVlPTI3OTQ2Jn
![image](https://user-images.githubusercontent.com/48286013/142590124-db8eaa2f-bf6c-4601-b7eb-907f62d72788.png)
![image](https://user-images.githubusercontent.com/48286013/142590146-3eabbe4a-7c0a-4800-b24f-68df4694ada4.png)


2.优化cs payload，可以进行按钮保存，方便之后打开使用
![image](https://user-images.githubusercontent.com/48286013/142590183-eb315244-1907-4cce-bb1b-ac9dfead1f44.png)



## 2021.11.19更新v0.43
优化windows下的启动参数，由于mac下java默认是utf-8，windows下java默认是gbk

mac下
![image](https://user-images.githubusercontent.com/48286013/142573620-2a6d9e60-d655-43e9-9312-a72e1766511a.png)


windows下

![image](https://user-images.githubusercontent.com/48286013/142574501-f7bbfb0c-d681-4b3f-96c2-1fe9fd799088.png)


导致有师傅提了issue，这里思考了一下，把文本文档的编码更改一下应该就行了，windows下双击打开jar：
![image](https://user-images.githubusercontent.com/48286013/142574409-9cafa494-1028-4b7c-a4a7-b75cf4c67a36.png)

## 2021.11.18更新v0.42

1.增加命令模块(Bash模块、PowerShell模块、Python模块、Perl模块)，参考http://www.jackson-t.ca/runtime-exec-payloads.html

Bash模块
![image](https://user-images.githubusercontent.com/48286013/142406058-949d8ee4-538e-4775-bd41-57a7c36668da.png)

PowerShell模块
![image](https://user-images.githubusercontent.com/48286013/142406094-b8772cab-d54c-4396-8ec5-8eaa360cc6c0.png)

Python模块
![image](https://user-images.githubusercontent.com/48286013/142406118-9bb318f4-5a2f-4d0b-96fc-40e949e7b398.png)

Perl模块
![image](https://user-images.githubusercontent.com/48286013/142406136-487292be-a95d-4c66-9b37-f371ef7b26d1.png)

2.增加cs快速生成命令模块，第二三四条可以绕过windows defender的上线命令，第一条只是做了加号处理
![image](https://user-images.githubusercontent.com/48286013/142406303-fbc4170c-0010-404e-9726-7a16289b24cf.png)

第五条效果不好
![image](https://user-images.githubusercontent.com/48286013/142406408-d288b0df-e455-4c07-9c53-414662623962.png)


3.优化windows下体验（由于没在windows下测试，导致windows之前的jar都打不开，0.42开始，windows和mac都会进行一次测试）

~~todo:base64模块解码加入猜解，参考(https://xz.aliyun.com/t/7779)~~

## 2021.11.17更新v0.41

1.优化listview选择列表事件，可通过上下方向键进行选择，

由于使用方向键可以移动光标位置，所以可以通过监听onKeyReleased事件来实现，具体实现逻辑如下：

添加监听事件：

```java
onKeyReleased="#mListView1Click2"
```

事件定义为：

```java
public void mListView1Click2(KeyEvent event)
```

函数为：

```java
mListView1.getSelectionModel().getSelectedItem()
```

通过读取光标来进行获取listview选中的内容

2.去除了文件中会显示.DS_store文件

3.增加druid1.0.16版本及以后的解密方式
![image](https://user-images.githubusercontent.com/48286013/142714061-706efac4-ef57-4eef-b2e0-6b3bd1c5e713.png)


4.增加编码模块(Unicode、URL、Base64、Hex、Html、ascii编码)，由于平时编码工具很少，要么是burp的编码模块，很难用，要么是某些在线网站，在线网站进行编码，需要有互联网，就导致了在一些内网环境中无法进行编码、解码，所以写了进去，简单介绍下：

Unicode模块(遇见一些网页响应包里的无法转码，可以尝试这个模块)
![image](https://user-images.githubusercontent.com/48286013/142168767-f1c1d0a0-bd5e-4cf6-ac81-614ad2097729.png)
![image](https://user-images.githubusercontent.com/48286013/142168787-4cecf2f2-a438-441a-9284-1e2023568e0d.png)

URL模块(有些在URL里以百分号存在的字符，可以尝试这个模块)
![image](https://user-images.githubusercontent.com/48286013/142168842-636decd2-8274-40b7-94cc-71cf3310a6a3.png)
![image](https://user-images.githubusercontent.com/48286013/142168853-52ebffb1-db60-4bb0-90a8-6b4df9351cc5.png)

Base64模块(形如base64格式的，可以尝试这个模块)
![image](https://user-images.githubusercontent.com/48286013/142168894-7646ae1c-49bd-43c9-a4d3-55d5ea38de32.png)
![image](https://user-images.githubusercontent.com/48286013/142168912-696026a1-062c-435a-87fb-d18adf56696c.png)

Hex模块
![image](https://user-images.githubusercontent.com/48286013/142168964-9fdefedb-2aac-4f82-ae2a-eaee5769ccf7.png)
![image](https://user-images.githubusercontent.com/48286013/142168988-c4a3c648-bfa6-4f80-8650-a004dd31490b.png)

Html模块(形如&#格式的，可以尝试这个模块)
![image](https://user-images.githubusercontent.com/48286013/142169035-04363225-dc93-4723-81ba-93df392d441b.png)
![image](https://user-images.githubusercontent.com/48286013/142169056-c8e3587f-044c-4bdb-909f-a246afac9d8d.png)

ascii模块(一般是在SQL注入中会用到，取ascii码进行比对)
![image](https://user-images.githubusercontent.com/48286013/142169215-d5441ec3-2c00-4fd4-8ea5-8cc3a800fe35.png)
![image](https://user-images.githubusercontent.com/48286013/142169093-f7fe18f0-8cfe-4002-bd9b-619abecaf93d.png)



## 2021.11.15更新 v0.4
1.完善密码模块对druid密码的解密，在druid 1.0.16版本及之前
![image](https://user-images.githubusercontent.com/48286013/141799695-c135ea10-7a0c-4276-bc9c-4ecbec16e465.png)
发现有些时候poc利用的还不是很好，emmm在思考怎么优化

## 2021.8.14更新 v0.3
1.完善对于单个目标进行全部poc检测
![image](https://user-images.githubusercontent.com/48286013/129446750-8814104e-5916-4efe-b56a-763faa6fc711.png)

2.python运行命令可在调用python脚本手动设置。

3.将python固定至jar包内部，减少config.properties文件；将test.txt固定至jar包内部，减少test.txt文件(对macos来说，windows还未适配)

后续看看windows怎么减少config.properties文件和test.txt文件，文件说明在最下面都有解释

基本已经完成我所需要的功能了，后续看有必要再次更新

## 2021.8.8更新 v0.2
1.批量模块更改为python批量，默认用的python3，所以需要配置一下python3命令。(emmm后续再看看改成go试试)

2.添加finalshell密码解密

3.添加seeyon数据库密码解密


注：

1.增加了pythonexp/poc2jarpiliang.py、url.txt文件，最好重新下载一遍项目里的txt

2.property/config.properties增加python2、python3，python脚本默认python2执行
### python批量
![image](https://user-images.githubusercontent.com/48286013/128634771-d1ddc92d-16f3-46da-ba62-3976baf4d6cc.png)

### finalshell密码解密(支持批量)
![image](https://user-images.githubusercontent.com/48286013/128634782-af2dd0a8-5673-4614-9f9f-cd9e717b3de0.png)

### seeyon数据库密码解密(支持批量)
![image](https://user-images.githubusercontent.com/48286013/128634792-cc3121e2-941f-4b6c-9aff-35e0f02734cf.png)


# 源介绍
# poc2jar
个人写的很简陋的poc工具，每次遇到新的漏洞爆发出来，都要去复现，复现好以后保存到哪里就成了一个问题了，写这个工具，就是为了解决这个问题，希望能集合到个人主机内，然后可以根据不同的poc来使用

工具介绍


# poc保存模块
举一个很简单的例子，hikvision的漏洞
![image](https://user-images.githubusercontent.com/48286013/124343105-9baccf00-dbfb-11eb-9032-75a4f538ac9d.png)
截取到数据包如下：
```
GET /onvif-http/snapshot?auth=YWRtaW46MTEK HTTP/1.1
Host:x.x.x.x
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:89.0) Gecko/20100101 Firefox/89.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Connection: keep-alive
Upgrade-Insecure-Requests: 1
```


直接放入到poc保存模块里，进行发包
![image](https://user-images.githubusercontent.com/48286013/124343125-d57dd580-dbfb-11eb-8359-6eaa389a9b83.png)
可以看到返回的为二进制的图片，那我们可以把漏洞关键字设置为Content-Type:image/jpeg，只要请求这个路径返回这个，就判断为存在漏洞

![image](https://user-images.githubusercontent.com/48286013/124343262-c3506700-dbfc-11eb-8701-ea5afeb3379c.png)

满足条件，可以保存为yml文件

填入exp名称、yml文件的名称(这里名称需要带入组件，如这里应该为hikvision/test.yml)
![image](https://user-images.githubusercontent.com/48286013/124343275-d6633700-dbfc-11eb-8ccc-c3f74bbf54fc.png)
至此，保存yml文件成功


# exp利用模块
直接选择刚刚保 存的yml文件
![image](https://user-images.githubusercontent.com/48286013/124343279-ebd86100-dbfc-11eb-8d91-256e228f0fc3.png)

输入刚刚的url，进行测试
![image](https://user-images.githubusercontent.com/48286013/124343292-fdba0400-dbfc-11eb-8c61-851a8f73860f.png)
存在相应回显即为存在漏洞

## 批量利用（这里多线程没有处理好，暂时不能太多目标，后期想更换为python多线程）
![image](https://user-images.githubusercontent.com/48286013/124343398-b8e29d00-dbfd-11eb-97de-4f2a9448ef0b.png)



# tasklist进程搜索模块
这里需要tasklist /svc格式的输入
![image](https://user-images.githubusercontent.com/48286013/124343033-14f7f200-dbfb-11eb-8e23-221be6cb7b52.png)


# 常用命令模块
![image](https://user-images.githubusercontent.com/48286013/124342992-d7936480-dbfa-11eb-8a42-06d04e410e95.png)


# python利用模块
![image](https://user-images.githubusercontent.com/48286013/124342827-7c14a700-dbf9-11eb-8750-07a2f2886ef7.png)


## 不足 todo
~~①.对于一些复杂请求的没有做到兼容测试，还是依赖于python模块去执行~~ 暂时没找到好方法替代

~~②.对于多线程没有处理好~~ 用python替代了

~~③.对于单个目标可以把对应模块的poc全部测一遍~~ 

④.所需要的文件有点多，考虑办法不需要文件，直接写进jar包内 2021.8.14(部分完成)

⑤.考虑和goby、xray格式相同(长久来看)

## 对文件的说明
①.poc文件夹存放poc文件，即yml文件

②.property文件夹存放cmdlists.txt，即常用命令模块；config.properties，即python路径，或者环境变量下python的调用命令；exetest.txt，即tasklist /svc查找的敏感进程；test.txt是保存的请求包

③.pythonexp文件夹存放的python脚本，usage放在了文件第一行，可以进行调用

## 安装说明
安装pyyaml库 5.3.1


## 运行

### Mac OS
直接运行jar即可

### Windows

直接运行jar即可

~~需要在命令行加入参数编码参数，否则会乱码，即java -jar -Dfile.encoding=utf-8 poc2jar.jar~~


## 编写过程的难点
其实真正的难点在于发请求，尤其是发POST请求，但是没有Content-type这样的漏洞payload的时候，有些漏洞请求是要POST，但是Content-type是不需要的，这就导致了该项目当时停滞不前，遇到一位大佬帮我解决问题，真的太感谢了，此处艾特董神
主要在于setIfNotSet自动加上了Content-type，所以我们重写这个方法
下面给出解决的demo

![image](https://user-images.githubusercontent.com/48286013/129446994-8390f4a3-e8de-4278-9573-82fe20e9974e.png)

test类下定义一个setIfNotSet方法
```
  public synchronized void setIfNotSet(String arg0, String arg1) {
//        System.out.println("hook: " + arg0);
      if ("Content-type".equals(arg0)) {
      return;
  }

      if ("Connection".equals(arg0)) {
      return;
  }

      if ("Accept".equals(arg0)) {
      return;
  }

      super.setIfNotSet(arg0, arg1);
}
```

继承test类
```
HttpURLConnection connection = (HttpURLConnection) realUrl.openConnection();
// 设置通用的请求属性

Object target = connection;
if (!target.getClass().equals(sun.net.www.protocol.http.HttpURLConnection.class)) {
    //https
    Field field1 = connection.getClass().getDeclaredField("delegate");
    field1.setAccessible(true);
    target = field1.get(connection);
}
Field field2 = sun.net.www.protocol.http.HttpURLConnection.class.getDeclaredField("requests");
field2.setAccessible(true);
test customMessageHeader = new test();
```


# 免责声明
请勿将本项目技术或代码应用在恶意软件制作、软件著作权/知识产权盗取或不当牟利等非法用途中。实施上述行为或利用本项目对非自己著作权所有的程序进行数据嗅探将涉嫌违反《中华人民共和国刑法》第二百一十七条、第二百八十六条，《中华人民共和国网络安全法》《中华人民共和国计算机软件保护条例》等法律规定。本项目提及的技术仅可用于私人学习测试等合法场景中，任何不当利用该技术所造成的刑事、民事责任均与本项目作者无关。
