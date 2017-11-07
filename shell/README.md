# `mongo` Shell 工具

本页索引:

* [介绍](#介绍)
* [启动 `mongo`Shell](#启动-mongo-shell)
* [使用 `mongo` Shell 工作](#使用mongo-shell-工作)
* [Tab 补全及其他快捷键](#tab-补全及其他快捷键)
* [Exit](#exit)

## 介绍

[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell 是一个基于 JavaScript 语言的 MongoDB 交互工具. 你可以使用 [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell 来查询和更新数据也可以执行管理员操作.

同时 [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell 也是 [MongoDB distributions](http://www.mongodb.org/downloads) 的一个组件. 只要你 [安装并且启动了 MongoDB](https://docs.mongodb.com/manual/installation/), 就可以通过 [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell 连接到你运行中的 MongoDB 实例.

在 [MongoDB 手册](https://elemefe.gitbooks.io/mongodb/content/) 中的大部分例子都是使用 [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell; 此外, 许多[驱动](https://docs.mongodb.com/manual/applications/drivers/) 也提供了相似的工具.

## 启动 `mongo` Shell

**重要**

在试图启用 [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell 之前确保 MongoDB 是运行的.

要启动 [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell 并连接上运行在 **localhost** 和 **默认端口** 的 [MongoDB](https://docs.mongodb.com/manual/reference/program/mongod/) 实例:

1. 打开命令行工具, 找到你 `<存放mongodb工具的目录>`:

   ```bash
   cd <存放mongodb工具的目录>
   ```

2. 输入`./bin/mongo` 来启动 [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo):

   ```bash
   ./bin/mongo
   ```

   如果你已经把 `<存放mongodb工具的目录>/bin` 添加到 `PATH` 环境变量中, 就可以直接输入 `mongo` 不用输入 `./bin/mongo`.

### 选项

当你不加任何参数运行 [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo), 那么 [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell 将会试图连接运行在 `localhost` 且端口是 `27017` 的 MongoDB 实例. 要指定一个其他 host 或者 port, 或者其他选项, 参见[启动 mongo 实例](https://docs.mongodb.com/manual/reference/program/mongo/#mongo-usage-examples)和[mongo 引用](https://docs.mongodb.com/manual/reference/program/mongo/), 其中提供了详细的可用选项.

### `.mongorc.js` 文件

在启动时, [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) 检查用户的 [`HOME`](https://docs.mongodb.com/manual/reference/program/mongo/#envvar-HOME) 目录下一个名为 [.mongorc.js](https://docs.mongodb.com/manual/reference/program/mongo/#mongo-mongorc-file) 的 JavaScript 文件. 如果文件存在, [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) 会在显示提示符之前第一时间加载 `.mongorc.js` 的内容. 当你使用 shell 来运行一个 JavaScript 文件或者表达式, 以及在命令行使用 `--eval` 选项或者指定一个 [.js 文件让 mongo 运行](https://docs.mongodb.com/manual/reference/program/mongo/#mongo-shell-file)时, [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) 会在 JavaScript 执行结束后读取 `.mongorc.js` 文件. 你也可以通过使用 [`--norc`](https://docs.mongodb.com/manual/reference/program/mongo/#cmdoption-norc) 选项阻止 `.mongorc.js` 被加载.

## 使用`mongo` Shell 工作

要查看当前使用的数据库库, 你可以输入 `db`:

```
db
```

The operation should return`test`, which is the default database. To switch databases, issue the`use<db>`helper, as in the following example:

```
use <database>
```

To list the available databases, use the helper`showdbs`. See also[`db.getSiblingDB()`](https://docs.mongodb.com/manual/reference/method/db.getSiblingDB/#db.getSiblingDB)method to access a different database from the current database without switching your current database context \(i.e.`db`\).

You can switch to non-existing databases. When you first store data in the database, such as by creating a collection, MongoDB creates the database. For example, the following creates both the database`myNewDatabase`and the[collection](https://docs.mongodb.com/manual/reference/glossary/#term-collection)`myCollection`during the[`insertOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/#db.collection.insertOne)operation:

```
use myNewDatabase
db.myCollection.insertOne( { x: 1 } );
```

The[`db.myCollection.insertOne()`](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/#db.collection.insertOne)is one of the[methods available in the mongo shell](https://docs.mongodb.com/manual/reference/method/).

* `db`
  refers to the current database.
* `myCollection`
  is the name of the collection.

If the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell does not accept the name of a collection, you can use the alternative[`db.getCollection()`](https://docs.mongodb.com/manual/reference/method/db.getCollection/#db.getCollection)syntax. For instance, if a collection name contains a space or hyphen, starts with a number, or conflicts with a built-in function:

```
db.getCollection("3 test").find()
db.getCollection("3-test").find()
db.getCollection("stats").find()
```

The[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell prompt has a limit of 4095 codepoints for each line. If you enter a line with more than 4095 codepoints, the shell will truncate it.

For more documentation of basic MongoDB operations in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell, see:

* [Getting Started Guide](https://docs.mongodb.com/getting-started/shell)
* [Insert Documents](https://docs.mongodb.com/manual/tutorial/insert-documents/)
* [Query Documents](https://docs.mongodb.com/manual/tutorial/query-documents/)
* [Update Documents](https://docs.mongodb.com/manual/tutorial/update-documents/)
* [Delete Documents](https://docs.mongodb.com/manual/tutorial/remove-documents/)
* [mongo Shell Methods](https://docs.mongodb.com/manual/reference/method/)

### Format Printed Results

The[`db.collection.find()`](https://docs.mongodb.com/manual/reference/method/db.collection.find/#db.collection.find)method returns a[cursor](https://docs.mongodb.com/manual/reference/glossary/#term-cursor)to the results; however, in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell, if the returned cursor is not assigned to a variable using the`var`keyword, then the cursor is automatically iterated up to 20 times to print up to the first 20 documents that match the query. The[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell will prompt`Typeit`to iterate another 20 times.

To format the printed result, you can add the`.pretty()`to the operation, as in the following:

```js
db.myCollection.find().pretty()
```

In addition, you can use the following explicit print methods in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell:

* `print()`to print without formatting
* `print(tojson(<obj>))`to print with [JSON](https://docs.mongodb.com/manual/reference/glossary/#term-json) formatting and equivalent to `printjson()`
* `printjson()` to print with [JSON](https://docs.mongodb.com/manual/reference/glossary/#term-json) formatting and equivalent to `print(tojson(<obj>))`

For more information and examples on cursor handling in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell, see[Iterate a Cursor in the mongo Shell](https://docs.mongodb.com/manual/tutorial/iterate-a-cursor/). See also[Cursor Help](https://docs.mongodb.com/manual/tutorial/access-mongo-shell-help/#mongo-shell-help-cursor)for list of cursor help in the[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell.

### Multi-line Operations in the`mongo`Shell

If you end a line with an open parenthesis \(`'('`\), an open brace \(`'{'`\), or an open bracket \(`'['`\), then the subsequent lines start with ellipsis \(`"..."`\) until you enter the corresponding closing parenthesis \(`')'`\), the closing brace \(`'}'`\) or the closing bracket \(`']'`\). The[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell waits for the closing parenthesis, closing brace, or the closing bracket before evaluating the code, as in the following example:

```
> if ( x > 0 ) {
... count++;
... print (x);
... }
```

You can exit the line continuation mode if you enter two blank lines, as in the following example:

```
> if (x > 0
...
...
>
```

## Tab 补全及其他快捷键

The[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell supports keyboard shortcuts. For example,

* Use the up/down arrow keys to scroll through command history. See[.dbshell](https://docs.mongodb.com/manual/reference/program/mongo/#mongo-dbshell-file)documentation for more information on the`.dbshell`file.

* Use`<Tab>`to autocomplete or to list the completion possibilities, as in the following example which uses`<Tab>`to complete the method name starting with the letter`'c'`:

  ```js
  db.myCollection.c<Tab>
  ```

  Because there are many collection methods starting with the letter`'c'`, the`<Tab>`will list the various methods that start with`'c'`.

For a full list of the shortcuts, see[Shell Keyboard Shortcuts](https://docs.mongodb.com/manual/reference/program/mongo/#mongo-keyboard-shortcuts)

## Exit

To exit the shell, type`quit()`or use the`<Ctrl-C>`shortcut.

SEE ALSO

* [Getting Started Guide](https://docs.mongodb.com/getting-started/shell)
* [`mongoReferencePage`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)



