---
layout: post
title: Building Spark in Intellij 13
categories:

tags:
- Spark
- Intellij
---

为了入职做准备，先是花了两周左右把Scala的两本教材看了一遍（需要再开一篇做个总结），觉得掌握得差不多了就开始啃Spark的原代码。发现相比于LLVM，Spark的文章坐得真不行，好像找不到官方的step-by-step资料教你如何在Intellij中编译整个Spark。当然，我觉得更大的可能是一般的做法应该是直接在命令行里面编译，然后只把Intellij当做编辑器使用。但是这样好像会给追踪和调试代码带来麻烦。anyway，花了一下午时间，终于能跑通examples里面的几个命令了。主要踩到的坑有这么几个：

- scala里面的Quasiquotes好像在Intellij 14中的支持不太好。会出现如下错误。

```
> Error:(42, 21) value q is not a member of StringContext
>     val lengthDef = q"final val length = $tupleLength"
>                     ^
> Error:(54, 7) value q is not a member of StringContext
>       q"""
>       ^
```
原因或者是因为Intellij 14本身不支持，或者是scala 2.10的编译器不支持。网上有帖子说加compiler plugin，我试了，好像还是不行，果断切换到Intellij 13。

- Intellij从13开始支持直接导入sbt工程，因此没有在导入之前使用sbt idea-gen，而是直接从git clone开始，在import的选项中选择sbt工程和auto-import。之后Intellij便能直接生成各种dependencies。完成之后就可以开始按照普通的scala项目的编译试examples里面的几个小代码例子。比较难的地方是会在中途遇见eventBatch和SparkFlumeProtocol没定义的错误。这两个类应该是从Avro文件中利用第三方软件生成的。somehow这个生成没有在编译之前运行。所以手动在external/flume-sink目录下运行mvn generate-sources，产生需要的scala文件，然后继续编译。所以我觉得将Spark导入Intellij成为sbt工程思路一开始就是错的，而是Maven工程可能会避免很多问题。

- exmpales里面的例子。LocalPi直接可以编译运行，SparkPi需要在Run/Edit Configurations里面加入VM options：-Dspark.master=local，即所谓的伪集群模式。

