# Android基础知识

## 四大组件

1. Activity
2. Service
3. BroadcastReceiver
4. ContentProvider

## SQLite数据库

轻量级、运算速度极快嵌入式关系型数据库

## Log日志打印

1. Log.v() 最琐碎的、意义最小的日志信息
2. Log.d() 调试信息
3. Log.i() 比较重要的数据
4. Log.w() 警告信息
5. Log.e() 错误信息

## Activity启动的几种方式

AcivityName 活动名称

显式启动

```kotlin
val intent = Intent(this, ActivityName::class.java)
startActivity(intent)
```

隐式启动

1. AndroidMainfest.xml

   ```xml
   <activity android:name=".ActivityName">
   	<intent-filter>
   		<action android:name="com.example.activitytest.ActivityName"/> // Activity Path
   		<!--1.-->
   		<category android:name="android.intent.category.DEFAULT"/>	// category名称
   		<!--2.-->
   		<!--<category android:name="com.example.activitytest.MY_CATEGORY"/>	// category名称-->
   	</intent-filter>
   </activity>
   ```

   ```kotlin
   val intent = Intent("com.example.activitytest.ActivityName")
   // 2.
   // intent.addCategory("com.example.activitytest.MY_CATEGORY")
   StartActivity(intent)
   ```

2. 调用外部应用程序

   ```kotlin
   val intent = Intent(Intent.ACTION_VIEW)
   intent.data = Uri.parse('https://www.baidu.com')
   startActivity(intent)
   ```

## intent-filter标签

属性

1. android:icon 一个表示父Activity、服务或广播接收器的图标
2. android:label 父组件的用户可读标签
3. android:priority  处理过滤器所描述类型的intent，应该为父组件指定优先级。此属性对Activity和广播接收器都有意义
4. android:order 当多个过滤器匹配应该按什么顺序处理过滤器

必须包含

1. action 在name属性中，声明接受的 Intent 操作

可包含

1. category 在name属性中，声明接收的Intent类别
2. data 线性依赖关系
   1. android:scheme 指定数据协议部分
   2. android:host 指定数据的主机部分
   3. android:port 指定数据的端口部分
   4. android:path 指定主机名和端口之后的部分
   5. android:mimeType 指定可以处理的数据类型

## Activity通信

1. 向下一个Activity传递数据

   ```kotlin
   val data = "Hello NextActivity"
   val intent = Intent(this, NextActivity::class.java)
   intent.putExtra("extra_data", data)
   startActivity(intent)
   ```

   ```kotlin
   val extraData = intent.getStringExtra("extra_data")
   ```

2. 向上一个Actvity传递数据

   ```kotlin
   startActivityForResult (intent : Intent, requestCode : int)
   ```

   ```kotlin
   val intent = Intent()
   intent.putExtra("data_return", "Hello LastActivity")
   setResult(RESULT_OK, intent)
   finish()
   ```

   ```kotlin
   override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
       super.onActivityResult(requestCode, resultCode, data)
       when (requestCode) {
           1 -> if (resultCode == RESULT_OK) {
               val returnedData = data?.getStringExtra("data_return")
               Log.d("LastActivity", "returned data is $returnedData")
           }
       }
   }
   ```

## Activity的生命周期

1. Activity栈

   Android是使用任务(task)来管理Activity的Activity的，一个任务就是一组存放在栈里的Activity的集合，这个栈也被称作返回栈(back stack)。

   <img src="images/Android基础知识/stack.png" alt="img"  width="550" />

2. Activity状态

   每个Activity在其神明周期中最多可能会有4种状态。

   1. 运行状态

      Activity处于栈顶时，系统最不愿回收的Activity。

   2. 暂停状态

      Activity不再处于栈顶位置，对话框形式的Activity在原Activity上。系统也不愿意回收这种Activity。

   3. 停止状态

      当一个Activity不再处于栈顶位置，并且完全不可见的时候，处于停止状态。系统会为其保存相应的状态和成员变量，当前其它地方需要内存时，此Activity可能被系统回收。

   4. 销毁状态

      一个Activity从返回栈种移除后就变成了销毁状态。系统最倾向于回收这种状态的Activity，以保证手机的内存充足。

