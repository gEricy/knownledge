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

##### enum与日志打印

```c
typedef enum {
    TYPE_STAT_INIT = 0,
    TYPE_STAT_START,
    TYPE_STAT_STOP,
    TYPE_STAT_MAX
} type_stat_t;

/* 用于打印日志 */
static char* s_type_stat_str[TYPE_STAT_MAX] = 
{
    [TYPE_STAT_INIT]  = "TYPE_STAT_INIT",
    [TYPE_STAT_START] = "TYPE_STAT_START",
    [TYPE_STAT_STOP]  = "TYPE_STAT_STOP"
};

void print_log()
{
    type_stat_t sts;

    // ...

    logger.info("type_stat[%s], ... ...", s_type_stat_str[sts], ..., ...); // 根据不同的枚举类型，打印日志

    // ...
}
```



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

```c
// 将src所指向的字符串中以src地址开始的前n个字节复制到dest所指的数组中，并返回被复制后的dest
// 返回值为char*，是为了方便，连环使用。
char *strncpy(char *dest, const char *src, int n) --> 参数3：缓冲区长度

注释：如果n < len(src)，只是将src的前n个字符复制到dest的前n个字符，不自动添加'\0'
     此时，结果dest不包括'\0'，需要再手动添加一个'\0'
例：
    strncpy(pastrword, client->chap_in.pastrword, plen);
    strncpy(strd->life_file, life_file, SIZE_256B - 1);
    strncpy(path->dev_path, dev_path, sizeof(path->dev_path) - 1);
    strncpy(sb->sb_strd_uuid, param->strd_uuid, strD_UUID_LEN - 1);

char buf[1024];   // buf 是字符数组
len = sizeof(buf) - 1;

char* buf = "ABCD";   // buf 是指针
len = strlen(buf)    

// 将src所指字符串的前n个字符添加到dest所指字符串的结尾处，并覆盖dest所指字符串结尾的'\0'
// 从而实现字符串的连接。
char * strncat(char *dest, const char *src, size_t n);  --> 参数3：参数2长度

strncat(dest, src, sizeof(src) - 1);
strncat(dest, src, strlen(src));

// 参数2表示：缓冲区dest_str的大小
int snprintf(char* dest_str,size_t size,const char* format,...);

ret = snprintf(path, _MAX_PATH, "%s", dir);
if (ret < 0 || ret >= _MAX_PATH) {
    astrert(ret >= 0 && ret < _MAX_PATH);
    return;
}

snprintf(path + ret, _MAX_PATH - ret, "%c%s", split, fname); 
ret = snprintf(buffer + used, len - used, "cache mode: %s state: %s \n", xxxxx);

# 字符串转换    
atoll(str); ---> strtoll(str, NULL, 10);
```



----

##### **MTU - 发送数据包不得超过MTU**

由于TCP可靠性特点，可知，TCP会对包切片成合适大小再发送，不会出现大于MTU的情况。而UDP无此机制，发送超过MTU大小的包到网络中，有可能超出的包会直接被丢弃

---

##### 定时器用法
定时器的使用经常会发生问题，注意：
定时器可能需要传入一个全局的参数，在启动定时器之前一定要初始化该参数，否则可能会出现访问未初始化的数据，导致踩内存
，一般编码流程如下。

```c
Args* args = ....; // new, assign  创建/赋值
ret = timer_start(func, args);
if (ret == NULL) {
	free(args)
}
```



---

