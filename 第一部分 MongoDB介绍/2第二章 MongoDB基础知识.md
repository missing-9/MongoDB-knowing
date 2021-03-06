# 第二章 MongoDB基础知识

## 2.1 文档
文档就是键值对的一个有序集。大多数文档通常包含多个键/值对。文档中的值可以是多种不同的数据类型（甚至可以是一个完整的内嵌文档）。文档的键是字符串。
```
1、MongoDB不但区分类型，而且区分大小写。
2、MongoDB的文档不能有重复的键。
```

## 2.2 集合
集合就是一组文档。

### 2.2.1 动态模式
动态模式意味着集合里面的文档可以是各式各样的。


> 既然没有必要区分不同类型文档的模式，为什么还要使用多个集合呢？
> 
>1、如果把各种各样的文档不加区分地放在同一集合里，开发者要么确保每次查询只返回特定类型的文档，要么让执行查询的应用程序来处理所有不同类型的文档。
> 
>2、如果从一个集合中查询多种特定类型的文档，速度会很慢。分开查询相应的集合速度会快得多。把同种类型的文档放在一个集合里，数据会更加集中。
>
>3、索引是按照集合来定义的。创建索引时，会使用文档的附加结构（特别是创建唯一索引时）在一个集合中只放入一种类型的文档，可以更有效地对集合进行索引。

### 2.2.2 命名

### 2.2.3 子集合
组织集合的一种惯例是使用“.”分隔不同命名空间的子集合，

## 2.3 数据库
在MongoDB中，多个文档组成集合，而多个集合可以组成数据库。一个MongoDB实例可以承载多个数据库，每个数据库拥有0个或者多个集合。每个数据库都有独立的权限，即便是在磁盘上，不同的数据库也放置在不同的文件中。要想在同一个MongoDB服务器上存放多个应用程序或者用户的数据，就需要使用不同的数据库。

有一些数据库名是保留的。如下所示。
- admin

> 1、从身份验证的角度来讲，这是“root”数据库。如果将一个用户添加到admin数据库，这个用户将自动获得所有数据库的权限。
> 
>2、一些特定的服务器端命令也只能从admin数据库运行，如列出所有数据库或关闭服务器。

- local

> 这个数据库永远都不可以复制，且一台服务器上的所有本地集合都可以存储在这个数据库中。

- config

> MongoDB用于分片设置时，分片信息会存储在config数据库中。

## 2.4 启动MongoDB

## 2.5 MongoDB shell简介
MongoDB自带JavaScript shell，可在shell中使用命令行与MongoDB实例交互。通过shell可以执行管理操作，检查运行实例等一系列操作。

## 2.6 数据类型

### 2.6.1 基本数据类型
- null
- 布尔型
- 数值
- 日期
- 正则表达式
- 数组
- 内嵌文档
- 对象id
- 二进制数据
- 代码

### 2.6.2 日期
在JavaScript中，Date类可以用作MongoDB的日期类型。创建日期对象时，应使用new Date(...)，而非Date(...)。

如果采用Date(...)创建日期时，返回的时日期的字符串表示，而非日期对象。按照这种方式将得到一堆混乱的日期对象和日期的字符串。由于日期和字符串之间无法匹配，所以执行删除、更新和查询等几乎所有操作时会导致很多问题。这个结果与MongoDB无关，是JavaScript的工作机制决定的。

shell根据本地时区设置显示日期对象。然而，数据库中存储的日期仅为新纪元以来的毫秒数，并未存储对应的时区。当然，可将时区信息存储为另一个键的值。

### 2.6.3 数组
数组是一组值，它既能作为有序对象（如列表、栈或队列），也能作为无序对象（如数据集）来操作。

在下面的文档中，“things”这个键的值是一个数组：
```
{"things":["pie",3.14]}
```
此例表示，数组可包含不同数据类型的元素（在此，是一个字符串和一个浮点数）。实际上，常规的键/值对支持的所有制都可以作为数据的值，数组中甚至可以嵌套数组。

