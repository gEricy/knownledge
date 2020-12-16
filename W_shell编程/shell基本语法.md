## 1. 字符串

>  获取字符串的长度
>
> ```shell
> name="abcd"
> echo ${#name} #输出 4
> ```
>
> 提取子字符串
>
> ```shell
> str="runoob is a great site"
> echo ${str:1:4} # 输出 unoo
> ```

## 2. 数组 ( )

括号表示数组，数组元素用 “空格” 符号分开

```shell
# 定义数组
array_name=(value0 value1 value2 value3)
# 读取数组: ${数组名[下标]}
echo ${array_name[n]}
# 获取数组中的所有元素: @符号
echo ${array_name[@]}
# 获取数组的长度
echo ${#array_name[@]}
```

## 3. 单引号 / 双引号

### 1.1.1. 变量是否会失效

单引号里的任何字符都会`原样输出`，单引号字符串中的`变量是无效的`

```shell
name="Tom"
echo "${name}"  # 双引号, 输出 Tom, 变量有效
echo '${name}'  # 单引号, 输出 ${name}, 变量失效
```

### 1.1.2. 能否被转义 ?

```shell
echo "aa\'aa"   # aa\'aa  单引号', 不能被转义 \'
echo "aa\"aa"   # aa"aa   双引号", 可以被转义 \"
```

### 1.1.3. 都能用于拼接字符串

```shell
name="Tom"
# 使用双引号拼接
new_name_1=" hello, "${name}" "
echo ${new_name_1}  # 输出 hello, Tom
# 使用单引号拼接
new_name_2=' hello, '${name}' '
echo ${new_name_2}  # 输出 hello, Tom
```

---



## 4. 流程控制

if条件

```shell
if condition1
then
    command1
elif condition2 
then 
    command2
else
    commandN
fi
```

for循环

```shell
for var in ${var_list}
do
    echo ${var}   
done
```

while循环

```shell
i=10
while [ $i -gt 0 ];do
	echo ${i}
	i=`expr ${i} - 1`  # i=$[${i}-1]
done
```

case多选择

```shell
read aNum
case $aNum in
    1)  echo '你选择了 1'
    ;;
    2)  echo '你选择了 2'
    *)  echo '你没有输入 1 到 2 之间的数字'
    ;;
esac
```

## 5. 函数

```shell
# 定义函数
function run()
{
	local var1=$1
	local var2=$2
	return 0  # 0 表示成功, 1 表示失败
}

# 使用函数
run "param1" "param2" 
res=$?   # 获取上一条命令的执行结果
if [ $res -ne 0 ]; then
	log_error "error: func run, res: ${res}"
fi
```

