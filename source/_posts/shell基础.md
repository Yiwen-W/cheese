---
title: shell基础
date: 2017-05-21 16:20:01
tags: Linux
categories: Linux
---
# Shell基础
## 什么是Shell
- Shell是一个命令行解释器，它为用户提供了一个向Linux内核发送请求以便运行程序的界面系统级程序，用户可以用Shell来启动，挂起，停止甚至是编写一些程序
- Shell还是一个功能相当强大的编程语言，易编写，易调试，灵活性较强。Shell是解释执行的脚本语言，在Shell中可以直接调用Linux系统命令
![](https://leanote.com/api/file/getImage?fileId=5926c867ab6441649b001db4)
## Shell的分类
- Bourne Shell：从1979其Unix就开始使用Bourne Shell。Bourne Shell的主文件名为sh，基本淘汰了
- C Shell：C Shell主要在BSD版的Unix系统中调用，其语法跟C语言相类似
- Shell的两种主要语法类型有Bourne和C，这两种语法彼此不兼容，Bourne家族主要包括sh,ksh,Bash,psh,zsh；C家族主要包括：csh，tcsh
![](https://leanote.com/api/file/getImage?fileId=59227ad9ab64415b23000d6c)
- Bash:Bash与sh兼容，现在使用的linux就是使用Bash作为用户的基本Shell
## Linux支持哪些Shell
![](https://leanote.com/api/file/getImage?fileId=59227ad9ab64415b23000d6a)

# 脚本执行方法
## echo命令
- echo [选项] [输出内容]
- 选项：
  - -e:支持反斜线控制的字符转换
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d71)

- 控制字符
|控制字符|作用|
|:-:|:-:|
|\a|输出警告音|
|\b|退格键，也就是向左删除键|
|\n|换行符|
|\r|回车键|
|\t|制表符，也就是Tab键|
|\v|垂直制表符|
|\0nnn|按照八进制ASCII码表输出字符，其中0位数字零，nnn是三位八进制数|
|\xhh|按照十六进制ASCII码表输出字符。其中hh是两位十六进制数|

![](https://leanote.com/api/file/getImage?fileId=59227ad9ab64415b23000d6f)
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d7c)
![](https://leanote.com/api/file/getImage?fileId=59227ad9ab64415b23000d6b)
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d81)

- \e[1;31m 开启颜色显示
- \e[0m 取消颜色显示
- 颜色：
  - 30m=黑色 31m=红色 32m=绿色 33m=黄色
  - 34m=蓝色 35m=洋红 36m=青色 37m=白色
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d7f)

## 第一个脚本
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d82)
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d73)
## 脚本执行
- 赋予执行权限，直接运行
  - chmod 755 hello.sh
  - ./hello.sh
- 通过bash调用执行脚本
  - bash hello.sh
![](https://leanote.com/api/file/getImage?fileId=59227ad9ab64415b23000d6e)
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d72)

# Bash的基本功能
## 命令别名与快捷键
### 查看与设定别名
- alias #查看系统中所有的命令别名
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d76)
- alias 别名=“原命令” #设定命令别名
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d84)
### 别名永久生效与删除别名
- vi ~/.bashrc #写入环境变量配置文件
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d75)
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d7a)
- unalias 别名 #删除别名 零时删除，永久删除需要再配置文件中更改
### 命令生效的顺序
- 第一顺位执行用绝对路径或相对路径执行的命令
- 第二顺位执行别名
- 第三顺位执行Bash的内部命令
- 第四顺位执行按照$PATH环境变量定义的目录查找顺序找到第一个命令
别名>原始命令

# 快捷键
- ctrl+c 强制终止当前命令
- ctrl+l 清屏
- ctrl+a 光标移动到命令行首
- ctrl+e 光标移动到命令行尾
- ctrl+u 从光标所在位置删除到行首
- ctrl+z 把命令放入后台
- ctrl+r 在历史命令中搜素

# 历史命令
## 历史命令
- history [选项][历史命令保存文件]
- 选项：
  - -c:清空历史命令
  - -w:把缓存中的历史命令写入历史命令保存文件~/.bash_history
- 默认保存的是1000条
- 如果想要修改，vi /etc/profile
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d70)
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d80)
## 历史命令的调用
- 使用上下箭头调用以前的历史命令
- 使用“!n”重复执行第n条历史命令
- 使用“!!”重复执行上一条命令
- 使用“!子串”重复执行最后一条以该子串开头的命令