可以使用数组内容对数组进行查询和构建索引。例如，之前的例子中，MongoDB可以查询出“things”数组中包含3.14这个元素的所有文档。要是经常使用这个查询，可以对“things”创建索引来提高性能。

### 2.6.4 内嵌文档
文档可以作为键的值，这样的文档就是内嵌文档。

### 2.6.5 _id和ObjectId
MongoDB中存储的文档必须有一个“_id”键。这个键的值可以是任何类型的，默认是个ObjectId对象。

**1.ObjectId**

ObjectId是“_id”的默认类型。不同的机器都能用全局唯一的同种方法方便地生成它。因为设计MongoDB的初衷就是用作分布式数据库，所以能够在分片环境中生成唯一的标示符非常重要。

ObjectId使用12字节的存储空间，是一个由24个十六进制数字组成的字符串。ObjectId是实际存储数据的两倍长。

ObjectId的12字节按照如下方式生成：

0-1-2-3：时间戳；

4-5-6：机器；

7-8：PID；

9-10-11：计数器。

**2.自动生成_id**

如果插入文档时没有“_id”键，系统会自动创建一个。通过这件事不是由MongoDB服务器来做，而是在客户端由驱动程序完成。这一做法非常好地体现了MongoDB的哲学：能交给客户端驱动程序来做的事情就不要交给服务器来做。这种理念背后的原因时，即便是像MongoDB这样扩展性非常好的数据库，扩展应用层也要比扩展数据库层容易得多。将工作交由客户端来处理，就减轻了数据库扩展的负担。


## 2.7 使用MongoDB shell
只要计算机能够连通MongoDB实例所在的计算机，shell就可以连接。在启动shell时指定机器名和端口，就可以连接到一台不同的机器（或者端口）。

启动mongo shell时不连接任何mongod有时很方便。通过--nodb参数启动shell，启动时就不会连接任何数据库。

启动之后，在需要时运行new Mongo(hostname)命令就可以连接到想要的mongod了。


### 2.7.1 shell小贴士
由于mongo时一个简化的JavaScript shell，可以通过查看JavaScript的在线文档得到大量帮助。对于MongoDB特有的功能，shell内置了帮助文档，可以使用help命令查看：

```
> help
        db.help()                    help on db methods
        db.mycoll.help()             help on collection methods
        sh.help()                    sharding helpers
        rs.help()                    replica set helpers
        help admin                   administrative help
        help connect                 connecting to a db help
        help keys                    key shortcuts
        help misc                    misc things to know
        help mr                      mapreduce

        show dbs                     show database names
        show collections             show collections in current database
        show users                   show users in current database
        show profile                 show most recent system.profile entries with time >= 1ms
        show logs                    show the accessible logger names
        show log [name]              prints out the last segment of log in memory, 'global' is default
        use <db_name>                set current database
        db.foo.find()                list objects in collection foo
        db.foo.find( { a : 1 } )     list objects in foo where a == 1
        it                           result of the last line evaluated; use to further iterate
        DBQuery.shellBatchSize = x   set default number of items to display on shell
        exit                         quit the mongo shell
>
```

可以通过db.help()查看数据库级别的帮助，使用db.foo.help()查看集合级别的帮助。

如果想知道一个函数是做什么用的，可以直接在shell输入函数名（函数名后不要输入小括号），这样就可以看到相应函数的JavaScript实现代码。

```
> db.foo.save
function(obj, opts) {
    if (obj == null)
        throw Error("can't save a null");

    if (typeof (obj) == "number" || typeof (obj) == "string")
        throw Error("can't save a number or string");

    if (typeof (obj._id) == "undefined") {
        obj._id = new ObjectId();
        return this.insert(obj, opts);
    } else {
        return this.update({_id: obj._id}, obj, Object.merge({upsert: true}, opts));
    }
}
>
```
