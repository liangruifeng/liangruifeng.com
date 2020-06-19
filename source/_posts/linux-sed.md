---
title: Linux文本处理三剑客之sed
date: 2018-10-13 22:12:02
tags: [linux]
---

&emsp;&emsp;sed是stream editor(流编辑器)的缩写，是linux中文本处理非常重要的工具。它一次处理一行内容，处理时，把当前处理的行存储在临时缓冲区中，称为“模式空间”(pattern space)，接着用sed命令处理模式空间中的内容，处理完成后，把缓冲区的内容输出，接着处理下一行，这样不断重复，直到文件末尾。文件内容并没有改变，因为这些都在模式空间处理的。sed可以用来自动编辑一个或多个文件。

<!-- more -->

## 1. 命令格式
>sed [options] [command] file(s)

##### [option] 选项
> -n: 仅显示处理后的结果；
-i: 直接修改读取的文件内容，而不是输出到终端；
-e &lt;script&gt;: 以选项中的指定的script来处理输入的文本文件；
-f &lt;script file&gt;: 以选项中指定的script文件来处理输入的文本文件；

##### [command] 命令
> a: 新增，在当前行下面插入文本；
c: 取代， 把选定的行改为新的文本；
d: 删除，删除选择的行；
i: 插入， 在当前行上面插入文本；
p: 打印，打印选择行数据，通常与sed -n一起使用；
s: 替换，替换指定字符串，通常与正则表达式一起使用；

## 2. 用法实例
##### 替换操作
将file文件中每一行第一个的oldStr替换成newStr
> sed 's/oldStr/newStr/' file   

使用后缀g标记会替换每一行中的所有匹配 
> sed 's/oldStr/newStr/g' file 

-n选项和p命令一起使用表示只打印那些发生替换的行    
> sed -n 's/oldStr/newStr/p' 

当需要从第N处匹配开始替换时，可以使用 /Ng
> echo testtesttesttest | sed 's/test/TEST/2'   
> testTESTtesttest

##### 定界符
其中 / 在sed中作为定界符使用，也可以使用任意的定界符：
> sed 's|oldStr|newStr|' file 
> sed 's:oldStr:newStr:' file   

定界符出现在样式内部时，需要进行转义：
> echo /bin | sed 's/\/bin/\/usr\/local\/bin/g'
> /usr/local/bin

##### 删除操作
删除空白行：
> sed '/^$/d' file

删除文件的第2行：
> sed '2d' file

删除文件的第2行到第5行：
> sed '2,5d' file

删除文件中所有开头是test的行：
> sed '/^test/'d file

##### 多点编辑
-e选项允许在同一行里执行多条命令。先删除1至5行，再用test替换TEST：
> sed -e '1,5d' -e 's/test/TEST/' file
    
##### 从文件读入
file里的内容被读进来，显示在与test匹配的行后面，如果匹配多行，则file的内容将显示在所有匹配行的下面：
> sed '/test/r file' filename


##### 写入文件
在example中所有包含test的行都被写入file里
> sed -n '/test/w file' example
