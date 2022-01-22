Spring 5 核心原理

## 第一章 软件架构设计原则

1. ### 开闭原则

   开闭原则是指一个软件实体（类、模块）应该对扩展开放，对修改关闭。它强调的是用抽象来构建框架，用实现扩展细节，以提高软件系统的可复用性及可维护性。

   **开闭原则的核心思想是面向抽象编程。**

2. ### 依赖倒置原则

   依赖倒置原则是指在设计代码结构时，高层模块不应该依赖低层模块，二者都应该依赖抽象。抽象不应该依赖实现细节，实现细节应该依赖抽象。通过依赖倒置，可以减少类与类之间的耦合性，提高系统的稳定性。

   **以抽象为基准比以细节为基准搭建起来的架构要稳定，因此需要在拿到需求之后要面向接口编程，先顶层再细节的设计代码结构。**

3. ### 单一职责原则

   单一职责原则是指一个类或者一个模块只负责做一件事情。在代码层的表现就是不要存在多个导致类变更的原因。

   **一个模块、类、接口或方法只负责干一件事。**

4. ### 接口隔离原则

   接口隔离原则指的是用多个专门的接口，而不是使用单一的总接口，客户端不应该依赖它不需要的接口。这个原则指导我们在进行接口设计时应当注意以下几点：

   + 一个类对另一个类的依赖应该建立在最小的接口的基础上；
   + 建立单一的接口，不要建立庞大臃肿的接口；
   + 尽量细化接口，接口中的方法尽量少（适中）。

5. ### 迪米特原则

   迪米特原则是指一个对象应该对其他对象保持最少的了解，又叫最少知道原则，尽量降低类与类之间的耦合度。

   **迪米特原则主要强调：只和朋友交流，不和陌生人说话。出现在成员变量、方法输入、输出参数中的类都可以称为朋友类，而出现在方法体内部的类不属于朋友类。**

6. ### 里式替换原则

   里式替换原则是指如果对每一个类型为T1的对象O1，都有类型为T2的对象O2，使得以T1定义的所有程序P在所有的对象O1都替换为O2时，程序P的行为没有发生改变，那么类型T2是类型T1的子类型。

   简单理解一下为：一个软件实体，如果适用于一个父类，那么一定适用于其子类，所有引用父类的地方必须能够透明的使用其子类对象，**子类对象能够替换父类对象，而程序逻辑不变。引申为：子类可以扩展父类的功能，但是不能改变父类的原有功能。**

   + 子类可以实现父类的抽象方法，但是不能覆盖父类的非抽象方法；
   + 子类可以增加自己特有的方法；
   + 当子类的方法重载父类的方法时，方法的前置条件（输入参数）要比父类方法更宽松；
   + 当子类方法实现父类方法时，方法的后置条件（输出/返回值）要比父类更严格或者与父类一样。

7. ### 合成复用原则

   合成复用原则指的是尽量使用对象组合而不是继承关系达到软件复用的目的。

## 第二章 Spring中常用的设计模式

使用设计模式的好处：

+ 写出更优雅的代码；
+ 更好的重构项目

Spring中常用的设计模式：

| 设计模式名称 | 举例                  |
| ------------ | --------------------- |
| 工厂模式     | BeanFactory           |
| 装饰器模式   | BeanWrapper           |
| 代理模式     | AOP Proxy             |
| 委派模式     | DispatcherServlet     |
| 策略模式     | HandlerMapping        |
| 适配器模式   | HandlerAdapter        |
| 模板方法模式 | JdbcTemplate          |
| 观察者模式   | ContextLoaderListener |
|  | |
1. ### 简单工厂模式

   简单工厂模式是指由一个工厂类决定创建哪一种产品类的实例。

   简单工厂模式适用于工厂类负责创建的对象较少的场景，且客户端只需要传入工厂类的参数，对于如何创建对象不关心。

   ```java
   //抽象产品类
   interface IProduct {
    
   }
   
   class ProductA implements IProduct {
    
   }
    
   class ProductB implements IProduct {
    
   }
   //简单工厂类
   class InstanceFactory {
       public static IProduct getInstance(String type) {
           if (type.equals("A")) {
               return new ProductA();
           }
           if (type.equals("B")) {
               return new ProductB();
           }
           return null;
       }
   }
   
   //改进版本的工厂类
   class InstanceFactory_new {
       public static IProduct getInstance(Class<? extends IProduct> clazz) throws IllegalAccessException, InstantiationException {
           return clazz.newInstance();
   }
   //客户端
   public class Factory {
       public static void main(String[] args) throws InstantiationException, IllegalAccessException {
   //        IProduct productA = InstanceFactory.getInstance("A");
   //        IProduct productB = InstanceFactory.getInstance("B");
           IProduct productA = InstanceFactory_new.getInstance(ProductA.class);
           IProduct productB = InstanceFactory_new.getInstance(ProductB.class);
           System.out.println(productA);
       }
   }
   ```

   JDK中的简单工厂模式：`Calender.getInstance()`

   

