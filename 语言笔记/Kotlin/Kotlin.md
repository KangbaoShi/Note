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

## 扩展函数

扩展函数表示即使在不修改某个类的源码的情况下，仍然可以打开这个类，向该类添加新的函数。

### 语法结构

```kotlin
fun ClassName.methodName(param1: Int, param2: Int): Int {
    return 0
}
```

示例

```kotlin
fun String.lettersCount(): Int {
    var count = 0
    for (char in this) {
        if (char.isLetter()) {
            count++
        }
    }
    return count
}
```

使用方法

```kotlin
val count = "ABC123xyz!@#".lettersCount()
```

## 运算符重载

`+`重载

```kotlin
class Obj {

    operator fun plus(obj: Obj): Obj {
        // 处理相加的逻辑
    }

}
```

`+`多重重载

```kotlin
class Money(val value: Int) {

    operator fun plus(money: Money): Money {
        val sum = value + money.value
        return Money(sum)
    }

    operator fun plus(newValue: Int): Money {
        val sum = value + newValue
        return Money(sum)
    }

}
```

### 随机字符长度

```kotlin
operator fun String.times(n: Int) = repeat(n)
fun getRandomLengthString(str: String) = str * (1..20).random()
```

### 语法糖表达式和实际调用函数对照表

| 语法糖表达式 | 实际调用函数         |
| :----------- | :------------------- |
| `a + b`      | `a.plus(b)`          |
| `a - b`      | `a.minus(b)`         |
| `a * b`      | `a.times(b)`         |
| `a / b`      | `a.div(b)`           |
| `a % b`      | `a.rem(b)`           |
| `a++`        | `a.inc()`            |
| `a--`        | `a.dec()`            |
| `+a`         | `a.unaryPlus()`      |
| `-a`         | `a.unaryMinus()`     |
| `!a`         | `a.not()`            |
| `a == b`     | `a.equals(b)`        |
| `a > b`      | `a.compareTo(b) > 0` |
| `a < b`      | `a.compareTo(b) < 0` |
| `a >= b`     | `a.compareTo(b) >=0` |
| `a <= b`     | `a.compareTo(b)<=0`  |
| `a..b`       | `a.rangeTo(b)`       |
| `a[b]`       | `a.get(b)`           |
| `a[b] = c`   | `a.set(b, c)`        |
| `a in b`     | `b.contains(a)`      |

## 定义高阶函数

**基本规则**

```kotlin
(String, Int) -> Unit
```

```kotlin
fun example(func: (String, Int) -> Unit) {
    func("hello", 123)
}
```

### 高阶函数例子

```kotlin
fun num1AndNum2(num1: Int, num2: Int, operation: (Int, Int) -> Int): Int {
    return operation(num1, num2)
}

fun plus(num1: Int, num2: Int): Int {
    return num1 + num2
}

fun minus(num1: Int, num2: Int): Int {
    return num1 - num2
}


fun main() {
    val num1 = 100
    val num2 = 80
    val result1 = num1AndNum2(num1, num2, ::plus)
    val result2 = num1AndNum2(num1, num2, ::minus)
    println("result1 is $result1")
    println("result1 is $result2")
}
```

### Lambda表达式

```kotlin
fun num1AndNum2(num1: Int, num2: Int, operation: (Int, Int) -> Int): Int {
    return operation(num1, num2)
}

fun main() {
    val num1 = 100
    val num2 = 80
    val result1 = num1AndNum2(num1, num2) { n1, n2
        -> n1 + n2
    }
    val result2 = num1AndNum2(num1, num2) { n1, n2
        -> n1 - n2
    }
    println("result1 is $result1")
    println("result1 is $result2")
}
```

### 实现applay()函数

```kotlin
fun StringBuilder.build (block : StringBuilder.() -> Unit) : StringBuilder {
    block()
    return this
}

fun main() {
    val list = listOf("Apple", "Banana")
    val result = StringBuilder().build {
        append("Start eating fruits.\n")
        for (fruit in list) {
            append(fruit).append("\n")
        }
        append("Ate all fruits.")
    }

    println(result.toString())
}
```

## 内联函数的作用

**inline**

当不使用inline时，Kotlin会将高阶函数变成Funcation接口的匿名类实现，这样会带来额外的内存和性能开销。

**inline例子**

```kotlin
inline fun num1AndNum2(num1: Int, num2: Int, operation: (Int, Int) -> Int): Int {
    val result = operation(num1, num2)
    return result
}
```

**noinline**

使用了noinline关键字的地方不会被内联

```kotlin
inline fun inlineTest(block1: () -> Unit, noinline block2: () -> Unit) {
}
```

1. 内联的函数类型参数在编译的时候会被进行代码替换，因此它没有真正的参数属性。非内联的函数类型参数可以自由地传递给其他任何函数，因为它就是一个真实的参数，而内联的函数类型参数只允许传递给另外一个内联函数，这也是它最大的局限性。
2. 内联函数和非内联函数还有一个重要的区别，那就是内联函数所引用的Lambda表达式中是可以使用`return`关键字来进行函数返回的，而非内联函数只能进行局部返回。

**noinline局部函数返回**

```kotlin
fun printString(str: String, block: (String) -> Unit) {
    println("printString begin")
    block(str)
    println("printString end")
}

fun main() {
    println("main start")
    val str = ""
    printString(str) { s ->
        println("lambda start")
        if (s.isEmpty()) return@printString
        println(s)
        println("lambda end")
    }
    println("main end")
}
```

```kotlin
main start
printString begin
lambda start
printString end
main end
```

Lambda表达式中是不允许直接使用`return`关键字的，这里使用了`return@printString`的写法，表示进行局部返回，并且不再执行Lambda表达式的剩余部分代码。

**inline非局部返回**

```xml
inline fun printString(str: String, block: (String) -> Unit) {
    println("printString begin")
    block(str)
    println("printString end")
}

fun main() {
    println("main start")
    val str = ""
    printString(str) { s ->
        println("lambda start")
        if (s.isEmpty()) return
        println(s)
        println("lambda end")
    }
    println("main end")
}
```

```
main start
printString begin
lambda start

```

**crossinline**

**报错**

```
inline fun runRunnable(block: () -> Unit) {
    val runnable = Runnable {
        block()
    }
    runnable.run()
}
```

**正常**

```kotlin
inline fun runRunnable(crossinline block: () -> Unit) {
    val runnable = Runnable {
        block()
    }
    runnable.run()
}
```

