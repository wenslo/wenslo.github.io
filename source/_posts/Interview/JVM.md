---
title: 面试-JVM篇章

categories: 

- 面试

tags: 

- 面试
date: 2019-10-22 23:30:03
description: 面试-JVM篇章

---



JVM相关面试题

基于《深入理解JVM》这本书，如果说可能问的话，会问什么样的问题，又该如何去解答，如果连环炮的形势问的话，又该怎么去解答。如果没有足够理解的话，估计很难面对连环炮的形势。JVM实际工作中主要定位排查问题，性能优化，节省资源，但是一些理论还是要知道的，不然的话就成了高级技工了。有人说月薪3W以下的都不叫程序员，还是有点道理的，不过每个

码畜，总归是有点理想的吧。

<!-- more -->

1. #### JVM运行时数据区分为哪几个部分？分别是什么作用？

   分为程序计数器，**Java虚拟机栈**，本地方法栈，**Java堆**，方法区

   程序计数器：

   程序计数器的工作就是通过改变计数器的值来选取吓一跳需要执行的字节码指令，分支、循环、跳转、异常处理、县城回复等基础功能都依赖程序计数器来完成。Java虚拟机的多线程是**通过线程轮流切换并分配处理器执行时间的方式来实现的**，在任何一个确定的时刻，**一个处理器（一个内核）都只会执行一条线程中的指令**。因此为了线程切换后能恢复到正确的执行位置，每个线程都有一个独立的程序计数器，**互不影响，独立存储，线程私有**，而且是唯一一个没有**OutOfMemoryError**情况的区域。
   
   Java虚拟机栈：
   
   线程私有，生命周期与线程相同，描述的是Java方法执行的内存模型：**每个方法在执行的同事都会创建一个栈帧（Stack Frame）用于存储局部变量表、操作数栈、动态链接、方法出口等信息。每个方法从调用到执行完成的过程，就对应着一个栈帧在虚拟机栈中入栈出栈的过程**。局部变量表**存放了编译期可知的各种基本数据类型（boolean,byte,char,short,int,float,long,double）、对象引用（reference类型，不是对象本身，可能是一个指向对象起始地址的引用指针，也可能是指向一个代表对象的句柄或其他与此对象相关的位置）和returnAddress类型（指向一条字节码指令的地址）**。如果线程请求的栈深度大于虚拟机允许的深度，那么会StackOverflowError，如果扩展时无法申请到足够的内存，则会OutOfMemoryError。
   
   本地方法栈：
   
   为虚拟机使用的Native方法服务。
   
   Java堆：
   
   是Java虚拟机所管理的内存中最大的一块。被所有线程共享，存放对象实例，**所有的对象实例以及数组都在堆上分配**。是是垃圾收集器管理的主要区域，细分可分为**新生代和老年代**，再细分可以有：**Eden空间、From Survivor空间、To Survivor空间。线程共享的Java堆中可能划分出多个线程私有的分配缓冲区（Thread Local Allocation Buffer）**。可以用-Xmx和-Xms来控制大小。无法扩展的时候，会抛出OutOfMemotyError。
   
   方法区：
   
   各个线程共享，存储已经被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。也叫做永久代。
   
   运行时常量池：
   
   是方法取得一部分，Class文件中除了有类的版本、字段、方法、接口等描述信息外，还有一项信息是常量池，用于存放编译器生成的各种字面量和符号引用，这部分内存在类加载后进入方法区的运行时常量池存放。
   
   直接内存：
   
   独立于Java堆和Native堆，收到本机总内存的影响。
   
   >可能延伸出的问题
   
   - 既然说程序计数器是唯一一个没有OutOfMemory的区域，那么说说有哪些区域会有OutOfMemory？
   
   答：Java虚拟机栈，Java堆，本地方法栈，方法区。虚拟机栈的内存是可以动态扩展的，但是如果动态扩展时，申请不到足够的内存的话，那么会抛出OutOfMemory；Java堆如果达到了最大使用内存，那么再次开辟内存的时候，同样也会抛出OutOfMemory；本地方法栈如果调用Native方法没有足够内存或者没有释放的时候，那么也会抛出OutOfMemory；方法区中由于有运行时常量池的存在，这部分是最占内存的，如果没有足够内存的时候，同样会OutOfMemory。
   
   - ##### 来说一下线程的生命周期
   
   - ##### 局部变量表里存放是有哪些？
   
   - ##### 说一下一个栈帧在虚拟机栈中入栈出栈的过程
   
   - ##### 什么情况下会StackOverflowError？栈深度怎么算？栈和堆所占内存比是多少？
   
   - ##### Eden空间、From Survivor空间、To Survivor空间内存占比怎么算？
   
   - ##### 虚拟机加载类的过程是什么样的？
   
   - ##### 直接内存什么情况下会抛出**OutOfMemoryError**？
   
