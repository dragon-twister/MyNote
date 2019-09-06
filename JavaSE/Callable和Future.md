[toc]
# Callable和Future

## What

在Java中一般通过继承Thread类或者实现Runnable接口这两种方式来创建多线程，但是这两种方式都有个缺陷，就是不能在执行完成后获取执行的结果。

因此Java 1.5之后提供了Callable和Future接口，通过它们就可以在任务执行完毕之后得到任务的执行结果。