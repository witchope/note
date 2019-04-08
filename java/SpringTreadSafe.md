# Spring 中的线程安全

Spring 作为一个 IOC 容器，帮助我们解耦应用并管理所需要的 `Bean` 。但是，Spring 本身并没有一个确定的方式保证线程安全，需要开发者通过编码的方式解决。

Spring 中的每个 `Bean` 都有一个 scope 的属性来表示 `Bean` 的作用域：

- singleton：默认的 scope ，单例复用模式，该 Bean 生命周期与 IOC 容器一致，会在第一次注入时创建；
- prototype：每次注入都会创建一个新的对象；
- request：每个 HTTP 请求会创建一个复用单例对象；
- session：每个 session 生命周期会创建一个复用单例对象；
- application：在 ServletContext 生命周期中复用同一个单例对象；
- websocket：在 WebSocket 生命周期中复用同一个单例对象；

其实我们交给 Spring 容器管理的大部分 Bean 都是些无状态的对象，因此并不存在线程安全问题（只要是无状态对象，就是线程安全的，单例相对多例的优势就是少了不断创建对象和 GC 的开销）。

IOC 容器中常见的无状态对象有：VO、DTO、DO、Dao、Service、Controller 等。

同上分析，Spring 根本没有对 Bean 的线程安全作出保证。对于每个 Bean 的线程安全，根本原因在于每个 Bean 的自身设计。尽量不要在 Bean 中声明带有任何状态的实例变量和类变量，如果实在有必要，可以通过 ThreadLocal 变量私有；如果变量需要在多个线程之间共享可以通过 Synchronized、Lock、CAS 等方式实现。

## 参考：

[会创建一个复用单例对象](http://www.importnew.com/27440.html)