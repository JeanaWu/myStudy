# （一）自动内存管理机制

## 1 Java内存区域&内存溢出异常

### 1.1 运行时数据区域

![image-20200130210726544](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200130210726544.png)

#### 1.1.1 程序计数器

当前线程所执行的字节码行号的指示器

每个线程都有独立的计数器，独立存储，互不影响——线程私有的内存

如果线程正在执行的是一个Java方法，这个计数器记录的是正在执行的虚拟机字节码指 令的地址；如果正在执行的是Native方法，这个计数器值则为空（Undefined）。此内存区域 是唯一一个在Java虚拟机规范中没有规定任何OutOfMemoryError情况的区域。

#### 1.1.2  java虚拟机栈

线程私有，生命周期与线程相同。

每个方法执行时候都会创造stack frame，存储LVA，operand stack 和 varibles

LVA所需要的内存空间在编译期间完成分配，当一个方法进入的时候，在帧栈分配多大的空间是确定的，方法运行期间不会改变变量表的大小

#### 1.1.3 本地方法栈

类似虚拟机栈，虚拟机栈服务Java方法，native method stack服务虚拟机使用到的native方法

#### 1.1.4 java堆

线程共享的内存区域，在虚拟机启动时创建

存放对象实例

java堆可以处于物理上不连续的内存空间，逻辑连续就好

如 果在堆中没有内存完成实例分配，并且堆也无法再扩展时，将会抛出OutOfMemoryError异 常。

#### 1.1.5 方法区

线程共享的内存区域

non-heap， permanant generation

存储已被虚拟机加载的类信息，常量，静态变量和JIT编译后的代码等数据

#### 1.1.6 运行时常量池

方法区一部分

存放编译期生成的各种字面量和符号引用

当常量池无法再申 请到内存时会抛出OutOfMemoryError异常。

#### 1.1.7 直接内存

### 1.2虚拟机对象

#### 1.2.1对象的创建

虚拟机遇到一条new指令时，首先将去检查这个指令的参数是否能在常量池中定位到一 个类的符号引用，并且检查这个符号引用代表的类是否已被加载、解析和初始化过。如果没 有，那必须先执行相应的类加载过程

Bump the pointer 内存规整

free list 内存不规整

对象的object header存储对象的必要设置：例如这个对象是哪个类的实例、如何才能找 到类的元数据信息、对象的哈希码、对象的GC分代年龄等信息。

#### 1.2.2对象的内存布局

hotspot虚拟机对象头：

Part1: 存储对象自身的运行时数据， 如哈希码（HashCode）、GC分代年龄、锁状态标志、线程持有的锁、偏向线程ID、偏向时 间戳等

Part2: 类型指针，即对象指向它的类元数据的指针，虚拟机通过这个指 针来确定这个对象是哪个类的实例

Part3: 对齐填充

#### 1.2.3 对象的访问定位

句柄![image-20200131120325739](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200131120325739.png)

直接指针访问

![image-20200131120430319](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200131120430319.png)

### 1.3 outofmemory 异常

#### 1.3.1 java堆溢出

#### 1.3.2 虚拟机和本地方法栈溢出

如果线程请求的栈深度大于虚拟机所允许的最大深度，将抛出StackOverflowError异常。

如果虚拟机在扩展栈时无法申请到足够的内存空间，则抛出OutOfMemoryError异常。

在单个线程下，无论是由于栈帧太大还是虚拟机栈容量太小，当内存无 法分配的时候，虚拟机抛出的都是StackOverflowError异常。

#### 1.3.3 方法区和运行时常量池溢出

#### 1.3.4 本机直接内存溢出



## 2 垃圾桶收集与内存分配策略

### 2.1 引用计数算法

### 2.2 可达性分析算法

![image-20200131151516500](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200131151516500.png)

位置：

虚拟机栈thread stack中引用的对象

方法区的中静态属性引用的对象

方法区中常量引用的对象

本地方法栈中JNI引用的对象

### 2.3 引用

强引用

软引用

若引用

虚引用

### 2.4 对象死亡

宣告对象死亡，需要两次标记

可达性分析后，发现没有一gc root相连接，标记一次

筛选对象是否要执行finalize（）

finalize（）方法是对象逃脱死亡命运的最后一次机会，稍后GC将对F-Queue中的对象 进行第二次小规模的标记，如果对象要在finalize（）中成功拯救自己——只要重新与引用链 上的任何一个对象建立关联即可，譬如把自己（this关键字）赋值给某个类变量或者对象的 成员变量，那在第二次标记时它将被移除出“即将回收”的集合；如果对象这时候还没有逃 脱，那基本上它就真的被回收了。

![image-20200203203344550](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200203203344550.png)

任何一个对象的finalize（）方法都只会被系统自动调用一次， 如果对象面临下一次回收，它的finalize（）方法不会被再次执行，因此第二段代码的自救行 动失败了。

### 2.5回收方法区

java不要求在方法区实现垃圾收集

永久带的垃圾回收：废弃常量，无用的类

​	无用的类：该类的所有实例都被回收，java堆中不存在任何势力，加载此类的class loader已经被回收，该类对应的java.lang.class对象没有在任何地方被一弄，无法在任何地方通过反射访问该类的方法

### 2.6 垃圾收集算法

#### 标记-清除算法

标记完成后统一回收

![image-20200203205244843](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200203205244843.png)

#### 复制算法

内存按容量分成两块，每次只使用一块，用完了以后讲还存活的对象复制到另一块上面，再清楚已使用的内存空间

![image-20200203205449024](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200203205449024.png)

#### 标记-整理算法

![image-20200203205945208](/Users/wu.juan/Library/Application Support/typora-user-images/image-20200203205945208.png)

标记过程 仍然与“标记-清除”算法一样，但后续步骤不是直接对可回收对象进行清理，而是让所有存 活的对象都向一端移动，然后直接清理掉端边界以外的内存

#### 分代收集算法

在新生代 中，每次垃圾收集时都发现有大批对象死去，只有少量存活，那就选用复制算法，只需要付 出少量存活对象的复制成本就可以完成收集。而老年代中因为对象存活率高、没有额外空间 对它进行分配担保，就必须使用“标记—清理”或者“标记—整理”算法来进行回收。

### 2.7 hotspot算法实现

#### 枚举根节点

需要停顿，保持一致性

通过oop得知哪些地方存放对象引用

#### 安全点

达到安全点，暂停，gc

到最近的安全点停下：抢先式中断，主动式中断

#### 安全区域

安全区域是指在一段代码片段之中，引用关系不会发生变化。在这个区域中的任意地方 开始GC都是安全的。我们也可以把Safe Region看做是被扩展了的Safepoint。

### 2.8 垃圾收集器

