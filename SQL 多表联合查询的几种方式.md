# SQL 多表联合查询的几种方式

最近在项目中用到连接查询，连接查询是关系数据中最主要的查询，包括内连接、外连接等。通过连接运算符可以实现多个表查询。下面来复习一下。

## 正文

连接查询主要分为三种：内连接、外连接、交叉连接。

### 内连接

使用比较运算符（包括`=`、`>`、`<`、`<>`、`>=`、`<=`、`!>` 和`!<`）进行表间的比较操作，查询与连接条件相匹配的数据。根据所使用的比较方式不同，内连接分为等值连接、自然连接和自连接三种。

关键字 `INNER JOIN`

![图片](https://mmbiz.qpic.cn/mmbiz_png/eQPyBffYbufI0iakKuTHvUD6aQjjcj5ZF8Pf3Hn6ubUVibIACTcj5N40OwDpLicBCbaOr5NJOSdd0CaDq0LWUEiaFQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### 等值连接/相等连接

使用”=”关系将表连接起来的查询，其查询结果中列出被连接表中的所有列，包括其中的重复列

```
SELECT
    PM_ACT_JOB_RLS.*, PM_ACT_RLS.*
FROM
    PM_ACT_JOB_RLS
INNER JOIN PM_ACT_RLS ON PM_ACT_JOB_RLS.RlsPK = PM_ACT_RLS.RlsPK
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/eQPyBffYbufI0iakKuTHvUD6aQjjcj5ZFlzAf6AKzyibiaCx6dic1HWEjXvyx0erp6qciaYZHIfykXw8xvTSW4jVgJA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### 自然连接

等值连接中去掉重复的列，形成的连接

```
SELECT
    PM_ACT_JOB_RLS.JobPK,
    PM_ACT_RLS.RlsPK,
    RlsName
FROM
    PM_ACT_JOB_RLS
INNER JOIN PM_ACT_RLS ON PM_ACT_JOB_RLS.RlsPK = PM_ACT_RLS.RlsPK
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/eQPyBffYbufI0iakKuTHvUD6aQjjcj5ZF1AXsu6zuLGJ4GaZ61ffXf8UJEcWWVceKvS2q3mFhxEzFcpxSD7VFBA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### 自连接

如果在一个连接查询中，设计到的两个表都是同一个表，这种查询称为自连接查询。

```
--c1、c2逻辑上是两张表，物理上是一张表
SELECT
    c1.CategoryID,
    c1.CategoryName
FROM
    [dbo].[Category] c1
INNER JOIN [dbo].[Category] c2 ON c1.[CategoryID] = c2.[ParentID]
```

### 外连接

内连接只返回满足连接条件的数据行，外连接不只列出与连接条件相匹配的行，而是列出左表（左外连接时）、右表（右外连接时）或两个表（全外连接时）中所有符合搜索条件的数据行。外连接分为左外连接、右外链接、全外连接三种。

#### 左外连接

返回左表中的所有行，如果左表中行在右表中没有匹配行，则在相关联的结果集中右表的所选择字段均为[NULL](http://mp.weixin.qq.com/s?__biz=MzI4Njc5NjM1NQ==&mid=2247506962&idx=1&sn=087849fd07936de257e4f7c836753076&chksm=ebd5e33edca26a2827fa3a257e138019c97bd760e6ffb9a51697e6484d8af726aded0d97b00b&scene=21#wechat_redirect)。

关键字 `LEFT [OUTER] JOIN`

![图片](https://mmbiz.qpic.cn/mmbiz_png/eQPyBffYbufI0iakKuTHvUD6aQjjcj5ZF1GKTXWB3HZWROHULbdwQz4S78MOibeTEKibqz9QBUibSvCt9Nzibo8Cjdw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

```
--左外连接  --1679条数据
SELECT
    me.*,
    mo.*
FROM
    MM_LOTS_EXT AS me
LEFT OUTER JOIN MM_LOT_OPERATIONS AS mo ON me.LotID = mo.SerialNumber
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/eQPyBffYbufI0iakKuTHvUD6aQjjcj5ZFXBRjo1GtZ0x0oNDPUBU8xibDMKbd1dTuJY1X2tptAtqXiajAriciaSwghg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### 右外连接

返回右表中的所有行，如果右表中行在左表中没有匹配行，则在左表中相关字段返回NULL值。

关键字   `RIGHT [OUTER] JOIN`

```
--右外连接  --209条数据
SELECT
    me.*,
    mo.*
FROM
    MM_LOTS_EXT AS me
RIGHT OUTER JOIN MM_LOT_OPERATIONS AS mo ON me.LotID = mo.SerialNumber
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/eQPyBffYbufI0iakKuTHvUD6aQjjcj5ZFaSy97OYkm9JmcRQ5Lk7zQawvRibPy3wsZHBhYTibFOgaKMxot7fTYgaw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### 全外连接/完全外连接

返回两个连接中所有的记录数据，是左外连接和右外连接的并集。

关键字   `FULL [OUTER] JOIN`

```
--全外连接  --1816条数据 
SELECT
    me.*,
    mo.*
FROM
    MM_LOTS_EXT AS me
FULL OUTER JOIN MM_LOT_OPERATIONS AS mo ON me.LotID = mo.SerialNumber
```

#### 交叉连接/笛卡尔积

两个表做笛卡尔积，得到的结果集的行数是两个表的行数的乘积。

关键字    `CROSS JOIN`

```
--笛卡尔积 (cross join后加条件只能用where,不能用on)  --45条数据（9*5）
SELECT
    *
FROM
    PM_ACT_JOB_RLS
CROSS JOIN PM_ACT_RLS
```

> 注意：带有where条件的子句，往往会先生成两个表行数乘积的数据表，然后从根据where条件从中选择。

当数据量比价大的时候，笛卡尔积操作会很消耗数据库的性能



 