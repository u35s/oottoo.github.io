# golang 标准库使用

### 并发同步
* sync.Mutex 互斥锁,阻塞其他routine
* sync.RWMutex 读写锁,多个读锁不会阻塞,写锁阻塞routine
* sync.Once 保证只执行一次
* sync.Pool 回收利用，减少GC
* sync.Cond 唤醒睡眠的routine可用
* sync.Map  线程安全的map,原子和互斥锁实现
* sync.WaitGroup 等待一组routine运行完成

### 排序
* sort.Sort 对实现接口的数据排序
* sort.Slice 切片闭包排序

### 可能造成危险的东东
* unsafe.Offsetof  可以获得结构体成员相对于首地址的偏移量
* unsage.Sizeof   可以获得结构体的大小

### 系统相关
* runtime.NumCPU 系统cpu数量
* runtime.GOMAXPROCS 设置工作线程数量

### 系统调用
* syscall.SIGHUP 信号

### 系统
* os.Exit(1) 退出程序

### 容器(链表)
* container

### 上下文
* context 多个routine之间通信,处理超时等情况

### io相关
* 方便Write,Read操作封装

### 编译
* builtin

### 反射
* reflect