2. ### 工厂方法模式

   工厂方法模式是指定义一个创建对象的接口，但让实现这个接口的类来决定实例化哪一个类，工厂方法模式让类的实例化推迟到子类中进行。在工厂方法模式中，用户只需要关注所需产品对应的工厂，无需关心实现细节，而且加入新的产品时符合开闭原则。

   **工厂方法模式主要解决产品扩展的问题。**在简单工厂模式中，随着产品链的丰富，如果每个产品的创建逻辑有区别，则工厂的职责会越来越多，有点像万能工厂，不便于维护。

   

   根据单一职责原则，我们将职责进行继续划分，专人干专事。ProductA由ProductA的工厂创建，ProductB由ProductB的工厂创建，对工厂本身我们也做一个抽象。

   ```java
   public interface IProduct {
   }
   
   public class ProductA implements  IProduct {
   }
   
   public class ProductB implements  IProduct {
   }
   
   public interface IFactory {
   
       IProduct createProduct();
   }
   
   public class ProductAFactory implements IFactory {
       @Override
       public IProduct createProduct() {
           return new ProductA();
       }
   }
   
   public class ProductBFactory implements IFactory {
       @Override
       public IProduct createProduct() {
           System.out.println("crate product B");
           return new ProductB();
       }
   }
   
   public class FactoryMethodTest {
   
       public static void main(String[] args) {
           IProduct productA = new ProductAFactory().createProduct();
           System.out.println(productA);
   
           IProduct productB = new ProductBFactory().createProduct();
           System.out.println(productB);
       }
   }
   ```

   工厂方法模式的优点：

   1. **良好的封装性，代码结构清晰**。如果一个调用者需要一个具体的产品对象，只需要知道这个产品的工厂是谁就行了，不用知道创建对象的具体过程。

   2. **扩展性好**。在增加产品类的情况下，只需要扩展一个工厂类就可以“拥抱变化”。

   3. **屏蔽产品类**。这一点很重要，产品类的具体实现如何变化，调用者都不需要关心，它只需要关心产品的接口，只要接口不变，系统中的上层模块就不需要发生变化。因为产品类的实例化工作是由工厂类负责的，一个产品对象具体由哪一个产品类生成是由工厂类决定的。

   4. **工厂模式是典型的解耦框架**。高层模块只需要知道产品的抽象类，其他的实现类都不需要关心。