2. ####  当new一个对象的时候，对象的创建是怎么样一个过程？

   虚拟机遇到一条new执行时，首先将去检查这个指令的参数是否能在常量池中定位到一个类的符号引用，并且检查这个符号引用代表的类是否已经被加载、解析和初始化过。如果没有，那么必须先执行相应的类加载过程。

   类加载检查通过后，虚拟机就会为对象分配内存。对象所需内存的大小在类加载完成后便可完全确定。如果Java堆中的内存是绝对规整的，所有用过的内存都放在一边，空闲的放在一边，中间放着一个指针作为分界点的指示器，那分配内存就是仅仅把那个指针向空闲空间挪动一段与对象大小相等的距离，这种分配方式叫做指针碰撞（Bump the Pointer）。如果内存并不是规整的，已经使用内存和未使用的内存相互交错，这种情况下就没办法使用指针碰撞。那么Java虚拟机就必须维护一个列表，记录哪些内存块是可用的，在分配的时候从列表中找到一块足够大的空间划分给对象实例，并更新列表上的记录，这种分配方式叫做空闲列表（Free List）。选择哪种分配方式由Java堆是否规整决定，而Java堆是否规整又由所采用的的垃圾收集器是否带有压缩整理功能决定。因此，在使用Serial，ParNew等带Compact过程的收集器时，系统采用的分配算法是指针碰撞，而使用CMS这种基于Mark-Sweep算法的收集器时，通常采用空闲列表。

   但是在对象的创建是一个非常频繁的行为，并发情况下并不是线程安全的，有可能指针指向A，来没来得及修改，B就使用了原来的指针进行分配内存。为了避免这种情况，有两种处理方式，一种是对分配内存空间的操作进行同步处理，JVM采用CAS+失败重试的方式保证更新操作的原子性；另一种就是把内存分配的动作按照线程划分在不同的空间之中进行，即每个线程在Java堆中预先分配一小块内存，称为本地线程分配缓冲（Thread Local Allocation Buffer，TLAB）。哪个线程需要分配内存，就在哪个线程的TLAB上分配，只有TLAB用完并且分配新的TLAB时，才需要进行同步锁定。虚拟机是否使用TLAB，通过-XX:+/-UseTLAB。

   内存分配完毕后，虚拟机就需要将分配到的内存空间都初始化为零值（不包括对象头），如果使用TLAB，这一工作过程也可以提前至TLAB分配时进行。**这一步保证了对象的实例字段在Java代码中可以不赋初始值就直接使用，程序能访问到这些字段的数据类型所对的零值**。

   然后对对象进行必要设置，如这个对象是哪个类的实例、如何才能找到类的元数据信息、对象的哈希码、对象的GC分代年龄信息。**这些信息存放在对象的对象头（Object Header）中**。根据虚拟机当前的运行状态的不同，如是否启用偏向锁等，对象头会有不同的设置方式。
   
   然后对象就已经产生了，但是知道虚拟机执行了init方法之后，把对象按照意愿进行初始化，这才算完成产生。
   
   > 延伸问题
   
   - ##### 来说一下类加载的过程是什么样。
   - ##### 如何确定一个对象所需内存大小？
   - ##### 内存是如何进行分配的？
   - ##### 如何确定内存分配方式？
   - ##### 哪种垃圾收集器带有压缩整理功能？
   - ##### 有哪几种垃圾收集器？分别有哪些特点？采用的垃圾回收算法有哪几种？分别是什么？
   - ##### 对象的创建过程是线程安全的么？不是？如何解决的？
   - ##### 开启TLAB的参数是什么？默认是否使用TLAB？
   - ##### 对象头什么时候进行初始化？包含哪些信息
   - ##### 对象头有哪几种不同的设置方式？


