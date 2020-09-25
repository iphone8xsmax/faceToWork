[toc]

## 单例模式实现

### 1.懒汉式--非线程安全

非线程安全，即在多线程下可能会创建多次对象

```go
/**
 * 使用结构体代替类
 */
type Tool struct {
    values int
}

/**
 * 建立私有变量
 */
var instance *Tool

/**
 * 获取单例对象的方法，引用传递返回
 */
func GetInstance() *Tool {
    if instance == nil {
        instance = new(Tool)
    }
    return instance
}
```

### 2.懒汉式----线程安全

在非线程安全的基本上，利用Sync.Mutex进行加锁,保证线程安全，但由于每次调用该方法都进行了加锁操作，在性能上相对不高效.

```go
/**
 * 锁对象
 */
var lock sync.Mutex
/**
 * 加锁保证线程安全
 */
func GetInstance() *Tool {
    lock.Lock()
    defer lock.Unlock()
    if instance == nil {
        instance = new(Tool)
    }
    return instance
}
```

### 3.饿汉式

直接创建好对象，这样不需要判断为空，同时也是线程安全。唯一的缺点是在导入包的同时会创建该对象，并持续占有在内存中。

```go
var instance Tool

func GetInstance() *Tool {
    return &instance
}
```

### 4.双重检查

在懒汉式（线程安全）的基础上再进行忧化，判少加锁的操作。保证线程安全同时不影响性能

```go
/**
* 锁对象
*/
var lock sync.Mutex

/**
* 第一次判断不加锁，第二次加锁保证线程安全，一旦对象建立后,获取对象就不用加锁了
*/
func GetInstance() *Tool {
    if instance == nil {
        lock.Lock()

        if instance == nil {
            instance = new(Tool)
        }

        lock.Unlock()
    }

    return instance
}
```

### 5.sync.Once

通过sync.Once 来确保创建对象的方法只执行一次.

```go
var once sync.Once

func GetInstance() *Tool {
    once.Do(func() {
        instance = new(Tool)
    })
    return instance
}
```

sync.Once内部本质上也是双重检查的方式，但在写法上会比自己写双重检查更简洁，以下是Once的源码:

```go
func (o *Once) Do(f func()) {　　　//判断是否执行过该方法，如果执行过则不执行
    if atomic.LoadUint32(&o.done) == 1 {
        return
    }
    // Slow-path.
    o.m.Lock()
    defer o.m.Unlock()　　//进行加锁，再做一次判断，如果没有执行，则进行标志已经扫行并调用该方法
    if o.done == 0 {
        defer atomic.StoreUint32(&o.done, 1)
        f()
    }
}
```