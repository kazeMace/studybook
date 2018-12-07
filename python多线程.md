
# Python 多线程

## 线程状态
创建线程之后，线程并不是始终保持一个状态。其状态大概如下：
- New 创建
- Runnable 就绪 等待调度
- Running 运行
- Blocked 阻塞
- Dead 消亡

## 线程类型
- 主线程
- 子线程
- 守护线程（后台线程）
- 前台线程

## threading模块
在python3中支持多线程的模块主要有```_thread```和```threading```
#### <p style="color:red">在python3中推荐使用threading模块进行多线程编程</p>
```python
import threading
```
#### 推荐使用更高级别的threading模块，而不使用_thread模块。
- 1、threading模块更加高级，有更好的线程支持，_thread模块中的一些属性会和threading模块有冲突。
- 2、更低级别的_thread模块拥有的同步原语很少（只有一个），而threading模块有很多
- 3、_thread模块不支持守护线程这个概念。当主线程退出时，所有子线程都将终止，不管他们是否仍在工作。threading模块支持守护线程。
#### 什么是守护线程？
- 守护线程一般就是一个等待客户端请求服务的服务器。如果没有客户端请求，守护线程就是空闲的。如果把一个线程设置为一个守护线程，就表示这个线程是不重要的，进程退出时不需要等待这个线程执行完成。
- 如果主线程准备退出时，不需要等待某些子线程完成，就可以为这些子线程设置守护线程标记。该标记值为真是，表示该线程是不重要的，或者说该线程只是用来等待客户端请求和不做任何其他事情。
- 守护线程是运行在后台的一种特殊线程。它独立于控制终端并且周期性地执行某种任务或等待处理某些发生的事件。
- 守护线程可能会突然停止。但他们的资源（例如打开的文件，数据库事务等）可能无法正确释放。如果想让线程正常停止，不能将这些线程非守护线程，并使用一个合适的信号机制，如 Event。
#### 要将一个线程设置为守护线程就需要在启动线程之前执行：
```python
thread.deamon = True
```

### 接下来介绍threading模块

#### threading模块中的对象

| 对象 | 描述 |
|:---|:---|
| Thread | 表示一个执行进程的对象 |
| Lock| 锁原语对象|
| RLock | 可重入锁对象|
| Event | 条件变量的通用版本，任意数量的线程等待某个事件的发生，在该时间发生后所有线程都被激活|
| Semaphore | 为线程间共享的有限资源提供一个“计数器”，如果没有可用资源是会被阻塞|
| BoundSemaphore | 与Semaphore相似，不过它不允许超过初始值|
| Timer | 与Thread相似，不过它要在运行前等待一段时间|
| Barrier | 创建一个“障碍”，必须达到指定数量的线程后才可以继续|

#### threading模块中的函数

|函数|描述|
|:--|:--|
|active_count()|当前活动的Thread对象个数|
|current_thread()|返回当前的Thread对象|
|enumerate()|返回当前活动的Thread对象|
|settrace(func)|为所有线程设置一个trace函数|
|setprofile(func)|为多有线程设置一个profile函数|
|stack_size(size=0)|返回新创建线程的栈大小；货位后续创建的线程设定栈的大小为size

### Thread类
threading模块的Thread类是主要的执行对象。
#### Thread的构造函数：    
    Thread(group=None, target=None, name=None, args=(), kwargs={}，daemon= None)
    group: 线程组，目前还没有实现，库引用中提示必须是None；
    target: run()要执行的方法，默认为None，意味着什么都不调用；
    name: 线程名，默认情况下是Thread-N，其中N是创建线程的顺序，是一个十进制数字；
    args/kwargs: 要传入方法的参数；
    args: 调用的函数的参数元组；
    kwargs: 调用的函数的参数字典；
    daemon: 如果不是None，daemon显示的设置线程是否为守护线程，如果是None（默认），守护线程的属性是从当前线程继承的。如果线程是有主线程创建的，则默认是不是守护线程。

#### Thread的创建

##### 方法1：使用线程的构造函数，将要执行的方法作为参数传给Thread的构造方法


```python
import threading
def func():
    print('func() passed to Thread')
t = threading.Thread(target=func)
t.start()
```

    func() passed to Thread
    

##### 方法2：从Thread继承，并重写run()（只能重写__init__()和run()方法）


```python
class MyThread(threading.Thread):
    def run(self):
        print('MyThread extended from Thread')
t = MyThread()
t.start()
```

    MyThread extended from Thread
    

#### Thread的主要方法有以下几种：
- Thread.start():  启动线程，每个线程最多被调用一个，如果被调用多次则会报RuntimeError错误（RuntimeError: threads can only be started once）。看一下start()方法的源码；
- Thread.run()： 表示线程执行的方法，可以重写该方法，run()方法执行的函数是创建线程时构造方法中target的值，如果定义了，run()方法会在args和kwargs中获取参数
- Thread.join(timeout=None): 等待，直到调用线程终止。当timeout参数存在且不是None时，应该是一个浮点数，表示操作的秒数。timeout参数设置原线程被阻塞的时间，如果为None，则表示需要调用线程终止才能继续执行。



#### 举个例子


```python
#python3
import time, threading


def loop():
    print('线程 %s 正在运行...' % threading.current_thread().name)
    n = 0
    while n<5:
        n = n+1
        print('线程 %s >>> %s' % (threading.current_thread().name, n))
        time.sleep(1)
        print('线程 %s 结束运行.' % threading.current_thread().name)
print('线程 %s 正在运行...' % threading.current_thread().name)
t1 = threading.Thread(target=loop, name='LoopThread1') #创建线程LoopThread1
t2 = threading.Thread(target=loop, name='LoopThread2') #创建线程LoopThread2
t1.start() #启动线程LoopThread1
t2.start() #启动线程LoopThread2
t1.join()
t2.join()
print('线程 %s 结束运行.' % threading.current_thread().name)
```

    线程 MainThread 正在运行...
    线程 LoopThread1 正在运行...
    线程 LoopThread1 >>> 1
    线程 LoopThread2 正在运行...
    线程 LoopThread2 >>> 1
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 2
    线程 LoopThread2 结束运行.
    线程 LoopThread2 >>> 2
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 3
    线程 LoopThread2 结束运行.
    线程 LoopThread2 >>> 3
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 4
    线程 LoopThread2 结束运行.
    线程 LoopThread2 >>> 4
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 5
    线程 LoopThread2 结束运行.
    线程 LoopThread2 >>> 5
    线程 LoopThread1 结束运行.
    线程 LoopThread2 结束运行.
    线程 MainThread 结束运行.
    

