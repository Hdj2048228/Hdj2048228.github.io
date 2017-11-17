---
layout:     post
title:      copy 和 mutableCopy
subtitle:   
date:       2017-11-15
author:     hdj
header-img: img/bgs/XCode-bg.jpg
catalog: true
tags:
    - iOS
    - copy 和 mutableCopy
---
# 前言

  一直不太懂copy 和mutable的区别，自从看了下面的文章，瞬间明悟了
  

[OC中的copy](http://www.jianshu.com/p/5f776a4816ee)


### 总结
   1. 使用copy的必须遵守NSCopy或NSMutableCopy协议，（view 使用copy 会报错）
   2. copy 的不可变对象和strong一样 引用计数+1，可变对象返回一个新对象，引用计数不变，且返回的是一个不可变对象，所以不能使用可变对象的属性方法，会crash
   3. mutableCopy都是返回一个新对象且可变，对于集合是集合中的值是指针拷贝，集合是新对象拷贝。
   
	
	
#### copy 和 mutableCopy详情
	
  1. 浅复制和深层复制的区别 自然语言描述：
    浅复制是镜子中的自己，自己做什么动作，镜子中的自己就做什么
    深层复制：就是自己的克隆人，虽然是从你身上克隆过去的，但是你未必找的得到女朋友，他说不定就找到了！
	
  2. 浅复制和深层复制的区别 程序猿描述：
  3. 浅复制，并不拷贝对象本身，仅仅是拷贝指向对象的指针；深复制是除了拷贝指向对象的指针，而且直接 拷贝整个对象内存到另一块内存中。再简单些说:
  **浅复制就是指针拷贝；深复制就是内容拷贝。**
    
	
> 浅copy: 指针复制，不会创建一个新的对象。
> 深copy: 内容复制，会创建一个新的对象。
	
### 对引用计数的影响
	
> * 浅copy，类似strong，持有原始对象的指针，会使retainCount加一。
> **浅copy和strong引用的区别仅仅是浅copy多执行一步copyWithZone:方法。**
 
> * 深copy，会创建一个新的对象，不会对原始对象的retainCount变化。
关键点
	
找准源对象类型才能分清copy 是浅拷贝还是深拷贝
#### 使用原则:
> * **针对不可变对象调用copy返回该对象本身，调用mutableCopy返回一个可变对象（新的）；**
> * **针对可变对象调用copy返回一个不可变对象（新的），调用mutableCopy返回另外一个可变对象（新的）。**
	
#### 非集合类的对象( NSString, NSNumber ... ) shallow copy 和 deep copy
	
####   准则
	
不管是集合类对象，还是非集合类对象，接收到copy和mutableCopy消息时，都遵循以下准则：
	
> 1. copy返回imutable对象；mutableCopy返回mutable对象；
> 2. 对copy返回值使用mutable对象接口就会crash
	
系统非集合类对象指的是 NSString, NSNumber ... 之类的对象。下面先看个非集合类immutable对象拷贝的例子:
	
	
> NSString *string = @"origin";
> 
> NSString *stringCopy = [string copy];
> 
> NSMutableString *stringMCopy = [string mutableCopy];
	
	
	
通过查看内存，可以看到 stringCopy 和 string 的地址是一样，进行了指针拷贝；而 stringMCopy 的地址和 string 不一样，进行了内容拷贝；
	
### 在非集合类对象中：
对immutable对象进行copy操作，是指针复制，mutableCopy操作时内容复制；
对mutable对象进行copy和mutableCopy都是内容复制。
	
用代码简单表示如下：
> 
>    [immutableObject copy] // 浅复制 + 不可变对象 
>   
>    [immutableObject mutableCopy] //深复制 + 可变对象
> 
>    [mutableObject copy] //深复制 + 不可变对象
> 
>   [mutableObject mutableCopy] //深复制 + 可变对象
	
	
### copy做为 property 修饰关键字
	
UIView及其父类 并没有像 数组 字典 字符串这些类一样 遵守 NSCopying, NSMutableCopying 协议 view使用copy 会crash。
	
### 集合类的对象自身的 shallow copy 和 deep copy
集合类对象是指NSArray、NSDictionary、NSSet ...
	
copy操作进行了指针拷贝，mutableCopy进行了内容拷贝。但需要强调的是：
 **此处的内容拷贝，仅仅是拷贝array这个对象，array集合内部的元素仍然是指针拷贝**。
	
 在集合类对象中，对immutable对象进行copy，是指针复制，mutableCopy是内容复制；对mutable对象进行copy和mutableCopy都是内容复制。
 但是： **集合对象的内容复制仅限于对象本身，对象元素仍然是指针复制**。用代码简单表示如下：
> 
> [immutableObject copy] // 浅复制 + 不可变对象
> 
> [immutableObject mutableCopy] //单层深复制 + 可变对象
> 
> [mutableObject copy] //单层深复制 + 不可变对象
> 
> [mutableObject mutableCopy] //单层深复制 + 可变对象
	
	
	
### copy 和 block 的关系
	
首先说block的三种类型。
> 
> 1. _NSConcreteGlobalBlock
> 全局的静态block，不会访问外部的变量。就是说block没有调用其他的外部变量。
> 
> 2. _NSConcreteStackBlock
> 保存在栈中的 block，当函数返回时会被销毁。这个block就是没有被赋值，并且block访问了外部变量。
> 
> 3. _NSConcreteMallocBlock
> 保存在堆中的 block，当引用计数为 0 时会被销毁，对_NSConcreteStackBlock 执行copy得来的。
	
##### 代码举例
	
> 
> - (void)textBlock {
>     // __NSGlobalBlock__
>     ^{NSLog(@"1111");}
>     ();
> 
>     // __NSStackBlock__
>     ^{NSLog(@"%@",self);}
>     ();
> 
>     // __NSMallocBlock__
>     // 等号是赋值运算, 相当于对block进行copy, 即相当于自动调用了block的copy方法
>     void(^blkStack)(void) = ^{NSLog(@"%d",i);};
>     blkStack();
>  }

	
**block 使用copy 修饰的原因：**
	
我们知道，函数的声明周期是随着函数调用的结束就终止了。我们的block是写在函数中的。
	
> 1. 如果是全局静态block的话，他直到程序结束的时候，才会被被释放。但是我们实际操作中基本上不会使用到不访问外部变量的block。
> 
> 2. 如果是保存在栈中的block，他会随着函数调用结束被销毁。从而导致我们在执行一个包含block的函数之后，就无法再访问这个block。因为（函数结束，函数栈就销毁了，存在函数里面的block也就没有了），我们再使用block时，就会产生空指针异常。
> 
> 3. 如果是堆中的block，也就是copy修饰的block。他的生命周期就是随着对象的销毁而结束的。只要对象不销毁，我们就可以调用的到在堆中的block。
	
这就是为什么我们要用copy来修饰block。因为**不用copy修饰的访问外部变量的block，只在他所在的函数被调用的那一瞬间可以使用。之后就消失了。**
	
##### block 什么时候被自动copy
	
经过代码验证，得出下面的结论，大家有兴趣的可以自己再验证一下。有错误的地方，欢迎大伙积极留言指出。
	
>  1. 作为变量：
> 
>     * 一个 block 刚声明的时候是在栈上
> 
>     * 赋值给一个普通变量之后就会被 copy 到堆上
> 
>     * 赋值给一个 weak 变量不会被 copy
> 
>  2. 作为属性：
> 
>     * 用 strong 和 copy 修饰的属性会被 copy
>     * 用 weak 和 assign 修饰的属性不会被 copy
> 
>  3. 函数传参：
>     * 作为参数传入函数 block不会被 copy
>     * 作为函数的返回值 block会被 copy
>     * 针对 block 做为参数 传入函数 block 不会被copy，这些知名的开源库是这么做的