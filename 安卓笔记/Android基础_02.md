# Android基础_02

## ContentProvider

**ContentProvider主要用于在不同的应用程序之间实现数据共享**

### 运行时权限

程序在启用某些功能时，在申请权限

Android 危险权限

| 权限组名             | 权限名                     |
| -------------------- | -------------------------- |
| CALENDAR             | READ_CALENDAR              |
|                      | WRITE_CALENDAR             |
| CALL_LOG             | READ_CALL_LOG              |
|                      | WRITE_CALL_LOG             |
|                      | PROCESS_OUTGOING_CALLS     |
| CAMERA               | CAMERA                     |
| CONTACTS             | READ_CONTACTS              |
|                      | WRITE_CONTACTS             |
|                      | GET_ACCOUNTS               |
| LOCATION             | ACCESS_FINE_LOCATION       |
|                      | ACCESS_COARSE_LOCATION     |
|                      | ACCESS_BACKGROUND_LOCATION |
| MICROPHONE           | RECORD_AUDIO               |
| PHONE                | READ_PHONE_STATE           |
|                      | READ_PHONE_NUMBERS         |
|                      | CALL_PHONE                 |
|                      | ANSWER_PHONE_CALLS         |
|                      | ADD_VOICEMAIL              |
|                      | USE_SIP                    |
|                      | ACCEPET_HANDOVER           |
| SENSORS              | BODY_SENSORS               |
| ACTIVITY_RECOGNITION | ACTIVITY_RECOGNITION       |
| SMS                  | SEND_SMS                   |
|                      | RECEIVE_SMS                |
|                      | READ_SMS                   |
|                      | RECEIVE_WAP_PUSH           |
|                      | RECEIVE_MMS                |
| STORAGE              | READ_EXTERNAL_STORAGE      |
|                      | WRITE_EXTERNAL_STORAGE     |
|                      | ACCESS_MeDIA_LOACTION      |

用户一旦同意了某个权限申请之后，同组的其他权限也会被系统自动授权

### ContentResolver

共享机制：Uri

val uri = Uri.parse("content://com.example.app.provider/table1")

1. query()

   | query()方法参数 | 对应SQL部分               | 描述                             |
   | :-------------- | :------------------------ | :------------------------------- |
   | uri             | from table_name           | 指定查询某个应用程序下的某一张表 |
   | projection      | select column1, column2   | 指定查询的列名                   |
   | selection       | where column = value      | 指定where的约束条件              |
   | selectionArgs   | -                         | 为where中的占位符提供具体的值    |
   | sortOrder       | order by column1, column2 | 指定查询结果的排序方式           |

   ```kotlin
   val cursor = contentResolver.query(
       uri,
       projection,
       selection,
       selectionArgs,
       sortOrder)
   ```

   ```kotlin
   while (cursor.moveToNext()) {
       val column1 = cursor.getString(cursor.getColumnIndex("column1"))
       val column2 = cursor.getInt(cursor.getColumnIndex("column2"))
   }
   cursor.close()
   ```

   

2. insert()

   ```kotlin
   val values = contentValuesOf("column1" to "text", "column2" to 1)
   contentResolver.insert(uri, values)
   ```

3. delete()

   ```kotlin
   contentResolver.delete(uri, "column2 = ?", arrayOf("1"))
   ```

### ContentProvider

ContentProvider类中有6个需要重写的抽象方法

1. onCreate() 初始化ContentProvider的时候调用
2. query() 从ContentProvider中查询数据
3. insert() 向ContentProvider中添加一条数据
4. update() 更新ContentProvider中已有的数据
5. delete() 从ContentProvider中删除数据
6. getType() 传入的内容URI返回相应的MIME类型

一个标准的URI写法

```xml
content://com.example.app.provider/table1
```

获取某项值

```xml
content://com.example.app.provider/table1/1
```

通配符匹配URI

1. *表示匹配任意长度的任意字符
2. #表示匹配任意长度的数字

匹配任意表的内容URI格式

```xml
content://com.example.app.provider
```

匹配table1表中的任意一行数据内容URI格式

```xml
content://com.example.app.provider/table1/#
```

`MIME`格式

1. 必须以`vnd`开头
2. 如果内容URI以路径结尾，则后接`android.cursor.dir/`;如果内容URI以id结尾，后接`android.cursor.item/`
3. 最后接上`vnd.<authority>.<path>`

