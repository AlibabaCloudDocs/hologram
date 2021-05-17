---
keyword: [HG\_VERSION, Hologres, 工具函数]
---

# HG\_VERSION

HG\_VERSION用于获取当前Hologres的版本。本文将为您介绍在Hologres中HG\_VERSION函数的具体用法。

## 使用限制

仅Hologres V0.10 及以上版本支持HG\_VERSION函数。

## 命令语法

使用HG\_VERSION函数获取当前Hologres的版本的语法如下：

```
select hg_version()
```

输出结果会显示当前Hologres实例版本、兼容的Psql版本，当前操作系统操作系统信息和编译器等信息。

## 使用示例

您可以在当前连接输入如下命令：

```
select hg_version()
```

输出结果如下所示：

```
                                                           hg_version                   
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
 Hologres 0.9.29 (tag: 0.9.29 build: Release,Skylake,clang), compatible with PostgresSQL 11.3 (Release-build@1b725dxxxx on origin/ralease-0.9.x) on x86_64-pcplinux-gnu, compiled by clang version 8.0.1 (Alibaba 8.0.1-14.alios7),64-bit
(1 row)
```

其中，输出结果显示当前Hologres实例版本为0.9.29、兼容Psql的11.3版本，当前操作系统是Linux下64位操作系统。