在这个例子中：
- 一共创建了两个线程，分别是LoopThread1和LoopThread2，两个线程交替的执行
- 每个线程都执行了5次循环
- 这里的time.sleep(1)表示在一个线程print了之后，会休眠1秒钟，而这是后可能会有其他的线程在执行。如果把sleep()这条语句去掉：


```python
#python3
import time, threading


def loop():
    print('线程 %s 正在运行...' % threading.current_thread().name)
    n = 0
    while n<5:
        n = n+1
        print('线程 %s >>> %s' % (threading.current_thread().name, n))
#         time.sleep(1)
        print('线程 %s 结束运行.' % threading.current_thread().name)
print('线程 %s 正在运行...' % threading.current_thread().name)
t1 = threading.Thread(target=loop, name='LoopThread1') #创建线程LoopThread1
t2 = threading.Thread(target=loop, name='LoopThread2') #创建线程LoopThread2
t1.start() #启动线程LoopThread1
t2.start() #启动线程LoopThread2
t1.join()
t2.join()
print('线程 %s 结束运行.' % threading.current_thread().name)
```

    线程 MainThread 正在运行...
    线程 LoopThread1 正在运行...
    线程 LoopThread1 >>> 1
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 2
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 3
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 4
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 5
    线程 LoopThread1 结束运行.
    线程 LoopThread2 正在运行...
    线程 LoopThread2 >>> 1
    线程 LoopThread2 结束运行.
    线程 LoopThread2 >>> 2
    线程 LoopThread2 结束运行.
    线程 MainThread 结束运行.线程 LoopThread2 >>> 3
    线程 LoopThread2 结束运行.
    
    线程 LoopThread2 >>> 4
    线程 LoopThread2 结束运行.
    线程 LoopThread2 >>> 5
    线程 LoopThread2 结束运行.
    

我们可以看出，由于线程运行的时间非常快，远远小于1秒。所以在这次的执行过程中，所有的线程1都执行完后线程2才开始执行

---

使用上面的例子看一下Thread对象的其他属性和方法和threading的属性和方法

##### Thread.name
返回当前Thread的线程名，同getName()方法


```python
#python3
import time, threading


def loop():
    print('线程 %s 正在运行...' % threading.current_thread().name)
    n = 0
    while n<5:
        n = n+1
        print('线程 %s >>> %s' % (threading.current_thread().name, n))
        time.sleep(1)
        print('线程 %s 结束运行.' % threading.current_thread().name)
print('线程 %s 正在运行...' % threading.current_thread().name)
t1 = threading.Thread(target=loop, name='LoopThread1') 
t2 = threading.Thread(target=loop, name='LoopThread2') 
t1.start() 
t2.start() 
t1.join()
t2.join()
print('线程 %s 结束运行.' % threading.current_thread().name)
```

    线程 MainThread 正在运行...
    线程 LoopThread1 正在运行...
    线程 LoopThread1 >>> 1
    线程 LoopThread2 正在运行...
    线程 LoopThread2 >>> 1
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 2
    线程 LoopThread2 结束运行.
    线程 LoopThread2 >>> 2
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 3
    线程 LoopThread2 结束运行.
    线程 LoopThread2 >>> 3
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 4
    线程 LoopThread2 结束运行.
    线程 LoopThread2 >>> 4
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 5
    线程 LoopThread2 结束运行.
    线程 LoopThread2 >>> 5
    线程 LoopThread1 结束运行.
    线程 LoopThread2 结束运行.
    线程 MainThread 结束运行.
    

这里的LoopThread1，LoopThread1和MainThread都是本程序中包含的线程的名字
##### 这里只创建了两个线程，为什么会有三个进程名？
执行python程序时会自动创建一个名为MainThread主线程实例， 子线程的名字需要在创建线程时指定。如果在创建子线程时未指定子线程名称，python会根据线程的创建顺序为线程起名为Thread-n。


```python
#python3
import time, threading


def loop():
    print('线程 %s 正在运行...' % threading.current_thread().name)
    n = 0
    while n<5:
        n = n+1
        print('线程 %s >>> %s' % (threading.current_thread().name, n))
        time.sleep(1)
        print('线程 %s 结束运行.' % threading.current_thread().name)
print('线程 %s 正在运行...' % threading.current_thread().name)
t1 = threading.Thread(target=loop) 
t2 = threading.Thread(target=loop) 
t1.start() 
t2.start() 
t1.join()
t2.join()
print('线程 %s 结束运行.' % threading.current_thread().name)
```

    线程 MainThread 正在运行...
    线程 Thread-8 正在运行...
    线程 Thread-8 >>> 1线程 Thread-9 正在运行...
    
    线程 Thread-9 >>> 1
    线程 Thread-8 结束运行.
    线程 Thread-8 >>> 2
    线程 Thread-9 结束运行.
    线程 Thread-9 >>> 2
    线程 Thread-8 结束运行.
    线程 Thread-8 >>> 3
    线程 Thread-9 结束运行.
    线程 Thread-9 >>> 3
    线程 Thread-8 结束运行.
    线程 Thread-8 >>> 4
    线程 Thread-9 结束运行.
    线程 Thread-9 >>> 4
    线程 Thread-8 结束运行.
    线程 Thread-8 >>> 5
    线程 Thread-9 结束运行.
    线程 Thread-9 >>> 5
    线程 Thread-8 结束运行.
    线程 Thread-9 结束运行.
    线程 MainThread 结束运行.
    

##### Thread.is_alive()
返回值类型为布尔型(True, False)，表示当前线程是否是活动状态


