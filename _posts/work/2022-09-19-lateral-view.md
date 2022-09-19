---
layout: article
title: 【Big-Data】Lateral View多列转多行
aside:
  toc: true
key: sql_lateral_view
date: 2022-09-19 09:18:07 +0800
tags:
- 301-work-blog
- 301-work-learning
category: [work, sql, basics]
typora-root-url: ../../../blog

---

# What is Lateral view

> The `LATERAL VIEW` clause is used in conjunction with generator functions such as `EXPLODE`, which will generate a virtual table containing one or more rows. `LATERAL VIEW` will apply the rows to each original output row.

简单来说，就是想把单行映射到产出表的多行，可以使用`lateral view`。

# How to use it

使用指南：[[Hive\]Lateral View使用指南_@SmartSi的博客-CSDN博客_lateral view](https://blog.csdn.net/SunnyYoona/article/details/62894761)

官方文档：[LATERAL VIEW Clause - Spark 3.3.0 Documentation (apache.org)](https://spark.apache.org/docs/latest/sql-ref-syntax-qry-select-lateral-view.html)

# 多列转多行

需求描述：原来表有多列，例如，id, src_a, src_b, src_c，现在转变成id, src，而src是原来三列变成了三行，即多列转多行的操作。

实现方法：[hive实现多列转行_面向搜索引擎写bug的博客-CSDN博客_hive 多列转行](https://blog.csdn.net/weixin_42867475/article/details/108549126)

```sql
select
    a.id,
    b.label,
    b.value
from
    test0912_wkl a LATERAL VIEW explode (
        map(
            'yuwen',
            yuwen,
            'shuxue',
            shuxue,
            'yingyu',
            yingyu
        )
    ) b as label, value
```

```sql
select
    t.uid as src,
    ori as dst
from
    (
        select
            a.id,
            a.email,
            a.phone,
            a.uuid
        from
            tablea a
            join (
                select
                    id,
                    count(distinct uuid) as uuid_count
                from
                    tablea
                where
                    date = '${date}'
                    and id != 'false'
                    and uuid != 0
                    and id is not null
                group by
                    id
            ) b on a.id = b.id
        where
            a.date = '${date}'
        union
        select
            id,
            email,
            phone,
            uuid
        from
            tablea
        where
            date = '${date}'
            and (
                email is not null
                or phone is not null
                or id is not null
            )
            and uuid != 0
    ) t lateral view explode (
        array (
            email,
            id,
            phone
        )
    ) p as ori
```

