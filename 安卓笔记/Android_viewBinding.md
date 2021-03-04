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
    private lateinit var binding: ActivityMainBinding

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