```python
#python3
import time, threading


def loop():
    print('线程 %s 正在运行...' % threading.current_thread().name)
    n = 0
    while n<5:
        n = n+1
        print('线程 %s >>> %s' % (threading.current_thread().name, n))
        print('线程 %s 是alive的: %s' %(threading.current_thread().name,threading.current_thread().is_alive()))
        time.sleep(1)
        print('线程 %s 结束运行.' % threading.current_thread().name)
print('线程 %s 正在运行...' % threading.current_thread().name)
t1 = threading.Thread(target=loop, name='LoopThread1') 
t2 = threading.Thread(target=loop, name='LoopThread2') 
t1.start() 
t2.start() 
t1.join()
t2.join()
print('线程 LoopThread1 是alive的: %s' %t1.is_alive())
print('线程 LoopThread2 是alive的: %s' %t2.is_alive())
print('线程 %s 结束运行.' % threading.current_thread().name)
```

    线程 MainThread 正在运行...
    线程 LoopThread1 正在运行...
    线程 LoopThread1 >>> 1
    线程 LoopThread1 是alive的: True
    线程 LoopThread2 正在运行...
    线程 LoopThread2 >>> 1
    线程 LoopThread2 是alive的: True
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 2
    线程 LoopThread1 是alive的: True
    线程 LoopThread2 结束运行.
    线程 LoopThread2 >>> 2
    线程 LoopThread2 是alive的: True
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 3
    线程 LoopThread1 是alive的: True
    线程 LoopThread2 结束运行.
    线程 LoopThread2 >>> 3
    线程 LoopThread2 是alive的: True
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 4
    线程 LoopThread1 是alive的: True
    线程 LoopThread2 结束运行.
    线程 LoopThread2 >>> 4
    线程 LoopThread2 是alive的: True
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 5
    线程 LoopThread1 是alive的: True
    线程 LoopThread2 结束运行.
    线程 LoopThread2 >>> 5
    线程 LoopThread2 是alive的: True
    线程 LoopThread1 结束运行.
    线程 LoopThread2 结束运行.
    线程 LoopThread1 是alive的: False
    线程 LoopThread2 是alive的: False
    线程 MainThread 结束运行.
    

我们可以看出线程在执行的时候alive的返回值是True，但线程结束时，返回值是false

##### Thread.daemon
返回值类型也为布尔型，表示当前线程是否为守护线程


```python
#python3
import time, threading


def loop():
    print('线程 %s 正在运行...' % threading.current_thread().name)
    n = 0
    while n<5:
        n = n+1
        print('线程 %s >>> %s' % (threading.current_thread().name, n))
        time.sleep(1)
        print('线程 %s 结束运行.' % threading.current_thread().name)
print('线程 %s 正在运行...' % threading.current_thread().name)
t1 = threading.Thread(target=loop, name='LoopThread1',daemon=True) 
t2 = threading.Thread(target=loop, name='LoopThread2') 
t1.daemon = True
t1.start() 
t2.start() 
print('线程t1是守护线程: %s'%t1.daemon)
print('线程t2是守护线程: %s'%t2.daemon)
t1.join()
t2.join()
print('线程 %s 结束运行.' % threading.current_thread().name)
```

    线程 MainThread 正在运行...
    线程t1是守护线程: False线程 LoopThread1 正在运行...线程 LoopThread2 正在运行...
    
    
    线程t2是守护线程: False线程 LoopThread1 >>> 1线程 LoopThread2 >>> 1
    
    
    线程 LoopThread2 结束运行.线程 LoopThread1 结束运行.
    
    线程 LoopThread2 >>> 2线程 LoopThread1 >>> 2
    
    线程 LoopThread2 结束运行.
    线程 LoopThread2 >>> 3
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 3
    线程 LoopThread2 结束运行.
    线程 LoopThread2 >>> 4
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 4
    线程 LoopThread2 结束运行.
    线程 LoopThread2 >>> 5
    线程 LoopThread1 结束运行.
    线程 LoopThread1 >>> 5
    线程 LoopThread2 结束运行.
    线程 LoopThread1 结束运行.
    线程 MainThread 结束运行.
    

将一个线程设置为守护线程，需要在创建线程的时候指定，或在线程start()之前显示的设置为守护线程

##### Thread.ident
返回当前线程的“线程标识符”或None，当线程结束并创建另一个线程时，线程标识符会被回收


```python
#python3
import time, threading


def loop():
    print('线程 %s 正在运行...' % threading.current_thread().name)
    n = 0
    while n<5:
        n = n+1
        print('线程 %s >>> %s' % (threading.current_thread().name, n))
        time.sleep(1)
        print('线程 %s 结束运行.' % threading.current_thread().name)
print('线程 %s 正在运行...' % threading.current_thread().name)
t1 = threading.Thread(target=loop) 
t2 = threading.Thread(target=loop) 
t1.start() 
t2.start() 
print('t1线程的线程标识符为: %d' %t1.ident)
print('t2线程的线程标识符为: %d' %t2.ident)
t1.join()
t2.join()
print('线程 %s 结束运行.' % threading.current_thread().name)
```

    线程 MainThread 正在运行...
    线程 Thread-10 正在运行...
    t1线程的线程标识符为: 8612线程 Thread-11 正在运行...线程 Thread-10 >>> 1
    
    
    t2线程的线程标识符为: 812线程 Thread-11 >>> 1
    
    线程 Thread-10 结束运行.
    线程 Thread-10 >>> 2
    线程 Thread-11 结束运行.
    线程 Thread-11 >>> 2
    线程 Thread-10 结束运行.
    线程 Thread-10 >>> 3
    线程 Thread-11 结束运行.
    线程 Thread-11 >>> 3
    线程 Thread-10 结束运行.
    线程 Thread-10 >>> 4
    线程 Thread-11 结束运行.
    线程 Thread-11 >>> 4
    线程 Thread-10 结束运行.
    线程 Thread-10 >>> 5
    线程 Thread-11 结束运行.
    线程 Thread-11 >>> 5
    线程 Thread-10 结束运行.
    线程 Thread-11 结束运行.
    线程 MainThread 结束运行.
    

##### threading.activeCount()
回当前活动（运行）的线程的数量(在pycharm中运行test1.py)


```python
#python3
import time, threading


def loop():
    print('线程 %s 正在运行...' % threading.current_thread().name)
    n = 0
    while n<5:
        n = n+1
        print('线程 %s >>> %s' % (threading.current_thread().name, n))
        time.sleep(1)
        print('线程 %s 结束运行.' % threading.current_thread().name)
print('线程 %s 正在运行...' % threading.current_thread().name)
t1 = threading.Thread(target=loop) 
t2 = threading.Thread(target=loop) 
t1.start() 
t2.start() 
print('当前有 %d 个线程是活动的' %threading.active_count())
t1.join()
t2.join()
print('线程 %s 结束运行.' % threading.current_thread().name)
```

    线程 MainThread 正在运行...
    线程 Thread-12 正在运行...当前有 7 个线程是活动的
    线程 Thread-13 正在运行...
    线程 Thread-12 >>> 1
    
    线程 Thread-13 >>> 1
    线程 Thread-13 结束运行.线程 Thread-12 结束运行.
    
    线程 Thread-13 >>> 2线程 Thread-12 >>> 2
    
    线程 Thread-13 结束运行.
    线程 Thread-12 结束运行.线程 Thread-13 >>> 3
    
    线程 Thread-12 >>> 3
    线程 Thread-13 结束运行.
    线程 Thread-13 >>> 4
    线程 Thread-12 结束运行.
    线程 Thread-12 >>> 4
    线程 Thread-13 结束运行.
    线程 Thread-13 >>> 5
    线程 Thread-12 结束运行.
    线程 Thread-12 >>> 5
    线程 Thread-13 结束运行.
    线程 Thread-12 结束运行.
    线程 MainThread 结束运行.
    

