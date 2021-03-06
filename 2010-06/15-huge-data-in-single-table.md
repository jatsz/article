单表数据量过大处理策略
========

今天和一个朋友在讨论怎么样应对单表数据量过大，比如一些交易数据，每天都有10W的交易量。没有多久该表的查询，插入速度将变慢，最终将不可用。

对于关系数据库来说，应对单表数据量过大的策略大体上有两种。

1. 分表。
2. 归档历史数据。

### 1，分表

在一些场景下可以将不同年份的数据放至不同的表格中。比如2004年的数据放至Table-2004，而2005年的数据放至Table-2005中。或者自定义一些其它规则。

总体是思想是通过规则，将数据分别放至不同的表中，以证单表数据量不会太大。

应用此技术时，可以通过一些中间手段将这些中间表的细节隐藏起来，比如SQL Server中的分布式视图。

### 2，归档历史数据

还有一种策略是定期Archive历史数据，比如每年的年底，将上一年Production DB的数据Archive至History DB中。

比如在2004年的年底，我们将2002年的数据Archive至History DB；

在2005年的年底时将2003年的数据Archive至History DB。

总体思想是定期Archive数据，保证当时表格中的数据量在一定范围。以此来保证Production DB的速度，同时也可以查询/更新History DB的数据（查询/更新的速度可能很慢）。

应用此技术时，可以通过一些数据库重定向技术。在Production DB查询更新/失败时，去History DB进行查询/更新。

**适用场景**

1. 分表操作适用于数据量巨大，但查询更新分布很平均。比如身份证系统，每个人可能被查到的概率大致相等。
2. 归档历史数据适用于数据量巨大，但查询分布有规律——最近的数据最常被使用。比如我们的生产数据，5年前的数据基本不用，即使是用了只涉及到查询。这样我们就可以将5年前的数据归档至History DB中并进行查询的优化。

