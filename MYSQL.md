# Innodb架构

## 	后台线程

### 		1.Master Thread

​			核心的线程，主要负责将缓存池中的数据异步刷新到磁盘，保证数据的一致性，包括脏页数据、合并插入缓存、undo页的回收``			等。

### 		2.IO Thread 

​			InnoDB 存储引擎大量使用了AIO来处理IO请求，这样可以提高数据库的性能。IO Thread主要负责这些IO请求的回调处理。
​			涉及参数：innodb_read_id_thread 、innodb_write_io_thread

### 		3.purge Thread 

​			用来回收已经使用并分配的undo页。 1.1版本之前purge操作在Master
​			Thread中完成，而从1.1版开始，purge操作可以到单独线程中来处理。
​			涉及参数：innodb_purge_threads 1.1默认为1  不可设置大于1,1，在1.2版可以设置为多个。

### 		4.page thread cleaner 

​			1.2版引入，作用是将脏页的刷新放在单独的线程来执行

## 	内存池

### 		1.缓冲池 innodb_buffer_pool

​			索引页index page、
​			数据页datat page、undo页
​			插入缓存 insert buffer
​			自适应哈希索引
​			innoDB存储的锁信息 lock info
​			数据字典

### 		2.重做日志缓冲池 redo_log_buffer

​			额外缓冲池 innodb_addition_mem_pool_size

### 		3.LRU（Lastt Recent Used，最近最少使用）

​			mysql对LRU进行了一些优化，加入了midpoint位置。新读到的页虽然是最新访问的页
​			但不直接放入LRU列表的首部，而是放在midpoint位置。
​			正常情况下位置在LRU的列表长度的5/8处。  参数：innodb_old_blocks_pct，该参数设置新页插入LRU列表的百分比。
​			主要是为了防止访问全部的页把活跃度高的页挤走。

## 	物理文件





​	