该语句在程序中不同的位置得到的结果是不同的

##### current_thread()
返回当前线程变量Thread的实例（用<>表示，第一个元素是线程名，第二个元素是线程标识符），使用type打印返回值类型为<class 'threading.Thread'>，如果线程不是通过threading模块创建的，则返回一个功能受限的虚拟线程。


```python
#python3
import time, threading


def loop():
    print('线程 %s 正在运行...' % threading.current_thread().name)
    print('线程 %s 正在运行...' % threading.current_thread().ident)
    print('线程 %s 正在运行...' % threading.current_thread())
    n = 0
    while n<5:
        n = n+1
        print('线程 %s >>> %s' % (threading.current_thread().name, n))
        time.sleep(1)
        print('线程 %s 结束运行.' % threading.current_thread().name)
print('线程 %s 正在运行...' % threading.current_thread().name)
t1 = threading.Thread(target=loop) 
t2 = threading.Thread(target=loop) 
t1.start() 
t2.start() 
t1.join()
t2.join()
print('线程 %s 结束运行.' % threading.current_thread().name)
```

    线程 MainThread 正在运行...
    线程 Thread-20 正在运行...
    线程 6436 正在运行...线程 Thread-21 正在运行...
    
    线程 <Thread(Thread-20, started 6436)> 正在运行...线程 1192 正在运行...
    
    线程 Thread-20 >>> 1线程 <Thread(Thread-21, started 1192)> 正在运行...
    
    线程 Thread-21 >>> 1
    线程 Thread-20 结束运行.
    线程 Thread-20 >>> 2
    线程 Thread-21 结束运行.
    线程 Thread-21 >>> 2
    线程 Thread-20 结束运行.
    线程 Thread-20 >>> 3
    线程 Thread-21 结束运行.
    线程 Thread-21 >>> 3
    线程 Thread-20 结束运行.
    线程 Thread-20 >>> 4
    线程 Thread-21 结束运行.
    线程 Thread-21 >>> 4
    线程 Thread-20 结束运行.
    线程 Thread-20 >>> 5
    线程 Thread-21 结束运行.
    线程 Thread-21 >>> 5
    线程 Thread-20 结束运行.
    线程 Thread-21 结束运行.
    线程 MainThread 结束运行.
    

##### get_ident() 
返回当前线程的线程标识符，是一个非零整数，返回的值是threading.current_thread()的返回值的后半部分。当该线程结束并创建另一个线程时，线程标识符会回收。


```python
#python3
import time, threading


def loop():
    print('线程 %s 正在运行...' % threading.current_thread().name)
    print('线程 %s 正在运行...' % threading.current_thread().ident)
    print('线程 %s 正在运行...' % threading.get_ident())
    print('线程 %s 正在运行...' % threading.current_thread())
    n = 0
    while n<5:
        n = n+1
        print('线程 %s >>> %s' % (threading.current_thread().name, n))
        time.sleep(1)
        print('线程 %s 结束运行.' % threading.current_thread().name)
print('线程 %s 正在运行...' % threading.current_thread().name)
t1 = threading.Thread(target=loop) 
t2 = threading.Thread(target=loop) 
t1.start() 
t2.start() 
t1.join()
t2.join()
print('线程 %s 结束运行.' % threading.current_thread().name)
```

    线程 MainThread 正在运行...
    线程 Thread-16 正在运行...
    线程 8860 正在运行...线程 Thread-17 正在运行...
    
    线程 8860 正在运行...线程 6720 正在运行...
    
    线程 <Thread(Thread-16, started 8860)> 正在运行...线程 6720 正在运行...
    
    线程 Thread-16 >>> 1线程 <Thread(Thread-17, started 6720)> 正在运行...
    
    线程 Thread-17 >>> 1
    线程 Thread-16 结束运行.
    线程 Thread-16 >>> 2
    线程 Thread-17 结束运行.
    线程 Thread-17 >>> 2
    线程 Thread-16 结束运行.
    线程 Thread-16 >>> 3
    线程 Thread-17 结束运行.
    线程 Thread-17 >>> 3
    线程 Thread-16 结束运行.
    线程 Thread-16 >>> 4
    线程 Thread-17 结束运行.
    线程 Thread-17 >>> 4
    线程 Thread-16 结束运行.
    线程 Thread-16 >>> 5
    线程 Thread-17 结束运行.
    线程 Thread-17 >>> 5
    线程 Thread-16 结束运行.
    线程 Thread-17 结束运行.
    线程 MainThread 结束运行.
    

##### threading.enumerate() 
返回一个list。这个list是当前正在运行的所有Thread实例, 包括守护线程, 使用threading.current_thread()创建的虚拟线程，主线程。如果线程已经终止或尚未启动则不在该list中显示。



```python
#python3
import time, threading


def loop():
    print('线程 %s 正在运行...' % threading.current_thread().name)
    print('当前运行的线程有 %s' % threading.enumerate())

    n = 0
    while n<5:
        n = n+1
        print('线程 %s >>> %s' % (threading.current_thread().name, n))
        time.sleep(1)
        print('线程 %s 结束运行.' % threading.current_thread().name)
print('线程 %s 正在运行...' % threading.current_thread().name)
t1 = threading.Thread(target=loop) 
t2 = threading.Thread(target=loop) 
t1.start() 
t2.start() 
t1.join()
t2.join()
print('线程 %s 结束运行.' % threading.current_thread().name)
```

    线程 MainThread 正在运行...线程 Thread-18 正在运行...
    线程 Thread-19 正在运行...
    当前运行的线程有 [<_MainThread(MainThread, started 10016)>, <Thread(Thread-4, started daemon 10072)>, <Heartbeat(Thread-5, started daemon 10080)>, <HistorySavingThread(IPythonHistorySavingThread, started 9212)>, <ParentPollerWindows(Thread-3, started daemon 1596)>, <Thread(Thread-18, started 4228)>, <Thread(Thread-19, initial)>]
    当前运行的线程有 [<_MainThread(MainThread, started 10016)>, <Thread(Thread-4, started daemon 10072)>, <Heartbeat(Thread-5, started daemon 10080)>, <HistorySavingThread(IPythonHistorySavingThread, started 9212)>, <ParentPollerWindows(Thread-3, started daemon 1596)>, <Thread(Thread-18, started 4228)>, <Thread(Thread-19, started 4684)>]
    
    线程 Thread-18 >>> 1线程 Thread-19 >>> 1
    
    线程 Thread-18 结束运行.
    线程 Thread-18 >>> 2
    线程 Thread-19 结束运行.
    线程 Thread-19 >>> 2
    线程 Thread-18 结束运行.
    线程 Thread-18 >>> 3
    线程 Thread-19 结束运行.
    线程 Thread-19 >>> 3
    线程 Thread-18 结束运行.
    线程 Thread-18 >>> 4
    线程 Thread-19 结束运行.
    线程 Thread-19 >>> 4
    线程 Thread-18 结束运行.
    线程 Thread-18 >>> 5
    线程 Thread-19 结束运行.
    线程 Thread-19 >>> 5
    线程 Thread-18 结束运行.
    线程 Thread-19 结束运行.
    线程 MainThread 结束运行.
    

