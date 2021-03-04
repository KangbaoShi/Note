# viewBinding

[viewBinding视图绑定]('https://developer.android.com/topic/libraries/view-binding')

## 简介

viewBinding是一种视图绑定插件，目的在于简化findViewById()这种繁琐的寻找对应元素的方式。

## 获取viewBinding功能

build.gradle

```xml
android {
	...
	viewBinding{
		enabled = true
	}
}
```

## 忽略某个布局

```xml
tools:viewBindingIgnore="true"
```

```
<LinearLayout
	...
	tools:viewBindingIgnore="true" >
	
	...
</LinearLayout>
    
```



## Activity

在Activity使用viewBinding

```kotlin
class MainActivity : AppCompatActivity() {
    private lateinit val binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        binding.button_id.setOnClickListener {
            ...
        }
    }
}
```

kotlin要求所有的变量在声明时需要初始化。而这里我们显然无法对其进行初始化，所以这里使用了lateinit关键字对变量进行延迟初始化。

## viewBinding在自定义布局中使用

TitleLayout.kt

```kotlin
class TitleLayout(context: Context?, attrs: AttributeSet?) : LinearLayout(context, attrs) {


    private var binding : TitleBinding =
        TitleBinding.inflate(LayoutInflater.from(context), this, true)

    init {
        binding.titleBack.setOnClickListener {
            Toast.makeText(context,"TitleBinding", Toast.LENGTH_SHORT).show()
        }
    }
}
```

title.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <Button
        android:id="@+id/titleBack"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_margin="5dp"
        android:text="Back"
        android:textColor="#fff"/>

    <TextView
        android:id="@+id/titleText"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_weight="1"
        android:gravity="center"
        android:text="Title Text"
        android:textColor="#000"
        android:textSize="24sp"/>

    <Button
        android:id="@+id/titleEdit"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_margin="5dp"
        android:text="Edit"
        android:textColor="#fff"/>
</LinearLayout>
```

