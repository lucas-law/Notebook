# 内存拷贝函数和mmap的一些坑

对于内存拷贝函数:```void *memcpy(void *dst, void *src, size_t size)``` ，其中的dst 和 src在某些平台(如arm64,android等) 是要求字节对齐的，否则就会出现BUSERROR.
例如，从mmap 映射出来的地址，如果将其拷贝到未对齐的空间，则会发生buserror错误。

解决方法：
拷贝两次，先预分配一块字节对齐的内存，然后在将对齐的内存的buffer中的数据拷贝到目的内存buffer中：
```

static char tempbuffer[ALIGNED_BUFFER_SIZE];
memcpy(tempbuffer, data + 1, ALIGNED_SIZE(copy_len));
memcpy(dst, tempbuffer, copy_len);

```
