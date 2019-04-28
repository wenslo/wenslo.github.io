---
title: 数据结构-栈和队列

categories: 

- 数据结构

tags: 

- 栈和队列



---

&emsp;&emsp;**栈（Last In First Out）**和**队列（First In First Out）**

&emsp;&emsp;细分的话，可以分为**顺序栈**，**链栈**，**顺序队列**，**链队**。也就是一个用**数组**实现，一个用**链表**实现，区别的话，也就是顺序存储和链式存储的区别。而JDK中util下的Stack，**实现是顺序栈**。

&emsp;&emsp;栈一般有两个指针，**top和base**，top指向栈顶，base指向栈底。这样的话，**top==base**，说明栈空，**top-base>=stacksize**的话，则说明栈满。

&emsp;&emsp;JDK的Stack的pop实现，是先进行peek()，然后remove()。这就说明了pop和peek的区别，pop是**弹出栈顶元素**，peek，顾名思义，偷看，**只查看栈顶元素**。JDK中是**并没有**提供链栈的实现的，没有说和List一样，提供一个接口，然后提供ArrayList和LinkedList两种实现。只有一个Stack的类，继承与Vector。但是看源码能够知道，util的LinkedList包含有peek，pop，push这些方法。也就是说***LinkedList完全可以将其当成是链栈来用***！LinkedList支持栈的操作，那么同理，ArrayList应该也支持栈的操作。毕竟都是线性表么。因此，区别的话就能知道：**ArrayList和LinkedList不是线程安全，而Stack是线程安全的**。

&emsp;&emsp;补充一下，ArrayList和LinkedList都实现的栈和队列的方法。

&emsp;&emsp;现代化的计算机将栈作为它的指令系统的一部分，也就是说，**栈很可能是计算机科学中除了数组之后的最基本的数据结构**。

&emsp;&emsp;用栈来实现进制转换很容易，除以n，然后余数压入栈中，依次进行，最后依次出栈。

&emsp;&emsp;**递归**实际上就是利用栈来实现的，自调用，层层入栈，层层出栈。

&emsp;&emsp;队列一般**长度固定**，然后实现是**循环队列**。这样的话，能够合理利用空间。用front指向队头，用tail指向队尾。front==tail的时候，就说明队列为空。