3. #### 说一下对象的内存布局

   在HotSpot虚拟机中，对象在内存中存储的的布局可以分为三个区域：**对象头（Object Header），实例数据（Instance Data），对象填充（Padding）**。

   对象头包括两部分信息，**第一部分用于存储对象自身的运行时数据，如哈希码，GC分代年龄，锁状态标志，线程持有的锁，偏向线程ID，偏向时间戳等。另一部分是类型指针，既对象指向它的的类元数据的指针，虚拟机通过这个指针来确定这个对象是哪个类的实例**。但是不是所有的虚拟机实现都必须在对象数据上保留类型指针。另外如果对象是一个Java数组，那在对象头中必须还有一块用于记录数组长度的数据，因为虚拟机可以通过普通Java对象的元数据信息确定Java对象的大小，但是从数组的元数据中无法确定数组的大小。

   实例数据存放的是对象真正存储的有效信息，也是在程序代码中所定义的各种类型的字段内容。无论是从父类继承下来的，还是在子类中定义的，都需要记录。这部分的存储顺序会受到虚拟机分配策略参数（FieldsAllocationStyle）和字段在Java源码中定义的顺序的影响。HotSpot虚拟机默认的分配策略为longs/doubles、ints、shorts/chars、bytes/booleans、oops（Ordinary Object Pointers），从分配策略中可以看出，相同宽度的字段总是被分配到一起。满足这个前提条件的情况下，在父类中定义的变量会出现在子类之前。如果CompactFields参数值为true（默认），那么子类中较窄的变量也可能会插入到父类变量的空隙之中。

   对齐填充不是必然存在，仅仅起到占位符的作用。因为HotSpot的自动内存管理系统要求对象起始地址必须是8字节的整数倍，也就是说，对象的大小必须是8字节的整数倍。而对象头正好是8字节的整数倍，也就是实例部分没有对齐时，就需要用对齐填充来补全。

   > 延伸问题

   - ##### 哪个虚拟机实现不保留类型指针？
   - ##### 虚拟机分配策略参数有哪些？分配策略有哪些？
   - ##### 有没有可能子类的变量出现在父类变量之前？

4. #### 说一下对象访问的时候是如何进行定位的？

   访问对象是通过栈上的reference数据来操作具体对象，两种办法，直接指针和句柄访问。

   句柄访问的话，会在Java堆中划分出一块内存作为句柄池，reference中存储的是对象的句柄地址，而句柄中包含了对象实例数据和类型数据各自的地址信息。

   直接指针的话，reference中存储的直接就是对象地址。

   使用句柄访问的好处是，reference中存储的是句柄地址，在对象被于东的时候只会改变句柄中的实例数据地址，而reference本身则不需要修改。

   直接指针的最好好处就是速度更快，节省了一次指针定位的时间开销。HotSpot主要使用直接指针。

   > 延伸问题

   - 直接指针和句柄访问各自有什么优缺点，哪个使用的比较多？
   
5. #### 什么情况下回OOM？写个代码，使其抛出OutOfMemoryError

   堆的话，可能会有内存泄露和内存溢出两种情况。如果一直新建对象，但是GC无法回收，超过了GC所允许的最大内存，那么就会产生OOM。

   如果内存泄漏，那么就检查泄露对象到GC Roots的引用链是通过怎样的路径关联并导致垃圾收集器无法自动回收它们的。如果是内存溢出，那么就调大内存，然后检查代码中是否有某些对象生命周期过长，持有时间过长等情况。

   栈的话，如果说请求栈的深度超过了允许的深度，会抛出StackoverFlow异常，但是如果栈无法申请到足够的内存的话，则会抛出OOM异常。

   运行时常量池的话，一直添加字符串，将其固化到常量池填满，应该就会OOM。

   方法区，由于方法区存放的是类的信息，那么构造足够多的类，将其撑爆就会OOM。

   本机直接内存。直接或者间接使用NIO，总内存不够，则会抛出OOM。

   > 延伸问题

   - ##### 什么是内存泄漏，什么是内存溢出？

   - ##### 控制内存大小的参数是什么？
   
6. #### 判断对象是否存活的算法有哪几种？各有什么特点？

   引用计数算法和可达性分析算法。

   

   > 延伸问题

   - ##### 可作为GC Roots的对象包括哪几种？

7. #### 说一说Java的引用

8. #### 如何判断一个常量是否为废弃常量？如何判断一个类是无用的类？

9. #### 说一下常用的垃圾收集算法

10. #### 说一下有哪些垃圾收集器，分别有什么用途，各自用的什么算法？

11. #### 既然你说到了G1收集器，那你说说G1收集器

12. #### G1和CMS有哪些区别？

  ​    

    ​    
