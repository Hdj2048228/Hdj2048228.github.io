---
layout:     post
title:      隐式类型转换
subtitle:   从 ([]==![]) 为 true 来剖析 JavaScript 隐式类型转换
date:       2021-08-05
author:     hdj
header-img: img/bgs/girl-3.jpg
catalog: true
categories : [js]

tags:
    - 隐式类型转换
---



# 前言
    
   JavaScript中的 == 和 === 之间有何区别， == 判断是否相等是否存在什么规则？让我们从 [] !== []
   这个简单的表达式开始来窥探究竟。

#  先来看看 [] == []的 为什么是false
   
   这个简单，理解js数据类型的应该都知道，对象类型是引用类型，之所以 [] == [] 输出为false，是因为，虽然长得
  一模一样，但其实是两个不同的对象，就像两个双胞胎，看起来一样，但是其身份证号（内存地址）是不一样的，，所以 对象之间的比较是比较的内存地址，
  同理 {} == {} 也是false
  
   ![[] == []](http://hdj2048228.github.io/img/2021-08/[]==[].png)

   ![{} == {}](http://hdj2048228.github.io/img/2021-08/{}=={}.png)
  
# 那么 [] == ![] 的结果是是什么呢
    
   先来看答案
   
   ![[] == []](http://hdj2048228.github.io/img/2021-08/[]==![].png)
   
   **why?**
   
## 首先先了解一下JavaScript提供三种不同的值比较操作
  
  1. 严格相等比较 (也被称作"strict equality", "identity", "triple equals")，使用 === ,
    
  2. 抽象相等比较 ("loose equality"，"double equals") ，使用 ==
    
  3. 以及 Object.is （ECMAScript 2015/ ES6 新特性）
  
  三种比较的区别
    
  **双等号将执行类型转换;** 
  
  **三等号将进行相同的比较，而不进行类型转换 (如果类型不同, 只是总会返回 false )**
   
  **Object.is的行为方式与三等号相同，但是对于NaN和-0和+0进行特殊处理，所以最后两个不相同，而Object.is（NaN，NaN）将为 true。**
  
  (通常使用双等号或三等号将NaN与NaN进行比较，结果为false) 请注意，所有这些之间的区别都与其处理原语有关; 
  这三个运算符的原语中，没有一个会比较两个变量是否结构上概念类似。对于任意两个不同的非原始对象，即便他们有相同的结构， 以上三个运算符都会计算得到 false 。
  
  **由此我们得知 == 会发生隐式类型转换**
  
## 然后我们再了解一下运算符的优先级
   
   [MDN-运算符优先级](https://developer.mozilla.orghttps://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)
   

  
   通过 运算符优先级我们发现 **！** 的优先级是大于 **==**的
    
   通过[ECMAScript 2021标准](https://tc39.es/ecma262/)我们得知以下内容
  
   总结起来就是**!**  会把其后的表达式得结果转为Boolean值
   
   这就涉及到另外一个知识点了，什么样的值为**true** 什么样的值为**false**?
   
   [MDN-真值（Truthy）](https://developer.mozilla.org/zh-CN/docs/Glossary/Truthy)
   
  >Truthy（真值）
    在 JavaScript 中，truthy（真值）指的是在布尔值上下文中，转换后的值为真的值。所有值都是真值，除非它们被定义为 假值
    （即除 false、0、""、null、undefined 和 NaN 以外皆为真值）。
   JavaScript 中的真值示例如下（将被转换为 true，if 后的代码段将被执行）：   
    
       if (true)
       if ({})
      if ([])
       if (42)
       if ("foo")
       if (new Date())
       if (-42)
       if (3.14)
       if (-3.14)
       if (Infinity)
       if (-Infinity)    
  
  
  由此可得 **[] 为 true**,那么 **![]** 则为 **false**
  
  那么现在的表达式可以改为 **[] == false** 
  
##  [] == false???

  是不是很疑惑按上面讲的来说， **[] 为true** 那岂不是 可以改为 **true == false** 了，结果不就是**false**嘛
  
## 隐式类型转换规则
    
   [ES5模式下](https://yanhaijing.com/es5/#200)
    
   比较运算x==y, 其中x和 y是值，产生true或者false。这样的比较按如下方式进行：
      
    1. 若Type(x)与Type(y)相同， 则
        a.若Type(x)为Undefined， 返回true。
        b.若Type(x)为Null， 返回true。
        c.若Type(x)为Number， 则
            i若x为NaN， 返回false。
            ii若y为NaN， 返回false。
            iii若x与y为相等数值， 返回true。
            iv若x 为 +0 且 y为−0， 返回true。
            v若x 为 −0 且 y为+0， 返回true。
            vi 返回false。
        d.若Type(x)为String, 则当x和y为完全相同的字符序列（长度相等且相同字符在相同位置）时返回true。 否则， 返回false。
        e.若Type(x)为Boolean, 当x和y为同为true或者同为false时返回true。 否则， 返回false。
        f.当x和y为引用同一对象时返回true。否则，返回false。
    2.若x为null且y为undefined， 返回true。
    3.若x为undefined且y为null， 返回true。
    4.若Type(x) 为 Number 且 Type(y)为String， 返回comparison x == ToNumber(y)的结果。
    5.若Type(x) 为 String 且 Type(y)为Number，
    6.返回比较ToNumber(x) == y的结果。
    7.若Type(x)为Boolean， 返回比较ToNumber(x) == y的结果。
    8.若Type(y)为Boolean， 返回比较x == ToNumber(y)的结果。
    9.若Type(x)为String或Number，且Type(y)为Object，返回比较x == ToPrimitive(y)的结果。
    10.若Type(x)为Object且Type(y)为String或Number， 返回比较ToPrimitive(x) == y的结果。
    11.返回false。
 
  
  按照以上规则我们再来看 **[] == false**
  
  则会发现 **[]** 与 **false** 类型不同，且**Type(false)** 为Boolean满足**第8条** 则转化为**[] == ToNumber(false)** 即 **[] == 0**
  
   **[] == 0** 继续规则发现 **Type([])** 为 **Object** 则满足**第10条** 则转化为**ToPrimitive([]) == 0**
   
##  ToPrimitive([]) == 0   
  
  
    ToPrimitive(obj,preferredType)
    
    JS引擎内部转换为原始值ToPrimitive(obj,preferredType)函数接受两个参数，第一个obj为被转换的对象，第二个
    preferredType为希望转换成的类型（默认为空，接受的值为Number或String）
    
    在执行ToPrimitive(obj,preferredType)时如果第二个参数为空并且obj为Date的事例时，此时preferredType会
    被设置为String，其他情况下preferredType都会被设置为Number如果preferredType为Number，ToPrimitive执
    行过程如下：
    1. 如果obj为原始值，直接返回；
    2. 否则调用 obj.valueOf()，如果执行结果是原始值，返回之；
    3. 否则调用 obj.toString()，如果执行结果是原始值，返回之；
    4. 否则抛异常。
    
    如果preferredType为String，将上面的第2步和第3步调换，即：
    1. 如果obj为原始值，直接返回；
    2. 否则调用 obj.toString()，如果执行结果是原始值，返回之；
    3. 否则调用 obj.valueOf()，如果执行结果是原始值，返回之；
    4. 否则抛异常。
    
  **对象的 valueOf() 方法和 toString() 方法**
    
   对象在执行 ToPrimitive 转换时，需要用到对象的valueOf()和toString()方法。我们可以在Object.prototype上找到这两个方法。在JavaScript中，Object.prototype是所有对象原型链的顶层原型，因此，任何对象都有valueOf()和toString()方法。
    
   javaScript的许多内置对象都重写了这两个方法，以实现更适合自身的功能需要。
    
   [Object.prototype.valueOf()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/valueOf)
  
   >不同类型对象的valueOf()方法的返回值：
     Array： 返回数组对象本身。
     Boolean： 布尔值。
     Date： 存储的时间是从 1970 年 1 月 1 日午夜开始计的毫秒数 UTC。
     Function： 函数本身。
     Number： 数字值。
     Object： 对象本身。这是默认情况。
     String： 字符串值。
     Symbol：Symbol值本身。
     
     
   [Object.prototype.toString()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/toString)  
   
   >不同类型对象的toString()方法的返回值：
    Array：连接数组并返回一个字符串，其中包含用逗号分隔的每个数组元素。
    Boolean：返回字符串 "true" 或 "false"。
    Date：返回一个美式英语日期格式的字符串。
    Function：返回一个字符串，其中包含用于定义函数的源文本段。
    Number： 返回指定 Number 对象的字符串表示形式。
    Object： 返回 "[object type]"，其中 type 是对象的类型。
    String： 字符串值。
    Symbol：返回当前 Symbol 对象的字符串表示
    
 据上述规则来看的话
     
   **ToPrimitive([])** 得到的是 空字符穿  **''** ，则意味着比较的是 **'' == 0**

### 思考 如何让 {} == 2 的结果为true   
     
   let a = {}  
   
   a == 2 返回 true
     
     
##  '' == 0 
      
  '' == 0 符合上面规则中的第 5 条，对 '' 进行 ToNumber 转换，得到 0,则比较的是 **0 == 0**
  
## 0 == 0  
  
   **0 == 0**  符合 第1条-c-iii 肯定为**true**了哈
   
## 复盘过程总结 

 现在，我们来分析一下文章开头提出的问题：[] == ![] // true
    
  * 根据 上面 逻辑非运算符 和 ToPrimitive 的规则，![] 返回 false，因此，我们接下来需要比较的是 [] == false；
    
  * [] == false 符合上面规则中的第 8 条，需要对 false 执行 ToNumber 转换，得到 0，接下来要比较 [] == 0；
  
  * [] == 0 符合上面规则中的第 10 条，对 [] 进行 ToPrimitive 转换，得到空字符串 ''，接下来要比较 '' == 0；
  * '' == 0 符合上面规则中的第 5 条，对 '' 进行 ToNumber 转换，得到 0
  * 接下来比较 0 == 0，得到true      
   
   
## 扩展

     []==[]
     //false
     []==![]
     //true
     {}==!{}
     //false
     {}==![]
     //VM1896:1 Uncaught SyntaxError: Unexpected token ==
     ![]=={}
     //false
     []==!{}
     //true
     undefined==null
     //true
   
   
   
###  JS数据类型
 
   基本数据类型 ：Undefined Null Boolean Number String
    
   引用类型:    Object
           
   ES6+:       Symbol	BigInt	         
  
  
### IsLooselyEqual
 [ecma262 定义——IsLooselyEqual](https://tc39.es/ecma262/#sec-islooselyequal)
       
       7.2.14 IsLooselyEqual ( x, y )
       The abstract operation IsLooselyEqual takes arguments x (an ECMAScript language value) and y (an ECMAScript language value). It provides the semantics for the comparison x == y, returning true or false. It performs the following steps when called:
       
       1. If Type(x) is the same as Type(y), then
       a. Return IsStrictlyEqual(x, y).
       2. If x is null and y is undefined, return true.
       3. If x is undefined and y is null, return true.
       4. NOTE: This step is replaced in section B.3.6.2.
       5. If Type(x) is Number and Type(y) is String, return IsLooselyEqual(x, ! ToNumber(y)).
       6. If Type(x) is String and Type(y) is Number, return IsLooselyEqual(! ToNumber(x), y).
       7. If Type(x) is BigInt and Type(y) is String, then
       a. Let n be ! StringToBigInt(y).
       b. If n is NaN, return false.
       c. Return IsLooselyEqual(x, n).
       8. If Type(x) is String and Type(y) is BigInt, return IsLooselyEqual(y, x).
       9. If Type(x) is Boolean, return IsLooselyEqual(! ToNumber(x), y).
       10. If Type(y) is Boolean, return IsLooselyEqual(x, ! ToNumber(y)).
       11. If Type(x) is either String, Number, BigInt, or Symbol and Type(y) is Object, return IsLooselyEqual(x, ? ToPrimitive(y)).
       12. If Type(x) is Object and Type(y) is either String, Number, BigInt, or Symbol, return IsLooselyEqual(? ToPrimitive(x), y).
       13. If Type(x) is BigInt and Type(y) is Number, or if Type(x) is Number and Type(y) is BigInt, then
               a. If x or y are any of NaN, +∞𝔽, or -∞𝔽, return false.
               b. If ℝ(x) = ℝ(y), return true; otherwise return false.
       14. Return false.
      
      7.2.15 IsStrictlyEqual ( x, y )
      The abstract operation IsStrictlyEqual takes arguments x (an ECMAScript language value) and y (an ECMAScript language value). It provides the semantics for the comparison x === y, returning true or false. It performs the following steps when called:
      
      1. If Type(x) is different from Type(y), return false.
      2. If Type(x) is Number or BigInt, then
      a. Return ! Type(x)::equal(x, y).
      3. Return ! SameValueNonNumeric(x, y).
    
    
   按以上相等之定义：
  
  字符串比较可以按这种方式强制执行: "" + a == "" + b 。
  
  数值比较可以按这种方式强制执行: +a == +b 。
  
  布尔值比较可以按这种方式强制执行: !a == !b 。
  
  等值比较操作保证以下不变：
  
  A != B 等价于 !(A==B) 。
  
  A == B 等价于 B == A ，除了A与B的执行顺序。
  
  相等运算符不总是传递的。例如，两个不同的String对象，都表示相同的字符串值； == 运算符认为每个String对象都与字符串值相等，但是两个字符串对象互不相等。例如：
  
        new String("a") == "a" 和 "a" == new String("a") 皆为true。
        
        new String("a") == new String("a") 为false。
  
   字符串比较使用的方式是简单地检测字符编码单元序列是否相同。不会做更复杂的、基于语义的字符或者字符串相等的定义以及Unicode规范中定义的collating order。所以Unicode标准中认为相等的String值可能被检测为不等。实际上这一算法认为两个字符串已经是经过规范化的形式。
  

###  7.1.1 ToPrimitive ( input [ , preferredType ] )

 ![ToPrimitive](http://hdj2048228.github.io/img/2021-08/ToPrimitive.png)

### 7.1.1.1 OrdinaryToPrimitive ( O, hint )
 
 ![OrdinaryToPrimitive](http://hdj2048228.github.io/img/2021-08/OrdinaryToPrimitive.png)

     
### 7.1.1.1 OrdinaryToPrimitive ( O, hint )
 
  ![OrdinaryToPrimitive](http://hdj2048228.github.io/img/2021-08/OrdinaryToPrimitive.png)

     
### 7.1.2 ToBoolean ( argument )
     
   ![ToBoolean](http://hdj2048228.github.io/img/2021-08/ToBoolean.png)

   * Undefined：false
   * Null：false
   * Boolean：结果等于输入的参数（不转换）。
   * Number：如果参数是 +0, -0, 或 NaN，结果为 false ；否则结果为 true。
   * String：如果参数是空字符串（其长度为零），结果为 false，否则结果为 true。
   * Symbol：true。
   * Object：true。   
### 7.1.2 ToNumber ( argument )

   ![ToNumber](http://hdj2048228.github.io/img/2021-08/ToNumber.png)

   * Undefined：NaN
   * Null：+0
   * Boolean：如果参数是 true，结果为 1。如果参数是 false，此结果为 +0。
   * Number：结果等于输入的参数（不转换）。
   * String：参见下文
   * Symbol：抛出 TypeError 异常
   * Object：先进行 ToPrimitive 转换，得到原始值，再进行 ToNumber 转换
   
### 7.1.23 对字符串类型应用 ToNumber

>对字符串应用 ToNumber 时，如果符合如下规则，转为数值：
 
* 十进制的字符串数值常量，可有任意位数的0在前面，如 '000123' 和 '123' 都会被转为 123
* 指数形式的字符串数值常量，如 '1e2' 转为 100
* 带符号的十进制字符串数值常量或指数字符串数值常量，如'-100', '-1e2' 都会转为 -100
* 二进制，八进制，十六进制的字符串数值常量，如'0b11', '0o11', '0x11' 分别转为 3, 9, 17
* 符合上述条件的字符串数值常量开头或结尾，可以包含任意多个空格。如' 0b11 ' 转为 3
* 空字符串（长度为零的字符串）或只有空格的字符串，转为 0
* 如果字符串不符合上述规则，将转为NaN。  

### 运算符优先级

 <table class="fullwidth-table">
            <tbody>
                <tr>
                    <th>优先级</th>
                    <th>运算符类型</th>
                    <th>结合性</th>
                    <th>运算符</th>
                </tr>
                <tr>
                    <td>21</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Grouping">分组</a></td>
                    <td>n/a（不相关）</td>
                    <td><code>( … )</code></td>
                </tr>
                <tr>
                    <td rowspan="5">20</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Property_Accessors#dot_notation">成员访问</a></td>
                    <td>从左到右</td>
                    <td><code>… . …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Property_Accessors#bracket_notation">需计算的成员访问</a></td>
                    <td>从左到右</td>
                    <td><code>… [ … ]</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new"><code>new</code></a>（带参数列表）</td>
                    <td>n/a</td>
                    <td><code>new … ( … )</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Functions">函数调用</a></td>
                    <td>从左到右</td>
                    <td><code>… ( <var>… </var>)</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Optional_chaining">可选链（Optional chaining）</a></td>
                    <td>从左到右</td>
                    <td><code>?.</code></td>
                </tr>
                <tr>
                    <td>19</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new"><code>new</code></a>（无参数列表）</td>
                    <td>从右到左</td>
                    <td><code>new …</code></td>
                </tr>
                <tr>
                    <td rowspan="2">18</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators#increment">后置递增</a></td>
                    <td rowspan="2">n/a</td>
                    <td><code>… ++</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators#decrement">后置递减</a></td>
                    <td><code>… --</code></td>
                </tr>
                <tr>
                    <td rowspan="10">17</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Logical_NOT">逻辑非 (!)</a></td>
                    <td rowspan="10">从右到左</td>
                    <td><code>! …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_NOT">按位非 (~)</a></td>
                    <td><code>~ …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Unary_plus">一元加法 (+)</a></td>
                    <td><code>+ …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Unary_negation">一元减法 (-)</a></td>
                    <td><code>- …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators#increment">前置递增</a></td>
                    <td><code>++ …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators#decrement">前置递减</a></td>
                    <td><code>-- …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/typeof"><code>typeof</code></a></td>
                    <td><code>typeof …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/void"><code>void</code></a></td>
                    <td><code>void …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/delete"><code>delete</code></a></td>
                    <td><code>delete …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/await"><code>await</code></a></td>
                    <td><code>await …</code></td>
                </tr>
                <tr>
                    <td>16</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Exponentiation">幂 (**)</a></td>
                    <td>从右到左</td>
                    <td><code>… ** …</code></td>
                </tr>
                <tr>
                    <td rowspan="3">15</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Multiplication">乘法 (*)</a></td>
                    <td rowspan="3">从左到右</td>
                    <td><code>… * …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Division">除法 (/)</a></td>
                    <td><code>… / …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Remainder">取余 (%)</a></td>
                    <td><code>… % …</code></td>
                </tr>
                <tr>
                    <td rowspan="2">14</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Addition">加法 (+)</a></td>
                    <td rowspan="2">从左到右</td>
                    <td><code>… + …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Subtraction">减法 (-)</a></td>
                    <td><code>… - …</code></td>
                </tr>
                <tr>
                    <td rowspan="3">13</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Left_shift">按位左移 (&lt;&lt;)</a></td>
                    <td rowspan="3">从左到右</td>
                    <td><code>… &lt;&lt; …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Right_shift">按位右移 (&gt;&gt;)</a></td>
                    <td><code>… &gt;&gt; …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Unsigned_right_shift">无符号右移 (&gt;&gt;&gt;)</a></td>
                    <td><code>… &gt;&gt;&gt; …</code></td>
                </tr>
                <tr>
                    <td rowspan="6">12</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Less_than">小于 (&lt;)</a></td>
                    <td rowspan="6">从左到右</td>
                    <td><code>… &lt; …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Less_than_or_equal">小于等于 (&lt;=)</a></td>
                    <td><code>… &lt;= …</code></td>
                </tr>
                <tr>
                    <td><a href="/en-US/docs/Web/JavaScript/Reference/Operators/Greater_than">大于 (&gt;)</a></td>
                    <td><code>… &gt; …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Greater_than_or_equal">大于等于 (&gt;=)</a></td>
                    <td><code>… &gt;= …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/in"><code>in</code></a></td>
                    <td><code>… in …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/instanceof"><code>instanceof</code></a></td>
                    <td><code>… instanceof …</code></td>
                </tr>
                <tr>
                    <td rowspan="4">11</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Equality">相等 (==)</a></td>
                    <td rowspan="4">从左到右</td>
                    <td><code>… == …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Inequality">不相等 (!=)</a></td>
                    <td><code>… != …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Strict_equality">一致/严格相等 (===)</a></td>
                    <td><code>… === …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Strict_inequality">不一致/严格不相等 (!==)</a></td>
                    <td><code>… !== …</code></td>
                </tr>
                <tr>
                    <td>10</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_AND">按位与 (&amp;)</a></td>
                    <td>从左到右</td>
                    <td><code>… &amp; …</code></td>
                </tr>
                <tr>
                    <td>9</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_XOR">按位异或 (^)</a></td>
                    <td>从左到右</td>
                    <td><code>… ^ …</code></td>
                </tr>
                <tr>
                    <td>8</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_OR">按位或 (|)</a></td>
                    <td>从左到右</td>
                    <td><code>… | …</code></td>
                </tr>
                <tr>
                    <td>7</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Logical_AND">逻辑与 (&amp;&amp;)</a></td>
                    <td>从左到右</td>
                    <td><code>… &amp;&amp; …</code></td>
                </tr>
                <tr>
                    <td>6</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Logical_OR">逻辑或 (||)</a></td>
                    <td>从左到右</td>
                    <td><code>… || …</code></td>
                </tr>
                <tr>
                    <td>5</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_operator">空值合并 (??)</a></td>
                    <td>从左到右</td>
                    <td><code>… ?? …</code></td>
                </tr>
                <tr>
                    <td>4</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Conditional_Operator">条件（三元）运算符</a></td>
                    <td>从右到左</td>
                    <td><code>… ? … : …</code></td>
                </tr>
                <tr>
                    <td rowspan="16">3</td>
                    <td rowspan="16"><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators#assignment_operators">赋值</a></td>
                    <td rowspan="16">从右到左</td>
                    <td><code>… = …</code></td>
                </tr>
                <tr>
                    <td><code>… += …</code></td>
                </tr>
                <tr>
                    <td><code>… -= …</code></td>
                </tr>
                <tr>
                    <td><code>… **= …</code></td>
                </tr>
                <tr>
                    <td><code>… *= …</code></td>
                </tr>
                <tr>
                    <td><code>… /= …</code></td>
                </tr>
                <tr>
                    <td><code>… %= …</code></td>
                </tr>
                <tr>
                    <td><code>… &lt;&lt;= …</code></td>
                </tr>
                <tr>
                    <td><code>… &gt;&gt;= …</code></td>
                </tr>
                <tr>
                    <td><code>… &gt;&gt;&gt;= …</code></td>
                </tr>
                <tr>
                    <td><code>… &amp;= …</code></td>
                </tr>
                <tr>
                    <td><code>… ^= …</code></td>
                </tr>
                <tr>
                    <td><code>… |= …</code></td>
                </tr>
                <tr>
                    <td><code>… &amp;&amp;= …</code></td>
                </tr>
                <tr>
                    <td><code>… ||= …</code></td>
                </tr>
                <tr>
                    <td><code>… ??= …</code></td>
                </tr>
                <tr>
                    <td rowspan="2">2</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/yield"><code>yield</code></a></td>
                    <td rowspan="2">从右到左</td>
                    <td><code>yield …</code></td>
                </tr>
                <tr>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/yield*"><code>yield*</code></a></td>
                    <td><code>yield* …</code></td>
                </tr>
                <tr>
                    <td>1</td>
                    <td><a href="https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Comma_Operator">逗号 / 序列</a></td>
                    <td>从左到右</td>
                    <td><code>… , …</code></td>
                </tr>
            </tbody>
        </table>
  
  
  
     