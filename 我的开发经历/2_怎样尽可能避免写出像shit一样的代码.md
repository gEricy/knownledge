写在最前，前段时间有新闻：美国一程序员枪杀同事，原因竟然是同时写代码太恶心。额… …，所以，我也应该时刻提醒自己不能被枪杀。:smile:本文只写了自己容易犯的小错误，并不会一一罗列所有场景。

----

#### 1. 代码规范

##### 命名规范

- 函数采用驼峰式/下划线式都可以，但是要统一命名格式。如: sendMessage，send_message
- 去掉所有的非前置元音（前置元音保留）。如：computer->cmptr
- 变量名：全局变量g_xxx， 全局静态变量s_xxx，普通变量（不用加前缀）

##### 空格保证代码不紧凑，美观

- 双目运算符两边加空格
- for加空格：for(int i = 0; i < MAX_NUM; i++)

##### 注释

```c
/* *
 * @brief: 给事件注册回调函数
 * @param  [out]  ev  事件
 * @param  [in]   cbk 回调函数
 * @param  [in]   arg 回调函数参数
 * */
int32_t
event_assign(struct event *ev,void (*callback)(evutil_socket_t, short, void *), void *arg)
```

##### 魔数magic

不要滥用魔数

##### 折行 \\

字符串无需折行

```c
logger.info("hello"
		   "world");
```

表达式，可直接换行，无需折行

```c
int
event_set(struct event *ev, struct event_base *base,
  		  evutil_socket_t fd, short events);

void test() {
	return xxx ? 
	       yyy : zzz;
}
```

(A) 如果是字符串，可以分成多个以空白符（包括回车换行）分隔的字符串，如"hello world"可以分为"hello" " world"，分割前后的两个字符串是等价的； (B) 如果是表达式（包括参数列表），可以直接分成多行书写，无需折行符； (C) 折行符'\'后应紧跟换行，不允许再出现别的空白符，如' ','\t','\v'等；

---

#### 2. 安全性

##### 宏

多条语句，采用do…while(0)，限制作用域，参数使用时，加小括弧

```c
#define TAILQ_INSERT_TAIL(head, elm, field) do {    \
	(elm)->field.tqe_next = NULL;					\
	(elm)->field.tqe_prev = (head)->tqh_last;		\
	*(head)->tqh_last = (elm);					    \
	(head)->tqh_last = &(elm)->field.tqe_next;		\
} while (0)
```

防命名冲突： 宏内部定义局部变量时，需要加特殊标记

```c
#define TEST(this) do {         \
    int __priv = (this)->priv;  \
} while(0)
```

---

**SQL注入**：不允许将外部传入的参数，直接拼到SQL语句上，执行之

**命令注入**：不允许调用system/popen

**目录穿越**：攻击者能访问任意目录，应该验证字符串中是否包含“截断字符串”、./、../

**缓冲区溢出**：数值int溢出、strncpy拷贝数据溢出、局部变量栈溢出（8M）

----

##### **MTU - 发送数据包不得超过MTU**

由于TCP可靠性特点，可知，TCP会对包切片成合适大小再发送，不会出现大于MTU的情况。而UDP无此机制，发送超过MTU大小的包到网络中，有可能超出的包会直接被丢弃