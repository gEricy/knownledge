### strcpy / memcpy / memmove

```c
/* 返回值char*: 支持链式表达式 */
char * strcpy(char *dst,const char *src)   /* 形参: const */
{
    assert(dst && src);  /* 健壮性: 检查入参的有效性 */

    /* 采用临时src和dst */
    char *tmp_dst = (char*)dst;
    char *tmp_src = (char*)src;

    // 先将*src拷贝给*dst; 再判断*dst != '\0'; 最后src/dst向后 ++
    while ('\0' != (*tmp_dst++ = *tmp_src++));

    return dst;
}

void* memcpy(void* dst, const void* src, size_t n)
{
    assert(dst && src && n >= 0);

    char *tmp_dst = (char*)dst;
    char *tmp_src = (char*)src;
 
    while(n--) {
        *tmp_dst++ = *tmp_src++;
    }
    
    return dst;
}

void* memmove(void* dst, const void* src, size_t n)
{
    assert(dst && src && n >= 0);
    
    char* tmp_dst = (char*)dst;
    char* tmp_src = (char*)src;

    // 内存重叠: 从后向前逐字拷贝
    if( tmp_dst > tmp_src && (tmp_src + n > tmp_dst) )
    {
        tmp_dst = tmp_dst + n - 1;  
        tmp_src = tmp_src + n - 1;
        while (n--) {
            *tmp_dst-- = *tmp_src--;
        }
    }
    else {  // 内存不重叠: 从前向后逐字拷贝
        while(n--) {
            *tmp_dst++ = *tmp_src++;
        }
    }

    return dst;
}
```