##### threading.main_thread()
返回主线程对象的实例


```python
#python3
import time, threading


def loop():
    print('当前主线程为: %s' %threading.main_thread())
    print('线程 %s 正在运行...' % threading.current_thread().name)


    n = 0
    while n<5:
        n = n+1
        print('线程 %s >>> %s' % (threading.current_thread().name, n))
        time.sleep(1)
        print('线程 %s 结束运行.' % threading.current_thread().name)
print('线程 %s 正在运行...' % threading.current_thread().name)
t1 = threading.Thread(target=loop) 
t2 = threading.Thread(target=loop) 
t1.start() 
t2.start() 
t1.join()
t2.join()
print('线程 %s 结束运行.' % threading.current_thread().name)
```

还有一个其他的方法

##### threading.settrace(func)
对所有从threading模块启动的线程设置跟踪函数。在run()方法被调用前，对每个线程设置跟踪函数(func)将会传递给sys.settrace()。
##### threading.setprofile(func)
对所有从threading模块启动的线程设置简介函数（profile function）。在run()方法被调用前，对每个县城设置跟踪函数(func)将会传递给set.setprofile()。
##### threading.stack_size([size])
返回创建新线程时使用的线程堆栈大小。其中参数size必须为0或者至少为32768（32kiB）的正整数值，如果未指定size参数，则缺省值为0，如果不支持修改线程堆栈的大小，则会报 RuntimeError。如果制定的堆栈大小无效，则会报ValueError。
##### threading.TIMEOUT_MAX
允许用于阻塞函数（Lock.acquire()，RLock.acquire()，Condition.wait() 等）的 timeout 参数的最大值。超过此值将产生 OverflowError。

| 属性 |描述 |
|----|---|
| # Thread对象数据属性|
| name | 线程名 |
|ident|线程的标识符|
|daemon|布尔标志，表示这个线程是否是守护线程|
    |# Thread方法|
|_init_(group=None, tatget=None, name=None, args-(),kwargs={}, verbose=None,daemon=None)|实例化一个线程对象，需要有一个可调用的target，以及其参数args或kwargs。还可以传递name或group参数，不过后者还未实现。此外，verbose标志也是可以接受的，而daemon的是将会设定thread.daemon属性/标志。|
|start()|开始执行该线程|
|run()|定义线程功能的方法（通常在子类中被重写）|
|join(timeout=None)|直至启动的线程终止之前一直挂起；除非给出了timeout（秒），否则会一直阻塞|
|getName()（已更换为thread.name）|返回线程名|
|setName()|设定线程名|
|isAlivel/is_alive()|布尔标志，表示这个线程是否还存活|
|isDaemon()（已更换为thread.daemon）|如果是守护线程，则返回True，否则返回False|
|setDaemon(daemonic)|把线程的守护标志设为布尔值daemonic(必须在线程start()之前调用)|

---

### Lock

线程之所以比进程轻量，其中一个原因就是他们共享内存。也就是各个线程可以平等的访问内存的数据，如果在短时间“同时并行”读取修改内存的数据，很可能造成数据不同步。因此引入锁，对临界资源进行互斥访问。

Lock对象的创建方式为：
```python
threading.Lock()
```

锁有两种状态：“locked”和“unlocked”

- 如果一个锁的状态是unlocked，调用acquire()方法改变它的状态为locked；
- 如果一个锁的状态是locked，acquire()方法将会阻塞，直到另一个线程调用release()方法释放了锁；
- 如果一个锁的状态是unlocked调用release()会抛出RuntimeError异常；
- 如果一个锁的状态是locked，调用release()方法改变它的状态为unlocked

##### Lock.acquire(blocking=True, timeout=-1):
加锁语句，分为阻塞和非阻塞两种形式。
- 如果创建锁使用blocking参数为True（默认值）的语句时，要等到所得状态变为unlocked，然后在设置为locked状态，并返回True
- 如果创建锁使用blocking为False的语句时，如果如果当前锁的状态是locked，这时有一个参数blocking为True的语句想要对当前内容加锁，则不会等待到unlocked，而是直接返回False。如果当前锁的状态是unlocked，则会加锁并返回True。
- 参数Timeout默认值为-1，表示无限等待，如果为正浮点数，则表示等待的时间。
- 返回值为True表示加锁成功，返回值为False表示加锁失败。

##### Lock.release()
释放锁
- 当为locked状态时，可以将其重置为unlocked状态
- 当为unlocked状态时，使用该语句会报RuntimeError错误。（RuntimeError: release unlocked lock）

下面的是使用了Lock的


