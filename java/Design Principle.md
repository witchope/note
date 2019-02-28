---
title: Design Principle
---

# 设计原则

- [设计原则](#设计原则)
  - [1.单一职责原则（Single Responsibility Principle - SRP）](#1单一职责原则single-responsibility-principle---srp)
  - [2.开放封闭原则（Open Closed Principle - OCP）](#2开放封闭原则open-closed-principle---ocp)
  - [3.里氏替换原则（Liskov Substitution Principle - LSP）](#3里氏替换原则liskov-substitution-principle---lsp)
  - [4.接口隔离原则（Interface Segregation Principle - ISP）](#4接口隔离原则interface-segregation-principle---isp)
  - [5.依赖倒置原则（Dependence Inversion Principle - DIP）](#5依赖倒置原则dependence-inversion-principle---dip)

## 1.单一职责原则（Single Responsibility Principle - SRP）

    原文：There should never be more than one reason for a class to change.
    译文：永远不应该有多于一个原因来改变某个类。
    理解：对于一个类而言，应该仅有一个引起它变化的原因。说白了就是，不同的类具备不同的职责，各施其责。这就好比一个团队，大家分工协作，互不影响，各做各的事情。
    应用：当我们做系统设计时，如果发现有一个类拥有了两种的职责，那就问自己一个问题：可以将这个类分成两个类吗？如果真的有必要，那就分吧。千万不要让一个类干的事情太多！

## 2.开放封闭原则（Open Closed Principle - OCP）

    原文：Software entities like classes, modules and functions should be open for extension but closed for modifications.
    译文：软件实体，如：类、模块与函数，对于扩展应该是开放的，但对于修改应该是封闭的。
    理解：简言之，对扩展开放，对修改封闭。换句话说，可以去扩展类，但不要去修改类。
    应用：当需求有改动，要修改代码了，此时您要做的是，尽量用继承或组合的方式来扩展类的功能，而不是直接修改类的代码。当然，如果能够确保对整体架构不会产生任何影响，那么也没必要搞得那么复杂了，直接改这个类吧。

## 3.里氏替换原则（Liskov Substitution Principle - LSP）

    原文：Functions that use pointers or references to base classes must be able to use objects of derived classes without knowing it.
    译文：使用基类的指针或引用的函数，必须是在不知情的情况下，能够使用派生类的对象。
    理解：父类能够替换子类，但子类不一定能替换父类。也就是说，在代码中可以将父类全部替换为子类，程序不会报错，也不会在运行时出现任何异常，但反过来却不一定成立。
    应用：在继承类时，务必重写（Override）父类中所有的方法，尤其需要注意父类的 protected 方法（它们往往是让您重写的），子类尽量不要暴露自己的 public 方法供外界调用。

## 4.接口隔离原则（Interface Segregation Principle - ISP）

    原文：The dependency of one class to another one should depend on the smallest possible interface.
    译文：一个类与另一个类之间的依赖性，应该依赖于尽可能小的接口。
    理解：不要对外暴露没有实际意义的接口。也就是说，接口是给别人调用的，那就不要去为难别人了，尽可能保证接口的实用性吧。她好，我也好。
    应用：当需要对外暴露接口时，需要再三斟酌，如果真的没有必要对外提供的，就删了吧。一旦您提供了，就意味着，您将来要多做一件事情，何苦要给自己找事做呢。

## 5.依赖倒置原则（Dependence Inversion Principle - DIP）

    原文：High level modules should not depends upon low level modules. Both should depend upon abstractions. Abstractions should not depend upon details. Details should depend upon abstractions.
    译文：高层模块不应该依赖于低层模块，它们都应该依赖于抽象。抽象不应该依赖于细节，细节应该依赖于抽象。
    理解：应该面向接口编程，不应该面向实现类编程。面向实现类编程，相当于就是论事，那是正向依赖（正常人思维）；面向接口编程，相当于通过事物表象来看本质，那是反向依赖，即依赖倒置（程序员思维）。
    应用：并不是说，所有的类都要有一个对应的接口，而是说，如果有接口，那就尽量使用接口来编程吧。


> 其他常见原则:

## 6.最少知识原则（Least Knowledge Principle - LKP）

尽量减少对象之间的交互，从而减小类之间的耦合。简言之，一定要做到：低耦合，高内聚。

## 7.合成复用原则 （The Composite Reuse Principle - CRP）

尽量使用对象组合，而不是通过继承来达到复用的目的

## 8. 共同封闭原则 （The Common Closure Principle - CCP）

一起修改的类，应该组合在一起（同一个包里）。如果必须修改应用程序里的代码，我们希望所有的修改都发生在一个包里（修改关闭），而不是遍布在很多包里。

## 9. 稳定抽象原则 （The Stable Abstractions Principle -SAP）

最稳定的包应该是最抽象的包，不稳定的包应该是具体的包，即包的抽象程度跟它的稳定性成正比。

## 10. 稳定依赖原则 （The Stable Dependencies Principle - SDP）

包之间的依赖关系都应该是稳定方向依赖的，包要依赖的包要比自己更具有稳定性。

## 参考文献

[十年阿里java架构师的六大设计原则和项目经验](https://juejin.im/post/5a51ef536fb9a01c9a2692b2)

[面向对象设计的SOLID原则](http://www.cnblogs.com/shanyou/archive/2009/09/21/1570716.html)