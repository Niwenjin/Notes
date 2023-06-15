# bash基本语法
* 变量
> `$ var="string"` 定义变量  
> `$ echo "$var"` 查看变量  
> `$ unset "var"` 删除变量
>* 字符串
> > `$ str="str1 ${str2}"` 字符串拼接  
> > `$ echo ${#str}` 获取字符串长度  
> > `$ echo ${str:1:4}` 从字符串第2个字符开始截取4个字符  
> * 数组
> > `$ array=(value0 value1 ...)`/`$ array[n]=valuen` 定义数组  
> > `$ echo ${#array[*]}` 查看数组元素个数
* 流程控制
> * 分支
> > * if分支  
> > <code> if [ condition ]  
> then  
> ...  
> elif [ condition ]  
> then  
> ...  
> else  
> ...  
> fi </code>
> > * case多选择  
> > <code> case $n in  
> 1\) ...  
> ;;  
> 2\) ...  
> ;;  
> *\) ...  
> ;;  
> esac </code>
> * 循环
> > * for循环  
> > <code>for i in item1 item2 ...  
> do  
> ...  
> done</code>
> > * while循环  
> > <code>while [ condition ]  
> do  
> ...  
> done</code>
> > * until循环  
> > <code>until [ condition ]  
> do  
> ...  
> done</code>
* 函数
> <code>func(){  
> ...  
> }</code>
* 重定向
> `$ command >/>> FILE` 将输出重定向（追加）到FILE  
> `$ command < FILE` 将输入重定向到FILE