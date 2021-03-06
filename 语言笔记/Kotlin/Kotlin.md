# Kotlin

## 标准函数

1. [let]('https://kotlinlang.org/docs/scope-functions.html#let')

2. [with]('https://kotlinlang.org/docs/scope-functions.html#with')

   A non-extension function: **the context object** is passed as an argument, but inside the lambda, it's available as a receiver (`this` ). **The return value** is the lambda result.

   We recommend `with` for calling functions on the context object without providing the lambda result. In the code, `with` can be read as “ *with this object, do the following.*”

   ```kotlin
   val result = with(obj) {
       // 这里是obj的上下文
       "value" // with函数的返回值
   }
   ```

   ```kotlin
   val list = listOf("Apple", "Banana", "Orange", "Pear", "Grape")
   val result = StringBuilder().run {
       append("Start eating fruits.\n")
       for (fruit in list) {
           append(fruit).append("\n")
       }
       append("Ate all fruits.")
       toString()
   }
   println(result)
   ```

3. [run]('https://kotlinlang.org/docs/scope-functions.html#run')

   **The context object** is available as a receiver (`this` ). **The return value** is the lambda result.

   `run` does the same as `with` but invokes as `let`- as an extension function of the context object.

   `run` is useful when your lambda contains both the object initialization and the computation of the return value.

   ```kotlin
   val result = obj.run {
       // 这里是obj的上下文
       "value" // run函数的返回值
   }
   ```

   ```kotlin
   val list = listOf("Apple", "Banana", "Orange", "Pear", "Grape")
   val result = StringBuilder().apply {
       append("Start eating fruits.\n")
       for (fruit in list) {
           append(fruit).append("\n")
       }
       append("Ate all fruits.")
   }
   println(result.toString())
   ```

   

4. [apply]('https://kotlinlang.org/docs/scope-functions.html#apply')

   **The context object** is available as a receiver (`this` ). **The return value** is the object itself.

   Use `apply` for code blocks that don't return a value and mainly operate on the members of the receiver object. The common case for `apply` is the object configuration. Such calls can be read as “ *apply the following assignments to the object.*”

   ```kotlin
   val result = obj.apply {
        // 这里是obj的上下文
   }
   // result == obj
   ```

   ```kotlin
   val list = listOf("Apple", "Banana", "Orange", "Pear", "Grape")
   val result = StringBuilder().apply {
       append("Start eating fruits.\n")
       for (fruit in list) {
           append(fruit).append("\n")
       }
       append("Ate all fruits.")
   }
   println(result.toString())
   ```

5. [also]('https://kotlinlang.org/docs/scope-functions.html#also')

   **The context object** is available as an argument (`it` ). **The return value** is the object itself.

   `also` is good for performing some actions that take the context object as an argument. Use `also` for actions that need a reference rather to the object than to its properties and functions, or when you don't want to shadow `this` reference from an outer scope.

   When you see `also` in the code, you can read it as “ *and also do the following with the object.*”

   

## 静态方法

1. 单例类

   ```kotlin
   object Util {
       
       fun doAction() {
           println("do action")
       }
   }
   ```

   单例类写法会将整个类中的所有方法全部变成类似于静态方法调用的方式。

2. companion object伴生类

   ```kotlin
   object Util {
   
   	fun doAction1() {
   		println("do action1")
   	}
   	
   	companion object {
   		fun doAction2() {
   			println("do action2")
   		}
   	}
   }
   ```

   一个类始终只会存在一个伴生类对象。

3. @JvmStatic

   ```kotlin
   object Util {
   	
   	fun doAction1() {
   		println("do action1")
   	}
   	
   	companion object {
   		
   		@JvmStatic
   		fun doAction2() {
   			println("do action2")
   		}
   	}
   }
   ```

   @JvmStatic可以用在单例类或者companion object中，@JvmStatic注解才是真正的静态方法。

4. 顶层方法

   Helper.kt

   ```kotlin
   fun doSomething() {
   	println("do something")
   }
   ```

   Kotlin编译器会将所有的顶层方法全部编译成静态方法。


## 对变量延迟初始化

1. class?

   ```kotlin
   var adapter : MsgAdapter? = null
   ```

2. lateinit

   ```kotlin
   lateinit var adapter : MsgAdapter
   ```

   判空

   ```kotlin
   if (!::adapter.isInitialized) {
   	adapter = MsgAdapter(msgList)
   }
   ```

## 密封类

1. 密封类是可继承的
2. 密封类及其所有子类只能定义在同一个文件顶层位置，不能嵌套在其它类中，这是密封类底层的实现机制所限制的

```kotlin
sealed class Result
class Success(val msg:String) : Result()
class Failure(val error: Exception) : Result()
```

```kotlin
fun getResultMsg(result: Result) = when (result) {
    is Success -> result.msg
    is Failure -> "Error is ${result.error.message}"
}
```

### 密封类具体作用

这是一个interface接口类

```kotlin
interface Result
class Success(val msg: String) : Result
class Failure(val error: Exception) : Result
```

getResultMsg()用于获取执行结果信息

```kotlin
fun getResultMsg(result: Result) = when (result) {
    is Success -> result.msg
    is Failure -> result.error.message
    else -> throw IllegalArgumentException()
}
```

kotlin默认when语法

```
fun describe(obj: Any): String =
    when (obj) {
        1          -> "One"
        "Hello"    -> "Greeting"
        is Long    -> "Long"
        !is String -> "Not a string"
        else       -> "Unknown"
    }
```

所以使用interface必须写else

使用密封类便可以省略else条件分支，因为Result的执行结果只可能是`Success`或者`Failure`