```python
import time, threading

# 假定这是你的银行存款:
balance = 0
lock = threading.Lock()
con = threading.Condition()

def change_it(n):
    # 先存后取，结果应该为0:
    global balance
#     time.sleep(1)

    balance = balance + n
    print("%s start save %d money and balance is %d" % (threading.current_thread().name, n, balance))

    balance = balance - n
    print("%s start get %d money and balance is %d" % (threading.current_thread().name, n, balance))
def run_thread(n):
    for i in range(10):
        # print("No."+str(i))
        lock.acquire()
        try:
            change_it(n)
        finally:
            lock.release()

t1 = threading.Thread(target=run_thread, args=(5,))
t2 = threading.Thread(target=run_thread, args=(8,))
t1.start()
t2.start()
t1.join()
t2.join()
# time.sleep(2)
print(balance)
```

    Thread-22 start save 5 money and balance is 5
    Thread-22 start get 5 money and balance is 0
    Thread-22 start save 5 money and balance is 5Thread-23 start save 8 money and balance is 8
    
    0Thread-22 start get 5 money and balance is 0Thread-23 start get 8 money and balance is 0
    
    
    Thread-22 start save 5 money and balance is 5Thread-23 start save 8 money and balance is 8
    
    Thread-22 start get 5 money and balance is 0Thread-23 start get 8 money and balance is 0
    
    Thread-22 start save 5 money and balance is 5Thread-23 start save 8 money and balance is 8
    
    Thread-22 start get 5 money and balance is 0Thread-23 start get 8 money and balance is 0
    
    Thread-22 start save 5 money and balance is 5Thread-23 start save 8 money and balance is 8
    
    Thread-22 start get 5 money and balance is 0Thread-23 start get 8 money and balance is 0
    
    Thread-22 start save 5 money and balance is 5Thread-23 start save 8 money and balance is 8
    
    Thread-22 start get 5 money and balance is 0Thread-23 start get 8 money and balance is 0
    
    Thread-22 start save 5 money and balance is 5Thread-23 start save 8 money and balance is 8
    
    Thread-22 start get 5 money and balance is 0Thread-23 start get 8 money and balance is 0
    
    Thread-22 start save 5 money and balance is 5Thread-23 start save 8 money and balance is 8
    
    Thread-22 start get 5 money and balance is 0Thread-23 start get 8 money and balance is 0
    
    Thread-22 start save 5 money and balance is 5Thread-23 start save 8 money and balance is 8
    
    Thread-22 start get 5 money and balance is 0Thread-23 start get 8 money and balance is 0
    
    Thread-22 start save 5 money and balance is 5Thread-23 start save 8 money and balance is 8
    
    Thread-22 start get 5 money and balance is 0Thread-23 start get 8 money and balance is 0
    
    Thread-23 start save 8 money and balance is 8
    Thread-23 start get 8 money and balance is 0
    

下面是没有使用Lock的


```python
import time, threading

# 假定这是你的银行存款:
balance = 0
lock = threading.Lock()
con = threading.Condition()

def change_it(n):
    # 先存后取，结果应该为0:
    global balance
#     time.sleep(1)

    balance = balance + n
    print("%s start save %d money and balance is %d" % (threading.current_thread().name, n, balance))

    balance = balance - n
    print("%s start get %d money and balance is %d" % (threading.current_thread().name, n, balance))

def run_thread(n):
    for i in range(10):
        change_it(n)

t1 = threading.Thread(target=run_thread, args=(5,))
t2 = threading.Thread(target=run_thread, args=(8,))
t1.start()
t2.start()
t1.join()
t2.join()
# time.sleep(2)
print(balance)
```

可以看出没有使用Lock锁的，中间出现了错误“13”, 如果没有join()方法，会使主线程中的print余额先结束执行，得到错误的结果


```python
import time, threading

# 假定这是你的银行存款:
balance = 0
lock = threading.Lock()
con = threading.Condition()

def change_it(n):
    # 先存后取，结果应该为0:
    global balance
#     time.sleep(1)

    balance = balance + n
    print("%s start save %d money and balance is %d" % (threading.current_thread().name, n, balance))

    balance = balance - n
    print("%s start get %d money and balance is %d" % (threading.current_thread().name, n, balance))

def run_thread(n):
    for i in range(10):
        change_it(n)

t1 = threading.Thread(target=run_thread, args=(5,))
t2 = threading.Thread(target=run_thread, args=(8,))
t1.start()
t2.start()
# t1.join()
# t2.join()
# time.sleep(2)
print(balance)
```


```python
import threading
import time

data = 0
lock = threading.Lock()

def func():
    global data
    print('%s acquire lock...' % threading.currentThread().getName())

    # 调用acquire([timeout])时，线程将一直阻塞，
    # 直到获得锁定或者直到timeout秒后（timeout参数可选）。
    # 返回是否获得锁。
    if lock.acquire():
        print('%s get the lock.' % threading.currentThread().getName())
        data += 1
        # time.sleep(2)
        print('%s release lock...' % threading.currentThread().getName())

        # 调用release()将释放锁。
        lock.release()

t1 = threading.Thread(target=func)
t2 = threading.Thread(target=func)
t3 = threading.Thread(target=func)
t1.start()
t2.start()
t3.start()
# t1.join()
# t2.join()
# t3.join()
print(data)
```

    Thread-17 acquire lock...
    Thread-18 acquire lock...Thread-19 acquire lock...1Thread-17 get the lock.
    
    
    
    Thread-17 release lock...
    Thread-18 get the lock.
    Thread-18 release lock...
    Thread-19 get the lock.
    Thread-19 release lock...
    

# RLock

RLock为可重入锁，创建方式为：
```python
threading.RLock()
```

RLock的方法同Lock的方法相同

##### RLock.acquire(blocking=True, timeout=-1):
加锁语句，分为阻塞和非阻塞两种形式。
- 如果创建锁使用blocking参数为True（默认值）的语句时，要等到所得状态变为unlocked，然后在设置为locked状态，并返回True
- 如果创建锁使用blocking为False的语句时，如果如果当前锁的状态是locked，这时有一个参数blocking为True的语句想要对当前内容加锁，则不会等待到unlocked，而是直接返回False。如果当前锁的状态是unlocked，则会加锁并返回True。
- 参数Timeout默认值为-1，表示无限等待，如果为正浮点数，则表示等待的时间。
- 返回值为True表示加锁成功，返回值为False表示加锁失败。

##### RLock.release()
释放锁
- 当为locked状态时，可以将其重置为unlocked状态
- 当为unlocked状态时，使用该语句会报RuntimeError错误。（RuntimeError: release unlocked lock）

## Lock与RLock对比

```python
# Lock
import threading
lock = threading.Lock()   # Lock对象
lock.acquire()   # 正常
lock.acquire()   # 产生了死琐,程序一直阻塞在这里。
lock.release()
lock.release()
```

```python
# RLock
import threading
rLock = threading.RLock()   # RLock对象
rLock.acquire()   # 正常
rLock.acquire()   # 可重入锁对象,在同一线程内，程序不会堵塞。
rLock.release()   # 释放
rLock.release()   # 释放
```

### 区别
- RLock为可重入锁对象，使得单线程可以再次获得已经获得了的锁（递归锁定）,RLock允许在同一线程中被多次acquire。而Lock却不允许这种情况。
- 注意：如果使用RLock，那么acquire和release必须成对出现，即调用了n次acquire，必须调用n次的release才能真正释放所占用的琐。

# 条件对象（Condition Objects）

python提供了Condition对象。除了具有acquire和release方法外，还提供wait和notify方法。线程首先acquire一个条件变量锁。如果条件不足，则该线程wait，如果满足就执行线程，甚至可以notify其他线程。其他处于wait状态的线程接到通知后会重新判断条件。

