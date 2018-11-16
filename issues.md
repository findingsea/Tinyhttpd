**问题1.** recv 一直是空，返回 -1 ，之前一直以为是没收到数据，打印了 errno 看，才知道是 recv err: Bad file descriptor (9)。

之前传递进去的参数是 `void *arg` 类型，然后用 intptr_t 转成 int，[代码](https://github.com/EZLippi/Tinyhttpd/blob/master/httpd.c#L57)：`int client = (intptr_t)arg;`，但是转成 int 之后就变成负数了，这里的问题在于不应该用 arg 直接转成 int，因为这表示一个地址，应该先把 arg 转成 int*，然后再获取其中的值。arg 直接转 int 使得 client 溢出成了一个负数，而这个值是表示 `file descriptor for the accepted socket`，负数的话，调用 recv 时就会出现 Bad file descriptor 错误。

正确的写法应该是：`int *client = (int *) arg;`。