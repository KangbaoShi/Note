# viewBinding

[viewBinding视图绑定]('https://developer.android.com/topic/libraries/view-binding')

## 简介

viewBinding是一种视图绑定插件，目的在于简化findViewById()这种繁琐的寻找对应元素的方式。

## 优势

- **更快的编译速度**：视图绑定不需要处理注释，因此编译时间更短。
- **易于使用**：视图绑定不需要特别标记的 XML 布局文件，因此在应用中采用速度更快。在模块中启用视图绑定后，它会自动应用于该模块的所有布局。

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

## viewBinding在Adapter中使用

Fruit.kt

```
package com.skb.listviewtest

class Fruit(val name: String, val ImageId: Int)

```

FruitAdapter.kt

```kotlin
package com.skb.listviewtest

import android.app.Activity
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.ArrayAdapter
import android.widget.ImageView
import android.widget.TextView
import com.skb.listviewtest.databinding.FruitItemBinding

class FruitAdapter(activity : Activity, val resourceId : Int, data: List<Fruit>) : ArrayAdapter<Fruit>(activity, resourceId, data) {

    private lateinit var binding : FruitItemBinding
    override fun getView(position: Int, convertView: View?, parent: ViewGroup): View {

        binding = FruitItemBinding.inflate(LayoutInflater.from(context), parent, false)
        val fruitImage : ImageView = binding.fruitImage
        val fruitName : TextView = binding.fruitName
        val fruit = getItem(position)   // 获取当前Fruit实例
        if (fruit != null) {
            fruitImage.setImageResource(fruit.ImageId)
            fruitName.text = fruit.name
        }
        return binding.root
    }
}
```

fruit_item.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <ImageView
        android:id="@+id/fruitImage"
        android:layout_width="40dp"
        android:layout_height="40dp"
        android:layout_gravity="center_vertical"
        android:layout_marginLeft="10dp"/>

    <TextView
        android:id="@+id/fruitName"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_vertical"
        android:layout_marginLeft="10dp"/>
</LinearLayout>
```

## viewBinding在RecycleView使用

Fruit.kt

```
class Fruit(val name : String, val ImageId : Int)
```

FruitAdapter.kt

```kotlin
class FruitAdapter(private val fruitList:List<Fruit>) : RecyclerView.Adapter<FruitAdapter.ViewHolder>() {


    inner class ViewHolder(private val itemBinding: FruitItemBinding) :RecyclerView.ViewHolder(itemBinding.root) {
        fun bind(fruit: Fruit) {
            itemBinding.fruitImage.setImageResource(fruit.ImageId)
            itemBinding.fruitName.text = fruit.name
        }

    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): FruitAdapter.ViewHolder {
        val binding = FruitItemBinding.inflate(LayoutInflater.from(parent.context), parent, false)
        return ViewHolder(binding)
    }

    override fun onBindViewHolder(holder: FruitAdapter.ViewHolder, position: Int) {
        val fruit = fruitList[position]
        holder.bind(fruit)
    }

    override fun getItemCount() = fruitList.size

}
```

fruit_item.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <ImageView
        android:id="@+id/fruitImage"
        android:layout_width="40dp"
        android:layout_height="40dp"
        android:layout_gravity="center_vertical"
        android:layout_marginLeft="10dp"/>

    <TextView
        android:id="@+id/fruitName"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_vertical"
        android:layout_marginLeft="10dp"/>
</LinearLayout>
```