创建方法：
```python
threading.Condition(lock=None)
```
- 创建条件变量对象，条件变量允许一个或多个线程等待，直到它们被另一个线程通知
- 参数lock的值有“None”，“Lock对象”，“RLock对象”

#####   Condition.acquire(*args)
获取底层锁。

#####   Condition.release()
释放底层锁。

##### wait(timeout=None)
调用这个方法将使线程进入Condition的等待池等待通知notify()或notify_all()，并释放锁。使用前线程必须已获得锁定，否则将抛出异常RuntimeError。
- 当底层锁是RLock时，wait()方法不是使用release()方法释放，而是使用RLock对象内部的接口释放锁。因为是RLock锁，可能被递归获取了很多次，使用release()不能保证可以释放锁。当锁被重新获得时，使用另一个内部接口恢复递归级别。

##### wait_for(predicate, timeout=None)
predicate为等待条件，为一个布尔值，当为True时，等待结束，也可一通过timeout设置最大等待时间。

##### notify(n=1)
调用这个方法将从等待池挑选一个线程并通知，收到通知的线程将自动调用acquire()尝试获得锁定（进入锁定池）；其他线程仍然在等待池中。
- 其中n为被唤醒的数量

##### notifyAll()
调用这个方法将通知等待池中所有的线程，这些线程都将进入锁定池尝试获得锁定。

---


```python
#python3
import threading
import time
 
# 商品
product = None
# 条件变量
con = threading.Condition()
 
# 生产者方法
def produce():
    global product
    
    if con.acquire():
        while True:
            if product is None:
                print( '生产者...')
                product = 'anything'
                
                # 通知消费者，商品已经生产
                con.notify()
            
            # 等待通知
            con.wait()
#             time.sleep(1) 
# 消费者方法
def consume():
    global product
    
    if con.acquire():
        while True:
            if product is not None:
                print('消费者...' )
                product = None
                
                # 通知生产者，商品已经没了
                con.notify()
            
            # 等待通知
            con.wait()
#             time.sleep(1) 
t1 = threading.Thread(target=produce)
t2 = threading.Thread(target=consume)
t2.start()
t1.start()

```


```python
import threading
import random
import time
con = threading.Condition()
list1 = list()
MAX_LEN = 10

class Producer(threading.Thread):

    def run(self):

        global list1
        while True:
            con.acquire()
            if len(list1)>=MAX_LEN:
                print('list1满了，生产者等待')
                con.wait()
                # print("唤醒消费者")
            elem = random.randrange(100)
            list1.append(elem)
            print("生产者生产了一个 {}, 现在list1的长度为 {}".format(elem, len(list1)))
            con.notify()
            con.release()
            time.sleep(random.random())
class Consumer(threading.Thread):
    def run(self):
        global list1

        while True:
            con.acquire()
            if not list1:
                print("现在list1为空，消费者等待")
                con.wait()
                # print("唤醒生产者")
            elem = list1.pop(0)
            print("消费者消费一个 {}. 现在list1的长度为 {}".format(elem, len(list1)))
            con.notify()
            con.release()
            time.sleep(random.random())

def main():

    Producer().start()
    Consumer().start()
main()
```

---

# 信号量对象(Semaphore Objects)

Semaphore管理一个内置的计数器，每当调用acquire()时-1，调用release() 时+1。计数器不能小于0；当计数器为0时，acquire()将阻塞，直到其他线程调用release()。

创建方法：
##### threading.Semaphore(value=1)
- 参数value的值为大于0的整数，默认值为1，如果value值小于0，会报ValueError错误

##### Semaphore.acquire(blocking=True, timeout=None)
获取信号量。
- 如果没有参数
    - 如果内部计数器大于0，则将其-1，并立即返回True
    - 如果内部计数器等于0，则被阻塞，直到release()使计数器大于0，这是将计数器-1，并返回True
- 如果blocking的参数值为false，当内部计数器为0时，不会阻塞，立即返回false。
- timeout为阻塞的时间，默认值为None。如果设置timeout为一个正数时（单位为秒），当在该时间内一直被阻塞则返回false，否则返回true

##### Semaphore.release()
释放信号量，使内部计数器+1


```python
#python3
import threading
import time
 
# 计数器初值为2
semaphore = threading.Semaphore(2)
 
def func():
    
    # 请求Semaphore，成功后计数器-1；计数器为0时阻塞
    print ('%s acquire semaphore...' % threading.currentThread().name)
    if semaphore.acquire():
        
        print ('%s get semaphore' % threading.currentThread().name)
        time.sleep(4)
        
        # 释放Semaphore，计数器+1
        print ('%s release semaphore' % threading.currentThread().name)
        semaphore.release() 
t1 = threading.Thread(target=func)
t2 = threading.Thread(target=func)
t3 = threading.Thread(target=func)
t4 = threading.Thread(target=func)
t1.start()
t2.start()
t3.start()
t4.start()
 
# time.sleep(2)
 
# 没有获得semaphore的主线程也可以调用release
print ('MainThread release semaphore without acquire')
semaphore.release()
```

---

# 计时器对象（Timer）

表示应该在一定时间之后才执行。Timer是Thread的子类。

创建方法：
threading.Timer(interval, function, args=None, kwargs=None)
- interval为指定的时间
- function为要执行的方法
- args和kwargs为方法的参数

##### Timer.start()
同Thread相同，启动计时器

##### Timer.cancel()
停止计时器，只有在计时器处于等待阶段时才有用


```python
from threading import Timer
def hello():
    print("hello, world")

t = Timer(5.0, hello)
t.start()  # after 5 seconds, "hello, world" will be printed
```

    hello, world
    


```python
import threading
 
def func():
    print ('hello timer!') 
timer = threading.Timer(5, func)
timer.start()

```

    hello timer!
    

### 举例

#### 创建Thread实例，传给他一个可调用的类实例


```python
import threading
from time import sleep,ctime
loops = [4,2]
def loop(nloop, nsec):
    print("开始loop", nloop,"在:",ctime())
    sleep(nsec)
    print("loop",nloop,"完成在",ctime())
def main():
    print("开始时间",ctime())
    threads = []
    nloops = range(len(loops))
    print(nloops)
    for i in nloops:
        t = threading.Thread(target=loop,args=(i, loops[i]))
        threads.append(t)

    for i in nloops:
        threads[i].start()

    for i in nloops:
        threads[i].join()

    print("全部做完",ctime())

if __name__== "__main__":
    main()
```

    开始时间 Sun Jan  7 00:25:55 2018
    range(0, 2)
    开始loop 开始loop0  1在:  在:Sun Jan  7 00:25:55 2018 
    Sun Jan  7 00:25:55 2018
    loop 1 完成在 Sun Jan  7 00:25:57 2018
    loop全部做完  0 完成在 Sun Jan  7 00:25:59 2018
    Sun Jan  7 00:25:59 2018
    

