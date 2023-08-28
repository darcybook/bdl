---
title: "3.多分枝语句和分支语句嵌套"
weight: 3
date: 2023-07-19T19:25:12+08:00
lastmod: 2023-07-19T19:25:12+08:00
tags: ["multi-branch"]
categories: ["multi-branch"]
# bookComments: false
# bookSearchExclude: false
---

# 多分枝语句和分支语句嵌套

讲过了“2选1”， 那如果出现“3选1”， 甚至是“N选1”怎么办?可使用多分支语句和分支语句嵌套。此外，BDL语言还提供了开关语句case,这是一种特殊的选择分支结构，稍后一节会讨论case的用法。本节来看一下分支语句嵌套的问题。

## 多分支

用实例说明似乎更好理解，假设需要编制-一个程序，根据学生的得分判定其类别，具体是:少于60分判为D (不及格)，60到74分判为C (一般)， 75到89分判为B (良好)，大于等于90分判为A (优秀)，学习分支结构后读者编写的代码可能如下所示。

```sql
    define mark integer
    prompt "请输入该学生成绩：" for mark
    if mark >= 90 then
        display "A"
    end if
    if mark >= 75 and mark <90 then
        display "B"
    end if
    if mark >= 60 and mark mark <75 then
        display "C"
    end if
    if mark <60 then
        display "D"
    end if
```

**代码解析**

代码的结果符合要求，但使用4个并列if结构在效率上会大打折扣，画出其流程图，如图所示。
{{< mermaid >}}
flowchart TD
    a(["开始"])-->b["申明变量mark、并输出提示，请用户输入"]
    b-->c[/"接受用户输入"/]
    c-->d{"mark>=90?"}
    d--"yes"-->e["输出'A'"]
    d--"no"-->f{"mark>=75 and mark <90"}
    e-->f
    f--"yes"-->g["输出'B'"]
    f--"no"-->h{"mark>=60 and mark <75"}
    g-->h
    h--"yes"-->i["输出C"]
    h--"no"-->j{"mark<60"}
    i-->j
    j--"yes"-->k["输出'D'"]
    j--"no"-->l(["结束"])
    k-->l

{{< /mermaid >}}

不论用户输入什么样的成绩，都会经过4次判断。有时候，某些判断不是必须的，比如说，如果用户输人的是95，实际上输出A之后程序就可以结束了，后面的3个判断完全是多余的。基于这种思路，可对代码进行改进。


改进的方法有多种:对于以上代码这种判断并执行对应代码块后就结束的程序，可以在代码块中`display`后调用`exit program`函数提前“毙掉”当前程序。

`exit program`其功能是中止程序的执行，并在退出前对程序占用的资源进行必要的清理。
exit是一个无返回值的函数，其参数称为退出码，用以通知操作系统当前程序是正常终止(一般为0)还是非正常终止(一般为-1)。

大多数情况下，在判断后程序并未结束，后面还有代码要执行，可以使用自由跳转语句goto,但这种太过自由的跳转的大量使用会使得程序如一团乱麻，理不清读不顺。因此，很多专家都反对使用goto语句，goto跳转 将在第9章中进行介绍，本节单就分支本身进行讨论。使用多分支if结构或分支结构嵌套来解决这一问题。

## 分支语句嵌套

方if(或者if else)结构红执行语句又是if(或者if else)结构时，称为分支语句嵌套。
分支语句嵌套的样式有千万种，不可能一一列举，举个简单的例子：

```
if 表达式1 then
    if 表达式2 then
        语句1
    else
        语句2
    end if
end if
```

上述代码时在if结构内嵌套了if else结构，首先计算表达式1的值，如果过值为假（0），直接跳过结构，语句1和语句2都不会执行，如果其值为真（非0），则执行内部的if else结构。
计算表达式2的值，如果其值为真，执行语句1，否则执行语句2。

可以看出，分支结构嵌套的执行流程实际上是个剥壳的过程，一层层地做出选择。有的读者可能会对上面的示例有所疑惑，两个if和一个else,它们的搭配关系是怎样的? BDL标准规定:else语句总是和它前面最近的if配对。

## 练习

尝试将判断成绩的分支结构改为嵌套分支结构，减少不必要的判断语句计算。