# 命令与文件补全
在Bash中，命令与文件补全是非常方便与常用的功能，我们只要在输入命令或文件时，按“Tab”键就会自动进行补全

# 输出重定向
## 标准输入输出
|设备|设备文件名|文件描述符|类型|
|:-:|:-:|:-:|:-:|
|键盘|/dev/stdin|0|标准输入|
|显示器|/dev/sdtout|1|标准输出|
|显示器|/dev/sdterr|2|标准错误输出|
## 输出重定向
|类型|符号|作用|
|:-:|:-:|:-:|
|标准输出重定向|命令 > 文件|以覆盖的方式，把命令的正确输出输出到指定的文件或设备当中|
||命令 >> 文件|以追加的方式，把命令的正确输出输出到指定的文件或设备当中|
|标准错误输出重定向|错误命令 2> 文件|以覆盖的方式，把命令的错误输出输出到指定的文件或设备当中|
||错误命令 2>> 文件|以追加的方式，把命令的错误输出输出到指定的文件或设备当中|
|正确输出和错误输出同时保存|命令 > 文件 2>&1 |以覆盖的方式，把正确输出和错误输出都保存到同一文件当中|
||命令 >> 文件 2>&1 |以追加的方式，把正确输出和错误输出都保存到同一文件当中|
||命令 &>文件|以覆盖的方式，把正确和错误输出都保存到同一文件当中|
||命令 &>>文件|以追加的方式，把正确和错误输出都保存到同一文件当中|
||命令>>文件1 2>>文件2|把正确的输出追加到文件1中，把错误的输出追加到文件2中|
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d7b)
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d74)
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d83)
![](https://leanote.com/api/file/getImage?fileId=59227ad9ab64415b23000d6d)
# 输入重定向
- [root@localhost~]# wc [选项][文件名]
- 选项：
  - -c统计字节数
  - -w统计单词数
  - -l统计行数
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d79)
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d77)
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d7e)

# 管道符
## 多命令顺序执行
|多命令执行符|格式|作用|
|::|::|::|
|;|命令1：命令2|多个命令顺序执行，命令之间没有任何逻辑联系|
|&&|命令1&&命令2|逻辑与 当命令1正确执行，则命令2才会执行，当命令1执行不正确，则命令2不会执行|
| \|\| |命令1\|\|命令2|逻辑或 当命令1执行不正确，则命令2才会执行|
![](https://leanote.com/api/file/getImage?fileId=59227adaab64415b23000d78)
![](https://leanote.com/api/file/getImage?fileId=592a7175ab64415a1e00121e)
![](https://leanote.com/api/file/getImage?fileId=592a7175ab64415a1e001221)
## 管道符
- 命令格式
  - [root@localhost~]#命令1 | 命令2  `#命令1的正确输出作为命令2的操作对象`
![](https://leanote.com/api/file/getImage?fileId=592a7175ab64415a1e00121f)
![](https://leanote.com/api/file/getImage?fileId=592a7175ab64415a1e001222)
![](https://leanote.com/api/file/getImage?fileId=592a7175ab64415a1e001220)

# 通配符
|通配符|作用|
|::|::|
|?|匹配一个任意字符|
|*|匹配0个或任意多个任意字符，也就是可以匹配任何内容|
|[]|匹配中括号中任意一个字符。例如：[abc]代表一定匹配一个字符，或者是a,或者是b,或者是c|
|[-]|匹配中括号中任意一个字符，-代表一个范围。例如：[a-z]代表匹配一个小写字母|
|[^]|逻辑非，表示匹配不是中括号内的一个字符，例如：[^0-9]代表匹配一个不是数字的字符|


- Bash中的其他符号
  - 单引号‘’：在单引号中所有的特殊符号，如$，`（反引号）都没有特殊含义。
  
  - 双引号“”：在双引号中特殊符号都没有特殊含义，但是$,`,\是例外，拥有“调用变量”，“引用命令”，和“转义符”的特殊含义
  
  - 反引号``:反引号括起来的内容是系统命令，在Bash中会先执行它，和$()作用一样，不过推荐使用$()，因为反引号容易看错
  - \$():和反引号作用一样，用来引用系统命令
  - #:在shell脚本中，#开头的行代表注释
  - \$:用于调用变量的值，如需要调用变量name的值时，需要用$name的方式得到变量的值

  - \\\:转义符，跟在\之后的特殊符号将失去特殊含义，变为普通字符，如\$将输出“$”符号，而不是当变量引用。

![](https://leanote.com/api/file/getImage?fileId=592a7175ab64415a1e00121d)