先分配Thread，实例化每个Thread对象，获得Thread实例，当所有线程都分配完成后，通过调用每个线程的start()方法让他们开始执行，而不是在此之前就执行。
对于join()方法而言，其另一个重要方面是它不需要调用。一旦线程启动，他们就会一直执行，知道给定的函数完成后退出。如果主线程还有其他事情要去做，而不是等待这些线程完成（例如其他处理或者等待新的客户端请求），就可以不调用join()方法。join()方法只有在需要等待线程完成的时候才是有用的。

#### 创建Thread的实例，传给它一个可调用的类实例


```python
import threading
from time import sleep,ctime
loops = [4,2]
class ThreadFunc(object):
    def __init__(self, func, args, name=""):
        self.name = name
        self.func = func
        self.args = args
        
    def __call__(self):
        self.func(*self.args)
def loop(nloop, nsec):
    print("开始loop", nloop,"在:",ctime())
    sleep(nsec)
    print("loop",nloop,"完成在",ctime())
def main():
    print("开始时间",ctime())
    threads = []
    nloops = range(len(loops))

    for i in nloops:
        t = threading.Thread(target=ThreadFunc(loop,(i, loops[i]),loop.__name__))
        threads.append(t)

    for i in nloops:
        threads[i].start()

    for i in nloops:
        threads[i].join()

    print("全部做完",ctime())

if __name__== "__main__":
    main()
```

    开始时间 Sun Jan  7 00:27:49 2018
    开始loop 开始loop 10  在:在:  Sun Jan  7 00:27:49 2018Sun Jan  7 00:27:49 2018
    
    loop 1 完成在 Sun Jan  7 00:27:51 2018
    loop 0 全部做完完成在 Sun Jan  7 00:27:53 2018
     Sun Jan  7 00:27:53 2018
    

#### 派生Thread的子类，并创建子类的实例


```python
import threading
from time import sleep,ctime
loops = [4,2]
class MyThread(threading.Thread):
    def __init__(self, func, args, name=""):
        threading.Thread.__init__(self)
        self.name = name
        self.func = func
        self.args = args
        
    def run(self):
        self.func(*self.args)
def loop(nloop, nsec):
    print("开始loop", nloop,"在:",ctime())
    sleep(nsec)
    print("loop",nloop,"完成在",ctime())
def main():
    print("开始时间",ctime())
    threads = []
    nloops = range(len(loops))

    for i in nloops:
        t = MyThread(loop,(i, loops[i]),loop.__name__)
        threads.append(t)

    for i in nloops:
        threads[i].start()

    for i in nloops:
        threads[i].join()

    print("全部做完",ctime())

if __name__== "__main__":
    main()
```

MyThread子类的构造函数必须先调用其基类Thread的构造函数：threading.Thread.__init__(self)
这种派生子类创建Thread实例的方法要是用run()来定义要实现的功能

# 生产者消费者模型


```python
import queue
import random
queue = queue.Queue(10)

class Producer(threading.Thread):

    def run(self):
        while True:
            elem = random.randrange(100)
            queue.put(elem)
            print("Producer a elem {}, Now size is {}".format(elem, queue.qsize()))
            time.sleep(random.random())

class Consumer(threading.Thread):

    def run(self):
        while True:
            elem = queue.get()
            queue.task_done()
            print("Consumer a elem {}. Now size is {}".format(elem, queue.qsize()))
            time.sleep(random.random())

def main():

    for i in range(3):
        Producer().start()

    for i in range(2):
        Consumer().start()
main()
```

queue内部实现了相关的锁，如果queue的为空，则get元素的时候会被阻塞，直到队列里面被其他线程写入数据。同理，当写入数据的时候，如果元素个数大于队列的长度，也会被阻塞。也就是在 put 或 get的时候都会获得Lock。

---

## 全局解释器锁
- python代码的执行是由python虚拟机进行控制的。python在设计时是这样考虑的，在主循环中同时只能有一个控制线程在执行，就像单核CPU系统中的多进程一样。内存中可以有许多程序，但是在任意给定的时刻，只能有一个程序在运行。同理，尽管python解释器中可以运行多个线程，但是在任意给定时刻只有一个线程会被解释器执行。
- python中的线程虽然是真正的线程，但解释器执行代码时，有一个GIL锁。任何Python线程执行前，必须先获得GIL锁，然后，每执行100条字节码，解释起就自动释放GIL锁。让别的线程有机会执行。这个GIL锁实际上把所有的线程的执行代码都给上了锁，所以，多线程在Python中只能交替执行。
- 虽然Python不能利用所线程实现多核任务，但可以用多进程实现多核任务。多个python进程有各自独立的GIL锁，互不影响。
- 对python虚拟机的访问是由全局解释器锁（GIL）控制的。这个锁就是用来保证同时只能有一个线程运行的。在多线程的环境中，python虚拟机将按照下面所述的方式执行。
    - 1、设置GIL
    - 2、切换仅一个线程去运行
    - 3、执行下面的操作之一
          - 指定数量的字节码指令
          - 线程主动让出控制权（可以调用time.sleep(0)来完成）
    - 4、把线程设置回睡眠状态
    - 5、解锁GIL
    - 6、重复上述步骤

# 参考
- <python核心编程》
- Python多线程（threading）学习总结 - CSDN博客
- 17.1. threading &#8212;基于线程的并行性 - Python文档 - RD文档
- 多线程 - 廖雪峰的官方网站
- Python 多线程 | 菜鸟教程
- POSIX线程 - 维基百科，自由的百科全书
- Python 多线程 - 简书
- https://rookiefly.cn/detail/98
- https://rookiefly.cn/detail/97
- Linux 守护进程的启动方法 - 阮一峰的网络日志
- Python threading - 简书
- Python 多线程和多进程编程总结 - tracholar's personal knowledge wiki
- python多线程编程(4) threading Lock and RLock · BeginMan
- Python线程同步机制: Locks, RLocks, Semaphores, Conditions, Events和Queues - Zhou's Blog
- Python线程指南 - AstralWind - 博客园
- python：threading多线程模块-Condition实现复杂的同步 -师父领进门，修行靠个人！-51CTO博客