3. ### 抽象工厂模式

   抽象工厂是指提供一个创建一系列相关或者相互依赖的对象的接口，无需指定它们的具体类。客户端不依赖于产品类实例如何被创建、如何被实现等细节，强调的是一系列相关的产品对象（同属于一个产品族）一起使用创建对象需要大量重复的代码。需要提供一个产品类的库，所有的产品以同样的接口出现，从而使客户端不依赖于具体的实现。

   ```java
   public class Test {
       public static void main(String[] args) {
           ComputerFactory factory = new AsusComputerFactory();
   //    ComputerFactory factory = new LevonoComputerFactory();
           Computer computer = factory.createComputer();
           Mouse mouse = factory.createMouse();
           Keyboard keyboard = factory.createKeyboard();
       }
   }
    
   abstract class Mouse {
    
   }
    
   abstract class Computer {
    
   }
    
   abstract class Keyboard {
    
   }
    
   abstract class ComputerFactory {
       public abstract Computer createComputer();
    
       public abstract Mouse createMouse();
    
       public abstract Keyboard createKeyboard();
   }
    
   class LevonoComputerFactory extends ComputerFactory {
    
       public Computer createComputer() {
           return new LevonoComputer();
       }
    
       public Mouse createMouse() {
           return new LevonoMouse();
       }
    
       public Keyboard createKeyboard() {
           return new LevonoKeyboard();
       }
   }
    
   class AsusComputerFactory extends ComputerFactory {
    
       public Computer createComputer() {
           return new AsusComputer();
       }
    
       public Mouse createMouse() {
           return new AsusMouse();
       }
    
       public Keyboard createKeyboard() {
           return new AsusKeyboard();
       }
   }
    
   class LevonoMouse extends Mouse {
    
   }
    
   class AsusMouse extends Mouse {
    
   }
    
   class LevonoComputer extends Computer {
    
   }
    
   class AsusComputer extends Computer {
    
   }
    
   class LevonoKeyboard extends Keyboard {
    
   }
    
   class AsusKeyboard extends Keyboard {
    
   }
   ```

   **抽象工厂模式的缺点:**
   假设现在需求变更，不仅需要生产电脑、键盘和鼠标，还需要生产鼠标垫，那么就需要再扩展一个产品：鼠标垫。相应的也就需要增加联想和华硕品牌的鼠标垫类，并修改抽象工厂类：ComputerFactory，进而LevonoComputerFactory和AsusComputerFactory也需要改变，违反了修改关闭原则。这也是抽象工厂模式最大的缺点：**产品扩展难**。

   **抽象工厂模式优点**:
   1.**封装性**，每个产品的实现类不是高层模块需要关心的，它仅仅关心接口，只需要知道工厂类是谁就能创建出需要的对象。

   2.**产品族内的约束为非公开状态**。如生产一台电脑就有对应的一个键盘和一个鼠标。

   **抽象工厂模式使用场景:**
   一个对象族（或者一组没有任何关系的对象）都有相同的约束，则可以使用抽象工厂模式。如一个文本编辑器和一个图片编辑器，都是软件实体，但linux和windows下的文本编辑器和图片编辑器都是同样的功能和界面，但是实现的代码是不一样的，这就有了共同的约束条件：操作系统类型。于是就可以使用抽象工厂模式产生不同操作系统下的文本编辑器和图片编辑器。

4. ### 单例模式

   单例模式是指确保一个类在任何情况下都绝对只有一个实例，并提供一个全局访问点。

   实现单例模式需要考虑以下几个关键点：

   + 私有化构造函数
   + 提供全局访问点
   + 线程安全
   + 性能（懒加载特性）

   常见的单例实现方式：

   ```java
   public class SingleTon01 {
   
       /**
        * 饿汉式单例模式，线程安全，不具备懒加载特性
        */
       private static SingleTon01 instance = new SingleTon01();
   
       private SingleTon01() {
       }
   
       public static SingleTon01 getInstance() {
           return instance;
       }
   }
   public class SingleTon02 {
   
       /**
        * 懒汉式单例，懒加载，线程不安全
        */
       private static SingleTon02 instance = null;
   
       private SingleTon02() {
       }
   
       public static SingleTon02 getInstance() {
           if (null == instance) {
               instance = new SingleTon02();
           }
           return instance;
       }
   }
   public class SingleTon03 {
   
       /**
        * 懒汉式单例，懒加载，线程安全,并发访问性能差
        */
       private static SingleTon03 instance = null;
   
       private SingleTon03() {
       }
   
       public static synchronized SingleTon03 getInstance() {
           if (null == instance) {
               instance = new SingleTon03();
           }
           return instance;
       }
   }
   public class SingleTon04 {
   
       /**
        * 懒汉式单例，懒加载，线程安全,并发访问性能好
        */
       private static volatile SingleTon04 instance = null;
   
       private SingleTon04() {
   
       }
   
       public static SingleTon04 getInstance() {
           if (null == instance) {
               synchronized (SingleTon04.class) {
                   if (null == instance) {
                       instance = new SingleTon04();
                   }
               }
           }
           return instance;
       }
   }
   public class SingleTon05 {
   
       /**
        * 懒汉式单例，懒加载，线程安全,并发访问性能好
        */
       private SingleTon05() {
       }
   
       public static SingleTon05 getInstance() {
           return SingleTonHolder.INSTANCE;
       }
   
       private static class SingleTonHolder {
           private static SingleTon05 INSTANCE = new SingleTon05();
       }
   }
   public enum SingleTon06 {
   
       INSTANCE;
   
       /**
        * 线程安全，非懒加载特性，并发访问性能好
        *
        * @return
        */
       public static SingleTon06 getInstance() {
           return INSTANCE;
       }
   }
   ```

   破坏单例模式：

   ```java
   //利用反射破坏单例
   public class SingleTon {
   	public static void main(String[] args) throws ClassNotFoundException, InstantiationException,
   			IllegalAccessException, IllegalArgumentException, InvocationTargetException {
   		// for(int i=0;i<1000;i++) {
   		// new Thread(new ThreadTest()).start();;
   		// }
    
   		System.out.println(SingleTon05.getInstance());
   		Class clzz = Class.forName("com.singleton.SingleTon05");
   		System.out.println(clzz);
   		Constructor<SingleTon>[] constructors = clzz.getDeclaredConstructors();
   		constructors[0].setAccessible(true);
   		System.out.println(constructors[0].newInstance());
   	}
   }
   
   //防止单例模式破解：在构造器中判定instance是否为null，为null则抛出异常
   class SingleTon05 {
   	private SingleTon05() {
   		if (Helper.instance != null) {
   			throw new RuntimeException("非法访问构造器。。。。");
   		}
   	}
    
   	public static SingleTon05 getInstance() {
   		return Helper.instance;
   	}
    
   	static class Helper {
   		private static final SingleTon05 instance = new SingleTon05();
   	}
   }
   
   //利用序列化反序列化破坏单例 
   ......
   ```

