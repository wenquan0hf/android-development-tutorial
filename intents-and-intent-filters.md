# Intents 和 Intent Filters

Android 应用中的三个核心组件：Activities，Services 和 broadcast receivers 都是通过称为“Intent”的消息来激活的。Android 应用一个特点是“低耦合”，各个 Activities，Services 和broadcast receivers 相当独立，可以看成是一个个“迷你应用”，而 Intent 是这些“迷你应用”的粘合剂，Intent 不但可以用于同一个 Application 之间 Activities，Services 和 broadcast receivers 的交互，也可以用于不同 Application 之间 Activities，Services 和 broadcast receivers 的交互。
Intent 本身为一个数据载体，可以描述想要执行的操作以及用于这个操作的数据和其它属性。用个容易理解的概念，在访问网站时，我们需要提供网站的URL，有时还需要通过 URL 参数，在 Android 世界里，Intent 的功能类似于 URL，Android 操作系统根据 Intent 来触发对于的Activitives,Services 或是 Broadcast Receivers。

Android 应用中的三个核心组件：Activities，Services 和 broadcast receivers 都是通过Intent 来触发的，当它们触发的机制各不相同，而且不会有重叠，也就是说发给 Activity 的 Intent 不会激活 Service 或是 broadcast receivers，发给 broadcast receivers 的 Intent 也不会触发 Activity 和 Service。

1. Activity 通过方法 Context.startActivity() 和 Activity.startActivityForResult()来调用。以函数调用为参考startActivity() 相当于调用无返回值的函数，startActivityForResult()调用的Activity有返回值，可以通过Activity.setResult()来返回结果。
2.  Context.startService()用来初始化Service，Context.bindService()可以用来建立与目标Service之间的连接，如果Service没有运行，则会启动该Service。
3. Broadcast Receiver是通过  Context.sendBroadcast(), Context.sendOrderedBroadcast(), 和Context.sendStickyBroadcast()来触发的。大部分的Broadcast消息来自于Android操作系统，如电池状态，来电，短消息等。

和URL不太一样的是，URL 和网站一般是一一对应的，而一个 Intent 可以用来触发某个指定的Activity，Service 或是 Broadcast Receiver，或是触发多个满足 Intent 条件的 Activities，Services 或是 Broadcast Receivers。

下面来看看 Android OS 如何根据 Intent 来找到满足触发条件的 Activity，Service 或是Broadcast Receiver.  借用 SQL 数据库的概念可以更好的理解。

```
SELECT (Activitives|Services|Broadcast Receivers) AS Target
     FROM  (List in AndroidManifest.xml)
     WHERE Intent Meet Target’s (Intent Filter)
```

意思就是从 AndroidManifest.xml 中定义的 Activities,Services 和 Broadcast Receiver 列表中查找符合 Intent 条件的 Activities，Services，或是 Broadcast Receivers。所有能活被激活的 Activity，Service 和 Broadcast Receiver 都必须在 AndroidManifest.xml 有定义，否则 Android OS 无法查询到该目标，相当于数据库中无记录，即使你在代码中定义了该Activity，Service 或 Broadcast Receiver。

Intent  
Intent 本身为一个数据载体，可以描述想要执行的操作以及用于这个操作的数据和其它属性。它主要包含下列信息：  
Component name：可以处理该 Intent 的组件名称，组件名称指定义 Activity，Service 的包和类的全名称。比如类名为 com.pstreets.gisengine.AndroidGISEngineTutorial,包名为 com.pstreets.gisengine。 组件名称为可选项，如果指定了，意味明确指定用来响应该 Intent 的 Activity，Service。  
Action: 列出需要执行的操作名称。或者在 Broadcast Intents 的情况下给出发生的事件名称。


|Constant|	Target component|	Action|
|:-------------|:-----------|:---------------------|
|ACTION_CALL|	activity|	开始打电话.|
|ACTION_EDIT|activity	|显示编辑对话框.|
|ACTION_MAIN|	activity	|作为一个任务（应用）的起始 Activity，对于可以从 Android 应用列表的应用来说，都需要在 AndroidManifest.xml 中设置 ACTION_MAIN 的 Intent-Filter 属性。|
|ACTION_SYNC	|activity	|同步数据.|
|ACTION_BATTERY_LOW|	broadcast receiver|	电池电量低告警.|
|ACTION_HEADSET_PLUG|	broadcast receiver	|耳机插入或拔出.|
|ACTION_SCREEN_ON	|broadcast receiver|	屏幕打开或关闭.|
|ACTION_TIMEZONE_CHANGED|	broadcast receiver	|时区变动.|

