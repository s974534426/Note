# 1. 小试牛刀
Shell确实很好用，也有很丰富很丰富的功能，在一些简单的任务中，可以使用shell加快效率，但如果比较复杂的任务，还是建议用python写脚本。
## 1. 简介
shell脚本第一行通常是```#! /bin/bash```，这是称为shebang的特殊行(其实没啥用，但好有意思。。。)指定了运行脚本的解释器
运行时有两种方法，一种是```sh script.sh```，这种情况下，其实sh已经指定了解释器，所以shebang其实是没用的
另一种是直接```./script.sh```，这种需要先用```chmod a+x script.sh```赋予可执行权限，然后才能用，这种情况下，shebang就会用于设置使用的解释器
bash中，用分号或换行分割不同命令，用#作为注释

## 2. 终端打印
### 1. echo
echo会自动加换行符，后面的字符可以不加引号，加单引号，加双引号，大体相同，但有一定差别
1. 加双引号，感叹号不能正确显示，需要转义
2. 加单引号，不会对其中的变量求值，比如```echo '$PATH'```显示仍未```$PATH```
3. 不加引号，不能加分号，因为分号是命令分割符

### 2. printf
不会自动换行，但可以像c语言一样使用格式替代符

### 3. 补充
1. echo可以使用-n来忽略默认换行符，使用-e表示可以使用转义字符
2. 彩色输出
巨好玩。。。分为字体颜色码和背景颜色码
字体颜色码：重置0，黑色30，红色31，绿色32，黄色33，蓝色34，洋红35，青色36，白色37
背景颜色码，重置0，然后其他的从40开始和上面对应
```\e[1;31m...\e[0m```
C语言终端也是同理，不过把e换成033

## 3. 变量
可以在shell中直接设置变量，也可以在脚本中设置，但注意```var=1```和```var = 1```是不同的，前一个是赋值，后一个是相等，用于比较，也就是说，在if语句中必须在等号两边有空格

## 4. 数学运算
书上写的太好了，之前为了shell中做个运算，在网上查各种资料，let和\[\]完全说不清
Bash中，可以用let，(())和\[\]执行基本算数运算，用expr和bc帮助高级运算
可以用普通变量赋值的方式定义数值，但会被存储为字符串，处理时需要使用一些其他方法
### 1. let
使用let时，变量名前不需要加\$，并且可以使用自增自减，和加等于。。。
例如```let result=no1+no2```，注意等号两边没有空格(之前提到过原因)
### 2. 方括号
```result=$[ no1 + no2]```
变量前也可以有\$修饰
### 3. 圆括号
变量名之前有\$
### 4. expr
似乎不太好用。。。
一方面是需要使用`符号，就是键盘最左上角的那个，另一方面运算符两边似乎也必须有空格，否则不会进行数值计算
### 5. bc
以上只能用于整数运算，不支持浮点数
给个例子
``` shell
echo "4 * 0.56" | bc
```
![20200307104157.png](https://raw.githubusercontent.com/s974534426/img_for_notes/master/20200307104157.png)
这个还可以用于计算平方根，进制转换等等。。。不过这么复杂的话不如用python脚本吧

## 5. 文件描述符和重定向
1. 用```echo $?```可以看上一个命令有没有成功
2. 如果想将数据重定向到文件同时提供一份数据重定向到标准输入的话，可以用tee命令(注意tee只会接受stdout的输入，stderr不会接受，如果要接受可能就需要```2>&1```)
3. 默认情况下，tee会将文件覆盖，但可以用-a选项，追加内容
4. 书上还介绍了一种重定向脚本内部文本块的方法
5. exec命令可以创建文件描述符

## 6. 数组和关联数组
### 1. 数组
创建数组```array=(1 2 3)```，这个在脚本中是0下标开始的，但好像在命令行中创建是从1开始？？？
打印```echo ${array[0]}```
打印所有值```echo ${array[*]}```
长度```echo ${#array[*]}```

### 2. 关联数组
需要先声明```declare -A array```
添加元素```array=([index1]=1 [index2]=2)```
或是直接添加
列出所有索引```echo ${!array[*]}```

## 7. 别名
## 8. 获取终端信息
用```tput cols```获取列数，```tput lines```获取行数，用```stty -echo```禁止将输入发送到终端，```stty echo```允许发送输出，这在输入密码时很有用

## 9. 日期和延时
主要是date和sleep，比较简单

## 10. 调试脚本

## 11. 函数和参数
定义和使用
```shell
fname()
{
statements
return
}

fname 
fname arg1 arg2
```
在函数内用```$1```等调用参数
函数也可以递归
Fork炸弹，看起来很有意思，自行google。。。
可以用```$?```获取函数返回值

## 12. 读取命令序列输出
1. 管道重定向
2. ```cmd_output=$(COMMANDS)```
3. 反引用``cmd_output=`COMMANDS` ``

后两种在写shell脚本时很好用

### 子shell
用圆括号包括子shell，子shell执行不会对当前shell有任何影响

## 13. 以不按回车的方式读取字符n
read用于读取键盘输入
-n可以控制读取n个字符
-t可以特定时限

## 14. 循环
``` shell
for var in list;
do
commands;
done
```
生成序列时可以用```echo {1..50}```或```echo {a..z}```
``` shell
for((i=0;i<10;i++))
{
commands;
}
```
``` shell
while condition
do
commands;
done
```

## 15. 比较与测试
### if
``` shell
if condition;
then
commands;
elif condition;
then
    commands;
else
    commands;
fi
```
或者我们可以用一些简单的操作，很漂亮的写法，利用逻辑判断符
``` shell
[ condition ] && action;
[ condition ] || action;
```
### 算数比较
条件一般放在封闭中括号中，**注意在[或]与操作数之间一定要有一个空格**
等于-eq
不等于-ne
大于-gt
小于-lt
大于等于-ge
小于等于-le
逻辑与-a
逻辑或-o，这里说的是在方括号内的逻辑与和或，如果连接两个方括号，依然可以使用&&和||
例子：```[ $var1 -ne 0 -a $var2 -ge 2 ]```
其他还有一些复杂的判断

### 字符串比较
使用字符串比较时，最好使用**双中括号**，单个中括号有时会产生错误，最好避开
```[[ $str1 = $str2 ]]```判断是否相等，一个是要用双括号，一个是注意等号两边有空格
```[[ -z $str1 ]]```字符串为空，则返回真
```[[ -n $str1 ]]```字符串非空，则返回真

