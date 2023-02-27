参考资料：[(31条消息) C语言 --- sizeof() 7种使用详解__玩硬件开发的大叔_的博客-CSDN博客_sizeof](https://blog.csdn.net/zhouml_msn/article/details/103361539)
- sizeof(常量)：注意不同数据类型之间的隐式转换；
- sizeof(字符):大小为1；
- sizeof(字符串)：加上字符串尾部的\0的大小；
- sizeof(数组)：返回数组总字节大小；
- sizeof（结构体）：注意字节对齐；
- sizeof（函数）：函数返回类型的大小，但是不会执行函数；