Data：定义不数据的 URL 以及数据的 MIME 类型。不同的 Action 能够处理的 Data 类型也不一样，比如 ACTION_CALL,它处理的数据格式为 tel: URI，URI 为电话号码。
Category: 定义了可以响应 Intent 的附加信息，一个 Intent 可以指定多个 Category 类型。和 Action 类似，Android 预定义了一些 Category 类型：


|Constant |	Meaning |
|:--------|:-------|
|CATEGORY_BROWSABLE	|表示目标 Activity 可以使用浏览器安全显示指定连接，比如说一个图片或是 Email 消息.|
|CATEGORY_GADGET	|表示该 Activity 可以当作一个 Gadget 嵌入到其它可以放置 Gadget 的 Activity 中。|
|CATEGORY_HOME	|表示还 Activity 是 Home Screen，可以设置这个属性来替换 Android 自带的 Home Screen。
|CATEGORY_LAUNCHER|	该 Activity 可以显示在 Android 程序管理器中。一般应用的主 Activity 都会在 AndroidManifest.xml 定义该属性。|

Extra:  附加 Key-Value 列表，可以向目标 Activity 传送附加参数。可以理解成函数调用时的参数。    
Flags：指出 Android 启动目标 Activity 时的一些选项（比如目标 Activity 隶属于那个应用等）。     

除非是 Intent 明确指定目标（Explicitly）Activity 的类和包名称，这是 Activity 无需在 AndroidManifest.xml 定义 intent-filter，其它情况也叫隐含（Implicit）方式启动目标 Activity，在这种情况下 Android  操作系统查找目标 Activity，Service 或是 Broadcast Receiver 时主要根据 Intent 的 Action，Data 和 Category 属性来匹配定义在 AndroidManifest.xml 中 Activity，Service 或是 Broadcast Receiver 的 Intent Filters。

Intent Filters  

Android 中没个有效的 Activity，Service，Broadcast Receiver 都必须在 AndroidManifest.xml 有对应的定义。除非只使用明确调用发生来启动目标 Activity，每个 Activity 都需要定义一个 intent-filter。下面是是明确指定目标Activity 的示例代码：

```
Intent intent=new Intent(SplashActivity.this,GNavigator.class);
startActivity(intent);
```

而更一般的情况，Activity 在 AndroidManifest.xml 具有如下定义：

```
<activity android:name=”.AndroidGISEngineTutorial”
    android:label=”@string/app_name”>
 <intent-filter>
  <action android:name=”android.intent.action.MAIN” />
  <action android:name=”com.example.project.SHOW_CURRENT” />   
  <action android:name=”com.example.project.SHOW_RECENT” />   
  <action android:name=”com.example.project.SHOW_PENDING” />
  
  ….
  <category android:name=”android.intent.category.DEFAULT” />   
  <category android:name=”android.intent.category.BROWSABLE” />
  …
   <data android:mimeType=”video/mpeg” android:scheme=”http” . . . />    
   <data android:mimeType=”audio/mpeg” android:scheme=”http” . . . />
   ….
 </intent-filter>
</activity>
```

Activity 的 intent-filter 可以包含 action，category，data 子元素，给出了该 Activity 能够处理的 Intent 的Action，Category 和数据类型。Android 操作系统就是根据 Activity 的 intent-filter 来匹配 Intent，从而触发目标Activity，或是 Service，Broadcast Receiver。

最常见的一个 Intent Filter 组合如下：

```
<intent-filter . . . >   
   <action android:name=”code android.intent.action.MAIN” />   
   <category android:name=”code android.intent.category.LAUNCHER” />
  </intent-filter>
```

表示用户可以从 Android 设备的应用程序管理器启动该 Activity，这个 Activity 为应用的主 Activity，主 Activity可以再使用 Intent 触发或是启动其它 Activity。

Tags: [Android](http://www.imobilebbs.com/wordpress/archives/tag/android)