3. Activity的生存期

   Activity类中定义了**7个回调方法**。

   1. onCreate()

      Acitivity在第一次被创建的时候调用

   2. onStart()

      Activity由不可见变为可见的时候调用

   3. onResume()

      Activity准备好与用户交互的时候调用。此时的Activity一定位于返回栈的栈顶，并处于运行状态。

   4. onPause()

      系统准备去启动或者恢复另一个Activity的时候调用。常在此释放一些消耗CPU的资源，保留一些关键数据，这个方法的执行速度要快，不然会影响到新的栈顶Activity的使用。

   5. onStop()

      Activity完全不可见的时候。它和onPause()方法的主要区别在于，如果启动的新Activity是一个对话框式的Activity，那么onPause()方法会得到执行，而onStop()方法并不会执行。

   6. onDestroy()

      Activity销毁之前调用

   7. onRestart()

      Activity重启时调用，停止状态变为运行状态

4. Activity的生存期

   1. 完整生存期

      Activity在onCreaete()方法和onDestory()方法之间所经历的就是完整的生存期。

      onCreate()完成资源初始化，onDestory()完成释放内存的操作。

   2. 可见生存期

      Activity在onStart()方法和onStop()方法之间所经历的就是可见生存期。

      onStart()进行资源加载，onStop()进行资源释放。

   3. 前台生存期

      Activity在onResume()方法和onPause()方法之间所经历的就是前台生存期。

      Activity总是处于运行状态。
   
   <img src="images/Android基础知识/life.png" alt="img"  width="550" />

## Activity数据保存

Activity采用Bundle保存数据，避免在Activity意外销毁时，数据丢失

```kotlin
override fun onSaveInstanceState(outState: Bundle) {
	super.onSaveInstanceState(outState)
	val tempData = "Something you just typed"
	outState.putString("data_key", tempData)
}
```

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
	super.onCreate(savedInstanceState)
	if (savedInstanceState != null) {
		val tempData = savedInstanceState.getString("data_key")
		Log.d(tag, "tempData is $tempData")
	}
	......
}
```

## Activity启动模式

AndroidManifest.xml中，通过给<activity>标签指定android:launchMode属性来选择启动模式

栈管理

1. standard

   <img src="images/Android基础知识/standard.png" width="550" />

2. singleTop

   <img src="images/Android基础知识/singleTop.png" alt="img" width="550" />

3. singleTask

   <img src="images\Android基础知识\singleTask.png" alt="img"  width="550" />

4. singleInstance

   <img src="images/Android基础知识/singleInstance.png" alt="img"  width="550"/>

##  杀死进程

杀死所有Activity之后，在将自身Kill掉。这段代码只能杀死自己本身。

```kotlin
android.os.Process.killProcess(android.os.Process.myPid())
```

## 布局方式

1. ConstrainLayout 约束性布局

2. LinearLayout 线性布局

   多余一个组件时，要使用orientation设置对齐方式

   

## 组件

1. TextView
2. EditView
3. Button
4. ImageView
5. ProgressBar
6. 

## 组件注册

1. 函数式API注册监听器

   ```kotlin
   class MainActivity : AppCompatActivity() {
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           setContentView(R.layout.activity_main)
           button.setOnClickListener {
               // 在此处添加逻辑
           }
       }
   
   }
   ```

2. 接口方式注册

   ```kotlin
   class MainActivity : AppCompatActivity(), View.OnClickListener {
   
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           setContentView(R.layout.activity_main)
           button.setOnClickListener(this)
       }
   
       override fun onClick(v: View?) {
           when (v?.id) {
               R.id.button -> {
                   // 在此处添加逻辑
               }
           }
       }
   
   }
   ```