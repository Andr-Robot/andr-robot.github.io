---
layout: post
title: "Spark SQL中Dataframe join操作含null值的列"
tags: [Spark]
comments: true
---

当在Spark SQL中对两个Dataframe使用join时，当作为连接的字段的值**含有null值**。由于null表示的含义是未知，既不知道有没有，在SQL中null值与任何其他值的比较（即使是null）永远不会为真。故在进行连接操作时**null == null不为True**,所以结果中不会出现该条记录，即左侧表格的这条记录对应**右侧的值均为null**。示例如下：   

**table_a:**     

| date       |  serverId  | lvSection |
| :---------:|:----------:|:---------:|
|2018-03-04  |    1       |      10   |
|2018-03-05  |    null    |    9      |
|2018-03-07  |    5       |      15   |      

**table_b:**   

| date       |  serverId  |  num   |
| :---------:|:----------:|:------:|
|2018-03-04  |    1       |   13   |
|2018-03-05  |    null    |   4    |
|2018-03-07  |    5       |   6    |      

```
Dataset<Row> table_c = table_a.join(table_b, ScalaUtils.getScalaSeq(Lists.newArrayList("date","serverId")), "left")
```      

**table_c:**     

| date       |  serverId  | lvSection |    num   |
| :---------:|:----------:|:---------:|  :------:|
|2018-03-04  |    1       |      10   |     13   |
|2018-03-05  |    null    |    9      |   null   |
|2018-03-07  |    5       |      15   |     6    |      

所以在使用join时应注意作为连接的字段出现null值的情况。

参考文献：    
[Difference between === null and isNull in Spark DataDrame
](https://stackoverflow.com/questions/41533290/difference-between-null-and-isnull-in-spark-datadrame)
