# Spring循环依赖

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





