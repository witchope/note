
# Spring 构造器注入

> Spring Constructor Injection

```java
@Controller
public class FooController {
  
  private final FooService fooService;
  
  @Autowired
  public FooController(FooService fooService) {
      this.fooService = fooService;
  }
}
```

## <font color=#0099ff>构造器注入优势：</font>

- 保证注入依赖不可变（ `final` 关键字）
- 保证依赖不为空 （容器会在注入是进行非 `null` 检查）
- 保证客户端（调用）代码的时候是完全初始化状态
- 避免循环依赖 （A, B 相互依赖）
- 提升注入类复用性

[原文链接][link]

[link]: http://www.cnblogs.com/joemsu/p/7688307.html
