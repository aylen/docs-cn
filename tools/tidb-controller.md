---
title: TiDB Controller 使用说明
category: tools
---

# TiDB Controller 使用说明

TiDB Controller 是 TiDB 的命令行工具，用于获取 TiDB 状态信息，多用于调试。

## 源码编译

编译环境要求：[Go](https://golang.org/) Version 1.7 以上

编译步骤：在 [TiDB Controller 项目](https://github.com/pingcap/tidb-ctl)根目录，使用 `make` 命令进行编译，生成 tidb-ctl。

编译文档：帮助文档在 doc 文件夹下，如丢失或需要更新，可通过 `make doc` 命令生成帮助文档。

## 使用介绍

`tidb-ctl` 的使用由命令（包括子命令）、选项和参数组成。命令即不带 `-` 或者 `--` 的字符，选项即带有 `-` 或者 `--` 的字符，参数即命令或选项字符后紧跟的传递给命令和选项的字符。

如：`tidb-ctl schema in mysql -n db`

* schema: 命令
* in: schema 的子命令
* mysql: in 的参数
* -n: 选项
* db: -n 的参数

### 获取帮助

`tidb-ctl -h/--help` 用于获取帮助信息。tidb-ctl 由多层命令组成，tidb-ctl 及其所有子命令都可以通过 `-h/--help` 来获取使用帮助。

### 连接

`tidb-ctl -H/--host { TiDB 服务地址} -P/--port { TiDB 服务端口}`

如不添加地址和端口将使用默认值，默认的地址是 127.0.0.1 (服务地址只能使用 IP 地址)，默认的端口是 10080。**连接选项是顶级选项，适用于以下所有命令。**

目前，TiDB Controller 可以获取四类信息，分别通过以下四个命令获得：

* `tidb-ctl mvcc` - MVCC 信息
* `tidb-ctl region` - Region 信息
* `tidb-ctl schema` - Schema 信息
* `tidb-ctl table` - Table 信息

### 使用举例

以获取 Schema 信息为例：

通过 `tidb-ctl schema -h` 可以获取这个子命令的使用帮助。schema 有两个子命令，in 和 tid。in 用来通过数据库名获取数据库中所有表的表结构，tid 用来通过全数据库唯一的 table_id 获取表的表结构。

#### in 命令

同样可以通过 `tidb-ctl schema in -h/--help` 来获取子命令 in 的使用帮助。

##### 基本用法

`tidb-ctl schema in {数据库名}`

如：`tidb-ctl schema in mysql` 将得到以下结果：

```text
[
    {
        "id": 13,
        "name": {
            "O": "columns_priv",
            "L": "columns_priv"
        },
              ...
        "update_timestamp": 399494726837600268,
        "ShardRowIDBits": 0,
        "Partition": null
    }
]
```

结果将以 json 形式展示，内容较长，这里做了截断。

如希望指定表名，可以使用 `tidb-ctl schema in {数据库名} -n {表名}` 进行过滤。

如：`tidb-ctl schema in mysql -n db` 将得到 mysql 库中 db 表的表结构，结果如下：

```text
{
    "id": 9,
    "name": {
        "O": "db",
        "L": "db"
    },
    ...
    "Partition": null
}
```

这里同样做了截断。

如希望指定服务地址，可以使用 `-H -P` 选项，如：`tidb-ctl -H 127.0.0.1 -P 10080 schema in mysql -n db`。