5. ### 原型模式

   原型模式是指原型实例指定创建对象的种类，并且通过复制这些原型创建对象。

   **原型模式使用场景：**

   + 类初始化消耗资源较多
   + 使用new创建一个对象需要非常繁琐的过程（数据准备、访问权限等）
   + 构造函数比较复杂
   + 在循环体中生成大量对象

   **浅克隆：**浅克隆只是完整复制了值类型数据，没有复制引用对象。换言之，所有引用对象的引用仍然指向原来的对象。

   **深克隆：**完整复制值类型数据和引用类型数据。

   ```java
   class Mail implements Cloneable {
       //收件人
       private String receiver;
       //邮件主题
       private String subject;
       //称谓
       private String appellation;
       //邮件内容
       private String context;
       //邮件尾部
       private String tail;
    
       public Mail(MailTemplate template) {
           this.context = template.getMailContext();
           this.subject = template.getMailSubject();
       }
    
       // getter setter方法
    
       @Override
       protected Mail clone() throws CloneNotSupportedException {
           Mail mail = (Mail) super.clone();
           return mail;
       }
    
       @Override
       public String toString() {
           return "Mail{" +
                   "receiver='" + receiver + '\'' +
                   ", subject='" + subject + '\'' +
                   ", appellation='" + appellation + '\'' +
                   ", context='" + context + '\'' +
                   ", tail='" + tail + '\'' +
                   '}';
       }
   }
    public static void main(String[] args) throws CloneNotSupportedException {
           //定义邮件模板
           Mail mail = new Mail(new MailTemplate());
           mail.setTail("xxx公司版权所有");
           for (int i = 0; i < MAXCOUNT; i++) {
               Mail cloneMail = mail.clone();
               System.out.println(cloneMail);
               cloneMail.setAppellation(getRandString(5) + " 先生（女士）");
               cloneMail.setReceiver(getRandString(5) + "@" + getRandString(3) + ".com");
               sendMail(cloneMail);
           }
       }
   }
   ```

6. ###  代理模式

   代理模式是指定义一个代理对象，以控制对目标对象的访问。代理对象在客户端和目标对象之间起着中介的作用。使用代理模式的目的主要有两个：

   + 一是保护目标对象；
   + 二是增强目标对象

   ```java
   interface Subject {
       void request(Object param);
   }
   
   class  Proxy implements Subject {
   
      private Subject targetObject;
   
      public Proxy(Subject targetObject){
          this.targetObject = targetObject;
      }
       @Override
       public void request(Object param) {
           System.out.println("Proxy request");
           before(param);
           targetObject.request(param);
           after(param);
       }
   
       private void before(Object param){
       }
   
       private void after(Object param){
       }
   }
   
   class RealSubject implements Subject {
   
       @Override
       public void request(Object param) {
           System.out.println("RealSubject request");
       }
   }
   public class StaticProxy {
   
       public static void main(String[] args) {
           RealSubject subject = new RealSubject();
   
           Proxy proxy = new Proxy(subject);
           proxy.request(null);
       }
   }
   ```

   

3. 

