### 生命周期：

在程序开发中，将一个对象从被实例化完成，到这个对象使用结束并销毁的过程

#### 线程的生命周期

一个线程被实例化完成，到这个线程销毁

 

#### 线程的状态：

##### 1.新生态： new

一个线程对象被实例化完成，但是还没有任何操作

##### 2.就绪态： ready

一个线程已经被开启，已经开始去争抢cpu时间片

##### 3.运行态：run

一个线程已经抢到了cpu时间片，开始执行这个线程中的逻辑

##### 4.阻塞态：interrupt

一个线程在运行的过程中，收到某些操作的影响，放弃了已经获取到的cpu时间片，并且不再参与cpu时间片的争抢，此时线程处于挂起状态

（等待队列，运行态调用wait方法，此时也是一种阻塞状态）

##### 5.死亡态：dead

一个线程对象需要被销毁



#### 状态转换图：

![image-20200102102601658](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200102102601658.png)



![image-20200106155927110](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200106155927110.png)

#### 线程的开辟方式

##### 1.继承thread类，做一个线程子类

可读性更强，但是java是单继承，会对原有的继承结构有影响

![image-20200102104027681](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200102104027681.png)

##### 2.通过runnable接口

![image-20200102104703363](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200102104703363.png)



### 线程的常用方法

#####  1.线程的命名

1.实例化一个线程对象，利用setname方法命名

2.实例化一个线程对象的同时，通过构造方法给线程命名

3.使用自定义的线程类，在实例化线程对象的同时，进行名称的赋值

需要给线程类添加对应的构造方法

##### 2.线程的休眠

1.参数： 毫为单位



##### 3.线程的优先级 

1.利用setPriority，设置线程的优先级

2.设置线程的优先级，只是去修改这个线程可以去抢到cpu时间片的概率

3.<u>并不是说优先级高的线程一定可以抢到时间片</u>

4.优先级的设置，是一个整数[0,10]，默认是5

5.设置优先级必须要放到这个线程开始执行之前（start之前）



##### 4.线程的礼让

1.利用yeild，进行线程的礼让.(yeild是一个静态方法)

2.让当前的运行状态的线程释放自己的cpu资源，由运行状态回到就绪状态



### 线程的临界资源问题和解决-->线程锁 synchronized

 多个线程同时使用同一个资源的时候回产生和预期不同的结果（类似数据不同步等）

解决：不让多个线程同时访问同一个资源---引入***线程锁***

##### 1.同步代码段

（括号内可以写任意东西）需要保证多个线程看到的锁，是同一把锁

`//对象锁`

`synchronized(){`

`}`

![image-20200103101300852](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200103101300852.png)

`//类锁`

`synchronized(SynchronizedDemo.class){`

`}`

##### 2.同步方法

使用关键字synchronized修饰的方法就是同步方法，就是将上述的代码封装成为一个方法直接调用

优化：直接使用synchronized修饰方法

`同步方法为静态方法： 同步锁为 类锁 ， 当前类.class`

class锁可以对类的所有对象实例起作用

`同步方法为非静态方法： 同步锁为 this`

静态方法：同步锁就是类锁 当前类.class



![image-20200103102536161](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200103102536161.png)



##### 3.锁 ReentrantLock

![image-20200103103219522](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200103103219522.png)

⚠️：代码sys前需要加上判断                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 ![image-20200103103446071](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200103103446071.png)

#### 脏读问题

指在读取实例变量的时候，此值已经被其他线程更改过了

在使用多线程调用同一个方法的时候，为了避免数据出现交叉的情况，使用synchronized关键字来进行同步。虽然在赋值的时候进行了同步，但是在取值的时候可能出现一些意想不到的情况——脏读。

#### 同步不具有继承性

同步不可以被继承

#### 出现异常，锁自动释放

#### 死锁问题

##### 1.可能造成死锁的情况

多个线程彼此持有对方所需要的锁对象，而不释放自己的锁

![image-20200103105219409](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200103105219409.png)

 console：A线程持有了A锁等待B锁

​				   B线程池有了B锁等待A锁

此时两个线程也没有结束，两个线程都无法释放也无法执行，造成了死锁的情况



##### 2.死锁的解决

###### 1.wait

等待，是object类中的一个方法，当前的线程释放自己的锁标记，并且让出cpu资源。使得当前的线程进入等待队列中

###### 2.notify

通知，是object类中的一个方法，唤醒等待队列中的一个线程，使这个线程进入到锁池

###### 3.notifyAll

通知，是object类中的一个方法，唤醒等待队列中的所有的线程（该锁标记的），使这些线程进入到锁池

##### 4.避免死锁的常用方法

​	1避免一个线程同时获取多个锁

​	2避免一个线程在锁内同时占用多个资源，尽量保证每个锁只占用一个资源

