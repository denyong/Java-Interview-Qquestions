# SpringBoot

## 循环依赖

​	问题描述：在两个类中，互相引用了对方的实例导致产生循环依赖的现象。
​第一种：构造方法循环依赖。

第二种：set方法注入
第三种：setter方式原型，prototype

```
	@Component
	public class A {
		  @Autowired
		  private B b;
		  public A (B b) {
		    System.out.println("A init1:" + b);

	  }
	  public void teach () {
	    System.out.println("teach:");
	    b.learn();
	  }
	}
	@Component
	public class B {
	  @Autowired
	  private A a;
	  public B (A a) {
	    System.out.println("B init1:" + a);
	  }
	  public void learn () {
	    System.out.println("B learn");
	  }
	}
```

## 注解

**@PostConstruct**

> 1. 修饰非静态的void方法
>
> 2. 在构造函数之后执行，init（）方法之前执行
>
> 3. 在加载servlet的时候运行且只运行一次
>
> 4. 执行顺序：Constructor(构造方法) -> @Autowired(依赖注入) -> @PostConstruct(注释的方法)

**@Qualifier**

> 1. 在@Autowire自动注入时，加上@Qualifier("test")可以注明注入哪个对象
> 2. 先声明后使用，相当于多个实现起多个不同的名字，注入时候告诉我你要注入哪个

**@Primary**

优先方案，被注解的实现，优先被注入



