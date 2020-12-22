Nu4udwdw7GAq，不知道大家有没有从服务端角度分析过这样的ID是怎么生成的？

1）怎样保证唯一性

2）怎样保证高性能

3）怎样从生成ID转换成这样的字符串

----

设计目标：支持每秒钟生成千万级别的唯一ID

返回字符串要求再8~12个字符，字符要求为英文字母+数字

1）问题分析

> 1s生成1000w个（1s = 1000ms）==> 1ms 生成 1w个
>
> 使用10台Server，10个worker
>
> 那么，问题最终简化为1ms生成100个  2^10=1024

2）问题分解

> 1. 设计一个简单的ID生成器
> 2. 生成器保证线程/进程安全？---数据库，Redis，锁
> 3. 如何实现支持分布式？
> 4. 将ID转换成字符串

3）ID设计参考

> 1. 身份证
> 2. 车牌号   粤B.24KOBE8  分层思想
> 3. UUID
> 4. snowflake

```python
import threading
import os
import time

_worker_id = os.getpid() & 0xFF  # 划分，相当于“粤”，8位（256个进程足够了）
#  & 0xFF：表示取出1个byte

_seq_id = 1
_seq_lock = thread.RLock()

# [8位 server_id] [8位 work_pid] [40位 时间戳ms] [10位 seq_id]
# 8 + 8 + 42 + 10 = 68
# [2]+[4]+[40]+[7]

time.time()  # 保证单调唯一性  ms

def next_seq_id():
	global _seq_id
	with _seq_lock:  # 线程锁: 实现线程安全的自增ID
        _seq_id += 1
        if _seq_id > 1000:
            _seq_id = 1
        return _seq_id

def _cur_ms():
    return int((time.time()) * 1000 - _timestamp_base_ms)
    
def next_guid():
    sever_id = 101          # 主机ID
    seq_id = next_seq_id()  # 序列号
    ms = _cur_ms()          # 时间戳
    
    gid = seq_id
    gid |= (ms << 10)
    gid |= (_worker_id << 52)
    gid |= (_server_id << 60)
    return gid

def _itos(i:int) -> str:
    # ...
    return ans

def next_invit_id():
    return _itos(next_guid())
```