​	3尝试使用定时锁 lock.trylock(timeout)来替代使用内部锁机制

​	4对于数据库锁，加锁和解锁必须在一个数据库连接里面，否则会出现解锁失败的情况

### violate 关键字

##### 1.主要作用场合

在多个线程中可以感知实例变量被更改了，并且可以获得最新的值的使用，也就是用多线程读取共享变量时可以获得最新值的使用

##### 2.数据读取

jvm分为私有堆栈和共有堆栈，这个两个值不同步的时候可能造成死循环，为此需要用violate来解决。

作用：当线程访问变量的时候，强制性从公共堆栈中取值

![image-20200106132334685](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200106132334685.png)

![image-20200106132354056](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200106132354056.png)

#### 

⚠️ i++ 的操作不是原子操作，非线程安全

从内存中取出i -> 计算i的值 -> 将i的值写到内存

如果在计算的时候，另一个线程也修改i的值，这个时候会出现脏数据，所以violate本身不具有原子性。

![image-20200106141408170](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200106141408170.png)

##### 3.原子类

原子类是不能分割的正太，没有其他线程可以中断或者检查正在原子操作中的变量

i++操作除了使用synchronize外，还可以用atomicInteger原子类进行实现



### synchronized 和 violate的比较

1.synchronized 修饰代码块和方法

​	violate 修饰变量

2.多线程访问violate不会发生都塞， synchronized会

3.violate保证数据可见性，但是不保证原子性

​	synchronized可以保证原子性，也可以间接保证可见性，会同步私有内存和公共内存的数据

4.violate解决的变量在多个线程之间的可见性，synchronized解决多个线程之前访问资源的同步性



### 线程间的通信

#### 等待wait/通知 notify机制

wait将线程放入等待队列

在调用wait前，线程必须获得对象的对象级别的锁--只能在同步方法或者同步块中调用。执行wait后，释放锁

notify也要在同步方法或者同步块中调用。在调用前也要获得锁

执行notify，当前线程不会马上释放对象锁，wait的线程也不能马上获得，是要等到执行notify的线程将程序执行完，推出synchronized代码块，当前线程才会释放锁



wait方法被执行后，锁被自动释放，执行完notify不释放--指的是执行完notify方法所在的同步代码块后才释放锁



#### interrupt与wait

线程wait状态的时候，调用interrupt会出现异常

#### wait（long）

带参数的wait方法是等待某一时间内是否与线程对锁进行唤醒，如果超过这个时间自动唤醒



#### 生产者消费者设计模式--wait/notify

生产者

`通过 一个生产标记，判断是否需要生产产品`

`如果需要： 生产产品，并通知消费者消费`

`如果不需要： 等待`

​	生产逻辑——通过一个生产标记，判断是否需要生产产品。如果需要生产，生产产品并通知消费者消费；如果不需要生产，等待，检测

消费者

`通过判断是否有足够的产品可以消费`

`如果可以消费： 获取产品，进行消费`

`如果不可以消费： 等待`

临界资源：产品



 ![image-20200103212031330](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200103212031330.png)

![image-20200103212406293](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200103212406293.png)

### 



假死

线程进入waiting等待状态。如果全部线程进入waiting状态，程序不再执行任何业务功能，整个项目呈停止状态

#### 通过管道进行通信--pipestream



### joint

等待线程对象销毁

使所属的线程对象x正常执行run方法中的任务，而使当前线程z进行无期限的阻塞，等待线程x销毁后再继续执行线程z后面的代码

在joint过程中，如果当前线程对象被中断，则出现异常

joint是在内部使用wait来实现的，所以具有释放锁的特点

joint（long）会释放锁，sleep（long）则会

#### threadlocal

变量在不同线程间的隔离性，也就是不同线程拥有自己的值，不同线程中的值是可以放入threadloacl类中进行保存的



### Lock

#### 使用condition实现等待/通知

condition类可以实现“选择通知”，new多个condition对象即可

condition.await() --> 使得当前执行任务的线程进入waiting状态

await类似wait

signal类似notify

signalall类似notifyall

#### 公平锁和非公平锁

公平锁：线程获取锁的顺序是按照线程加锁顺序来分配的FIFO

非公平锁：抢占机制，所及获得

### 定时器timer

Timer主要作用是设置计划任务，但是封装任务的累是TimerTask

timertask是一个抽象类，代码需要放到子类中

schedule如果执行任务时间没有被延时，下一次从上一次的开始开始计算

secheduleatfixedrate如果没有被延时，下一次参考上一次的结束时间开始计算



### 单例模式与多线程

注意多线程在懒汉模式的时候容易违背单例

通过synchronized解决，但是对于整体上锁，代码运行效率低下

#### DCL双检查锁机制

#### 静态内置类实现单例模式

#### 序列化与反序列化的单例模式实现





![image-20200130190019049](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200130190019049.png)