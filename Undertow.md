# Undertow

```
设置IO线程数, 它主要执行非阻塞的任务,它们会负责多个连接, 默认设置每个CPU核心一个线程
server.undertow.io-threads=16

阻塞任务线程池, 当执行类似servlet请求阻塞IO操作, undertow会从这个线程池中取得线程
它的值设置取决于系统线程执行任务的阻塞系数，默认值是IO线程数*8
server.undertow.worker-threads=256

每块buffer的空间大小,越小的空间被利用越充分，buffer会用于服务器连接的IO操作，不要设置太大
server.undertow.buffer-size=1024

是否分配的直接内存(NIO直接分配的堆外内存)
server.undertow.direct-buffers=true
```

# 测试参数

线程数：10

Ramp-Up(时间：秒)：1  代表每一秒发送10个请求

循环次数：10

# 开发环境

**服务器配置**

```
cpu: 0.5
内存: 1G
核心线程数: 2
```

**第一次测试结果**

![image-20210602184832912](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210602184832912.png)

吞吐量：40.9

**第二次测试结果**

![image-20210602184410392](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210602184410392.png)

吞吐量：40.1

**第三次测试结果**

![image-20210602184520026](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210602184520026.png)

吞吐量：43.2

**平均值：41.4**

# 链路耗时分析

**案例一：**

![image-20210602194904357](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210602194904357.png)

**案例二：**

![image-20210602195203824](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210602195203824.png)

**案例三：**

![image-20210602200026292](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210602200026292.png)