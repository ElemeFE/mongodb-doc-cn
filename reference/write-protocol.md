# MongoDB 通信协议

* [简介](#简介)
* [TCP/IP套接字](#套接字)
* [报文类型格式](#报文类型格式)
* [标准报文头](#标准报文头)
* [客户端请求报文](#客户端请求报文)
* [数据库响应报文](#数据库响应报文)

## 简介

MongoDB 通信协议是简单的基于套接字的请求响应风格的协议。客户端和数据库服务器通过长连接的 TCP/IP 套接字来进行交互。

## 套接字

客户端通过长连接的 TCP/IP 套接字来与数据库连接，没有连接握手阶段。

### 端口

[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)和[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)进程默认端口号为 27017，此外端口是可以配置修改的。

### 字节序

All data in the MongoDB wire protocol is little-endian.
MongoDB 通信协议的数据采用小端[`字节序`](https://zh.wikipedia.org/wiki/%E5%AD%97%E8%8A%82%E5%BA%8F#.E5.B0.8F.E7.AB.AF.E5.BA.8F)。

## 报文类型格式

报文类型有两种，客户端请求和数据库响应。

注解

* 本文使用 C 语言风格的结构体来描述报文格式。
* 文档中使用的类型如 \(
  `cstring`
  ,
  `int32`
  , 等.\) 对应于[BSON 类型说明](http://bsonspec.org/#/specification)中的数据类型格式
  .
* 文档中使用星号表示法来表示重复，例如 int64* 表示一个或多个 int64 类型（具体可见[BSON 类型说明](http://bsonspec.org/#/specification)）可以被一个接一个的往套接字中写入。
* 本文将标准头部命名为 `MsgHeader`。整型常量使用大写字母表示 (例如，`ZERO` 表示整型值 0)。

## 标准报文头

一般来说，每个消息包含一个标准的消息头紧随其后的是特定数据。标准的消息头的结构如下:

```c
struct MsgHeader {
    int32   messageLength; // 总报文大小，包含本字段
    int32   requestID;     // 报文标识 ID
    int32   responseTo;    // 数据库响应报文标识 ID，和 requestID 一样，
                           // 表明响应回该 requestID 对应的请求
    int32   opCode;        // 请求类型 - 具体参照下表
}
```

| 字段 | 描述 |
| :--- | :--- |
| `messageLength` | 报文总长度，使用 4 个字节表示。 |
| `requestID` | 由客户端或服务器生成的报文唯一标识。 例如客户端请求 \([OP\_QUERY](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-query) 和 [OP\_GET\_MORE](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-get-more)\), 将返回 [OP\_REPLY](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-reply) 报文给 `responseTo` 字段表示的请求. 客户端可以使用 `requestID` 和 `responseTo` 字段关联查询响应到原始请求上。 |
| `responseTo` | 服务端响应使用此字段关联到 `requestID` 对应的查询请求上。|
| `opCode` | 报文类型，详情见 [请求操作码](#请求操作码)。 |

### 请求操作码

注释

自 MongoDB 2.6 和[`maxWireVersion`](https://docs.mongodb.com/manual/reference/command/isMaster/#isMaster.maxWireVersion) `3`, MongoDB 驱动使用[数据库命令](https://docs.mongodb.com/manual/reference/command/#collection-commands)[`insert`](https://docs.mongodb.com/manual/reference/command/insert/#dbcmd.insert),[`update`](https://docs.mongodb.com/manual/reference/command/update/#dbcmd.update), 和[`delete`](https://docs.mongodb.com/manual/reference/command/delete/#dbcmd.delete)方式代替 `OP_INSERT`,`OP_UPDATE`, 和`OP_DELETE`操作码方式。许多驱动仍然在使用操作码方式。

重点

`OP_COMMAND` 和 `OP_COMMANDREPLY` 是集群内部操作命令，不需要实现。

`OP_COMMAND` 和 `OP_COMMANDREPLY` 操作协议格式是非固定的，不支持向后兼容。

协议支持的 `操作码 (Opcode)` 如下:

| 操作码 (Opcode) 名 | 值 | 注解 |
| :--- | :--- | :--- |
| `OP_REPLY` | 1 | 客户端请求回复。此操作 `responseTo` 会被设置为对应的 `requestID` 值。 |
| `OP_UPDATE` | 2001 | 更新文档。 |
| `OP_INSERT` | 2002 | 插入新文档。 |
| `RESERVED` | 2003 | 保留操作，以前用于 OP\_GET\_BY\_OID。 |
| `OP_QUERY` | 2004 | 集合查询操作。 |
| `OP_GET_MORE` | 2005 | OP_QUERY 后获取更多信息的后续操作。 详情见游标 (cursor)。 |
| `OP_DELETE` | 2006 | 删除文档。 |
| `OP_KILL_CURSORS` | 2007 | 用于通知数据库客户端游标已经操作完成，需销毁。 |
| `OP_COMMAND` | 2010 | 集群内部请求命令操作。 |
| `OP_COMMANDREPLY` | 2011 | 集群内部请求命令 `OP_COMMAND` 的响应。 |

## 客户端请求报文

客户端可以发送指定除[OP\_REPLY](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-reply)opCode以外的所有请求消息。[OP\_REPLY](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-reply)被保留供数据库使用。

只有[OP\_QUERY](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-query)和[OP\_GET\_MORE](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-get-more)消息导致数据库的响应。将不会有任何其他消息发送回应。

您可以使用getLastError命令确定消息是否成功。

### OP\_UPDATE

OP\_UPDATE消息用于更新集合中的文档。OP\_UPDATE消息的格式如下：

```
struct OP_UPDATE {
    MsgHeader header;             // standard message header
    int32     ZERO;               // 0 - reserved for future use
    cstring   fullCollectionName; // "dbname.collectionname"
    int32     flags;              // bit vector. see below
    document  selector;           // the query to select the document
    document  update;             // specification of the update to perform
}
```

| 字段 | 描述 |
| :--- | :--- |
| `header` | 消息头，如[标准消息头中所述](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wp-message-header)。 |
| `ZERO` | 整数值为0.保留以备将来使用。 |
| `fullCollectionName` | 完整的集合名称;即命名空间。完整的集合名称是数据库名称与集合名称`.`的拼接，使用一个for concatenation。例如，对于数据库`foo`和集合`bar`，完整的集合名称是`foo.bar`。 |
| `flags` | 位矢量指定操作的标志。位值对应于以下内容：`0`对应于Upsert。如果设置，如果没有找到匹配的文档，数据库将把提供的对象插入到集合中。`1`对应于MultiUpdate.If设置，数据库将更新集合中的所有匹配对象。否则只更新第一个匹配的文件。`2`-`31`保留。必须设置为0。 |
| `selector` | BSON文档，指定要更新的文档的选择查询。 |
| `update` | BSON文档，指定要执行的更新。有关指定更新的信息，请参阅MongoDB手册中的[更新操作](https://docs.mongodb.com/manual/applications/update)文档。 |

没有对OP\_UPDATE消息的响应。

### OP\_INSERT

OP\_INSERT消息用于将一个或多个文档插入到集合中。OP\_INSERT消息的格式是

```
struct {
    MsgHeader header;             // standard message header
    int32     flags;              // bit vector - see below
    cstring   fullCollectionName; // "dbname.collectionname"
    document* documents;          // one or more documents to insert into the collection
}
```

| 字段 | 描述 |
| :--- | :--- |
| `header` | 消息头，如[标准消息头中所述](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wp-message-header)。 |
| `flags` | 位矢量指定操作的标志。位值对应于以下内容：`0`对应于ContinueOnError。如果设置，数据库将不会停止处理批量插入，如果一个失败（例如由于重复的ID）。这使得批量插入的行为与一系列单个插入行为类似，只是如果任何插入失败，将会设置lastError，而不仅仅是最后一个。如果发生多个错误，则只有最近一次将由getLastError报告。（1.9.1新增）`1`-`31`保留。必须设置为0。 |
| `fullCollectionName` | 完整的集合名称;即命名空间。完整的集合名称是数据库名称与集合名称`.`的拼接，使用一个for concatenation。例如，对于数据库`foo`和集合`bar`，完整的集合名称是`foo.bar`。 |
| `documents` | 一个或多个要插入到集合中的文档。如果有多个，它们会依次写入到插槽中。 |

对OP\_INSERT消息没有响应。

### OP\_QUERY

OP\_QUERY消息用于在数据库中查询集合中的文档。OP\_QUERY消息的格式是：

```
struct OP_QUERY {
    MsgHeader header;                 // standard message header
    int32     flags;                  // bit vector of query options.  See below for details.
    cstring   fullCollectionName ;    // "dbname.collectionname"
    int32     numberToSkip;           // number of documents to skip
    int32     numberToReturn;         // number of documents to return
                                      //  in the first OP_REPLY batch
    document  query;                  // query object.  See below for details.
  [ document  returnFieldsSelector; ] // Optional. Selector indicating the fields
                                      //  to return.  See below for details.
}
```

| 字段 | 描述 |
| :--- | :--- |
| `header` | 消息头，如[标准消息头中所述](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wp-message-header)。 |
| `flags` | 位矢量指定操作的标志。位值对应于以下内容：`0`被预定了。必须设置为0。`1`对应于TailableCursor。Tailable表示当检索到最后一个数据时光标未关闭。而是光标标记最终对象的位置。如果收到更多的数据，可以稍后再从光标所在的位置继续使用光标。像任何“潜在游标”一样，游标可能在某个点（CursorNotFound）变成无效 - 例如，如果它引用的最终对象被删除。`2`对应于SlaveOk.Allow查询副本从属。通常这些返回一个错误，除了名字空间“本地”。`3`对应于OplogReplay。仅使用内部复制 - 不应设置驱动程序。`4`对应于NoCursorTimeout。闲置时间（10分钟）后，服务器通常超时闲置游标以防止使用过多内存。设置这个选项来防止这个。`5`对应于AwaitData。与TailableCursor一起使用。如果我们在数据的末尾，阻塞一段时间，而不是没有返回任何数据。超时后，我们照常返回。`6`对应于排气。假设客户端将完全读取所有查询的数据，将数据全部流式传输到多个“更多”包中。当你拉大量的数据，并知道你想把这一切拉下来，更快。注意：除非关闭连接，否则不允许客户端读取所有数据。`7`对应于部分。如果一些碎片落下（而不是抛出错误），从蒙哥马斯获得部分结果`8`-`31`保留。必须设置为0。 |
| `fullCollectionName` | 完整的集合名称;即命名空间。完整的集合名称是数据库名称与集合名称`.`的拼接，使用一个for concatenation。例如，对于数据库`foo`和集合`bar`，完整的集合名称是`foo.bar`。 |
| `numberToSkip` | 当返回查询结果时，设置要省略的文档数量 - 从结果数据集中的第一个文档开始。 |
| `numberToReturn` | 将第一个[OP\_REPLY](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-reply)消息中的文档数限制为查询。但是，`cursorID`如果有更多的结果，数据库仍然会建立一个游标并返回给客户端`numberToReturn`。如果客户端驱动程序提供“限制”功能（如SQL LIMIT关键字），则由客户端驱动程序确保不超过指定数量的文档返回给调用应用程序。如果`numberToReturn`是`0`，数据库将使用默认的返回大小。如果数字是负数，那么数据库将返回该数字并关闭游标。该查询没有进一步的结果可以获取。如果`numberToReturn`是`1`服务器将把它作为`-1`（后自动关闭游标）。 |
| `query` | 表示查询的BSON文档。查询将包含一个或多个元素，所有这些元素都必须与要包含在结果集中的文档匹配。可能的因素包括`$query`，`$orderby`，`$hint`，`$explain`，和`$snapshot`。 |
| `returnFieldsSelector` | 可选的。BSON文件，限制返回的文件中的字段。所述`returnFieldsSelector`含有一种或多种元素，其中的每一个是应返回字段的名称，以及整数值`1`。在JSON符号，一个`returnFieldsSelector`限制的字段`a`，`b`并且`c`将是：{ a ：1 ，b ：1 ，c ：1 } |

数据库将用OP\_REPLY消息响应[OP\_QUERY](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-reply)消息。

### OP\_GET\_MORE

OP\_GET\_MORE消息用于在数据库中查询集合中的文档。OP\_GET\_MORE消息的格式是：

```
struct {
    MsgHeader header;             // standard message header
    int32     ZERO;               // 0 - reserved for future use
    cstring   fullCollectionName; // "dbname.collectionname"
    int32     numberToReturn;     // number of documents to return
    int64     cursorID;           // cursorID from the OP_REPLY
}
```

| 字段 | 描述 |
| :--- | :--- |
| `header` | 消息头，如[标准消息头中所述](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wp-message-header)。 |
| `ZERO` | 整数值为0.保留以备将来使用。 |
| `fullCollectionName` | 完整的集合名称;即命名空间。完整的集合名称是数据库名称与集合名称`.`的拼接，使用一个for concatenation。例如，对于数据库`foo`和集合`bar`，完整的集合名称是`foo.bar`。 |
| `numberToReturn` | 将第一个[OP\_REPLY](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-reply)消息中的文档数限制为查询。但是，`cursorID`如果有更多的结果，数据库仍然会建立一个游标并返回给客户端`numberToReturn`。如果客户端驱动程序提供了“限制”功能（如SQL LIMIT关键字），则由客户端驱动程序确保不超过指定数量的文档返回给调用应用程序。如果`numberToReturn`是`0`，数据库将使用默认的返回大小。 |
| `cursorID` | 光标标识符出现在[OP\_REPLY中](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-reply)。这必须是来自数据库的值。 |

数据库将用OP\_REPLY消息响应[OP\_GET\_MORE](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-reply)消息。

### OP\_DELETE

OP\_DELETE消息用于从集合中删除一个或多个文档。OP\_DELETE消息的格式是：

```
struct {
    MsgHeader header;             // standard message header
    int32     ZERO;               // 0 - reserved for future use
    cstring   fullCollectionName; // "dbname.collectionname"
    int32     flags;              // bit vector - see below for details.
    document  selector;           // query object.  See below for details.
}
```

| 字段 | 描述 |
| :--- | :--- |
| `header` | 消息头，如[标准消息头中所述](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wp-message-header)。 |
| `ZERO` | 整数值为0.保留以备将来使用。 |
| `fullCollectionName` | 完整的集合名称;即命名空间。完整的集合名称是数据库名称与集合名称`.`的拼接，使用一个for concatenation。例如，对于数据库`foo`和集合`bar`，完整的集合名称是`foo.bar`。 |
| `flags` | 位矢量指定操作的标志。位值对应于以下内容：`0`对应于SingleRemove。如果设置，数据库将只删除集合中第一个匹配的文档。否则，所有匹配的文件将被删除。`1`-`31`保留。必须设置为0。 |
| `selector` | 代表用于选择要删除的文档的查询的BSON文档。选择器将包含一个或多个元素，所有这些元素都必须与要从集合中移除的文档匹配。 |

没有响应OP\_DELETE消息。

### OP\_KILL\_CURSORS

OP\_KILL\_CURSORS消息用于关闭数据库中的活动光标。这是确保在查询结束时回收数据库资源所必需的。OP\_KILL\_CURSORS消息的格式是：

```
struct {
    MsgHeader header;            // standard message header
    int32     ZERO;              // 0 - reserved for future use
    int32     numberOfCursorIDs; // number of cursorIDs in message
    int64*    cursorIDs;         // sequence of cursorIDs to close
}
```

| 字段 | 描述 |
| :--- | :--- |
| `header` | 消息头，如[标准消息头中所述](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wp-message-header)。 |
| `ZERO` | 整数值为0.保留以备将来使用。 |
| `numberOfCursorIDs` | 消息中的光标ID的数量。 |
| `cursorIDs` | 游标ID的“数组”被关闭。如果有多个，它们会依次写入到插槽中。 |

如果游标被读取直到耗尽（读取直到[OP\_QUERY](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-query)或[OP\_GET\_MORE](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-get-more)为游标ID返回0），则不需要[终止](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-get-more)游标。

### OP\_COMMAND

警告

`OP_COMMAND`是集群内部的，不应该由客户端或驱动程序来实现。

的`OP_COMMAND`格式和协议是不稳定的，而不保持向后兼容版本之间可能会改变。

`OP_COMMAND`是一个有线协议消息，用于在一个MongoDB服务器向另一个MongoDB服务器发出的集群内数据库命令请求。接收数据库发回一个[OP\_COMMANDREPLY](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-commandreply)作为对一个响应`OP_COMMAND`。

```
struct {
   MsgHeader header;     // standard message header
   cstring database;     // the name of the database to run the command on
   cstring commandName;  // the name of the command
   document metadata;    // a BSON document containing any metadata
   document commandArgs; // a BSON document containing the command arguments
   inputDocs;            // a set of zero or more documents
}
```

| 字段 | 描述 |
| :--- | :--- |
| `header` | 标准消息头，如在[标准消息头](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wp-message-header)。 |
| `database` | 要运行该命令的数据库的名称。 |
| `commandName` | 命令的名称。有关[数据库命令](https://docs.mongodb.com/manual/reference/command/#database-commands)的列表，请参阅数据库命令。 |
| `metadata` | 可用于系统将任何元数据附加到不属于命令参数本身的内部命令（由客户端驱动程序提供） |
| `commandArgs` | 包含命令参数的BSON文档。有关`commandName`其参数的信息，请参阅文档 |
| `inputDocs` | 作为输入到命令的零个或多个文档。用于需要从客户端发送大量数据的命令，例如批量插入。 |

## 数据库的响应消息

### OP\_REPLY

该`OP_REPLY`消息由数据库发送，以响应[OP\_QUERY](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-query)或[OP\_GET\_MORE](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-get-more)消息。OP\_REPLY消息的格式是：

```
struct {
    MsgHeader header;         // standard message header
    int32     responseFlags;  // bit vector - see details below
    int64     cursorID;       // cursor id if client needs to do get more's
    int32     startingFrom;   // where in the cursor this reply is starting
    int32     numberReturned; // number of documents in the reply
    document* documents;      // documents
}
```

| 字段 | 描述 |
| :--- | :--- |
| `header` | 消息头，如[标准消息头中所述](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wp-message-header)。 |
| `responseFlags` | 位矢量指定标志。位值对应于以下内容：`0`对应于CursorNotFound。在`getMore`被调用时被设置，但是在服务器上的光标ID是无效的。返回零结果。`1`对应于QueryFailure。查询失败时设置。结果由一个包含描述失败的“$ err”字段的文档组成。`2`对应于ShardConfigStale。司机应该忽略这一点。只会[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)看到这个集合，在这种情况下，它需要从服务器更新配置。`3`对应于AwaitCapable。当服务器支持“等待数据查询”选项时设置。如果没有，客户端应该在Tailble游标的getMore之间稍微睡一会儿。Mongod版本1.6支持AwaitData，因此总是设置AwaitCapable。`4`-`31`保留。忽视。 |
| `cursorID` | 这`cursorID`是OP\_REPLY的一部分。如果查询的结果集适合一个OP\_REPLY消息，`cursorID`则该值为0.这`cursorID`必须在用于获取更多数据的任何[OP\_GET\_MORE](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-get-more)消息中使用，并且在不再需要时通过[OP\_KILL\_CURSORS](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-kill-cursors)消息必须关闭。 |
| `startingFrom` | 在光标的起始位置。 |
| `numberReturned` | 答复中的文件数量。 |
| `documents` | 退回文件。 |

### OP\_COMMANDREPLY

警告

`OP_COMMANDREPLY`是集群内部的，不应该由客户端或驱动程序来实现。

的`OP_COMMANDREPLY`格式和协议是不稳定的，而不保持向后兼容版本之间可能会改变。

这`OP_COMMANDREPLY`是一个有线协议消息，用于在内部响应由一个MongoDB服务器向另一个服务器发出的集群内[OP\_COMMAND](https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wire-op-command)请求。

格式`OP_COMMANDREPLY`是：

```
struct {
   MsgHeader header;       // A standard wire protocol header
   document metadata;      // A BSON document containing any required metadata
   document commandReply;  // A BSON document containing the command reply
   document outputDocs;    // A variable number of BSON documents
}
```

| 字段 | 描述 |
| :--- | :--- |
| `header` | 标准消息头，如在[标准消息头]·(https://docs.mongodb.com/manual/reference/mongodb-wire-protocol/#wp-message-header)。 |
| `metadata` | 可用于系统将任何元数据附加到不属于命令参数本身的内部命令（由客户端驱动程序提供）。 |
| `commandReply` | 包含命令回复的BSON文档。 |
| `outputDocs` | 用于可以返回大量数据的命令，如查找或聚合。这个字段目前没有被使用。 |



