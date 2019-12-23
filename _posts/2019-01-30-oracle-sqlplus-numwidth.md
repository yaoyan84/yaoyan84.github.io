---
layout: post
title:  "Oracle sqlplus长数字不显示为科学记数法"
date:   2019-01-30 10:00:05
categories: oracle
tags:  oracle sqlplus numwidth
excerpt: "Oracle sqlplus长数字不显示为科学记数法"
---

在使用 ```sqlplus``` 查询数据库时，如果数字长度大于10位，默认为记为科学记数法:

```sql
SQL> select 12345678901 value from dual;

     VALUE
----------
1.2346E+10
```

如果不希望显示为科学记数法，则需要更改默认 ```numwidth``` 值即可：

```sql
SQL> set numwidth 12
SQL> select 12345678901 value from dual;

       VALUE
------------
 12345678901
 ```
 
 
 在使用sql查询进行一些大数计算时，想知道精确结果，确实有必要设置一个合适的 ```numwidth``` 值。
