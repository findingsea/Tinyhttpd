**问题1.** recv 一直是空，返回 -1 ，之前一直以为是没收到数据，打印了 errno 看，才知道是 recv err: Bad file descriptor (9)。

之前传递进去的参数是 `void *arg` 类型，然后用 intptr_t 转成 int，但是转成 int 之后就变成负数了，但是这个值是表示 `file descriptor for the accepted socket`，负数的话，调用 recv 时就会出现 Bad file descriptor 错误。