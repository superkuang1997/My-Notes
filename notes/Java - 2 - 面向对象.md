# Java面向对象🔥

## 面向对象的概念

- 面向过程：将问题分解成步骤，然后按照步骤实现函数，执行时依次调用函数。数据和对数据的操作是分离的。

- 面向对象：将问题分解成对象，描述事物在解决问题的步骤中的行为。对象与属性和行为是关联的。

面向过程的优点是性能比面向对象高，不需要面向对象的实例化；缺点是不容易维护、复用和扩展。

面向对象的优点是具有封装、继承、多态的特性，因而容易维护、复用和扩展，可以设计出低耦合的系统；缺点是由于需要实例化对象，因此性能比面向过程低。



## 对象和类

对象是现实世界中可以明确标识的实体，对象有自己独有的状态和行为。对象的状态由数据域的集合构成，对象的行为由方法的集合构成。

类是定义同一类型对象的结构，是对具有相同特征的对象的抽象。类是一个模板，用来定义对象的数据域和方法。可以从一个类创建多个对象，创建对象称为实例化。



## 对象的实例化

对象是客观存在的事物，可以说任何客观存在的都是可以成为对象

类就是具备某些共同特征的实体的集合，它是一种抽象的数据类型，它是对所具有相同特征实体的抽象。

将现实世界的事物抽象之后创建了类，程序运行时产生类的对象

```java
Person kiana = new Person();  // 创建一个对象
```

1. 调用构造方法，创建一个新的 Person 对象，并将该对象的地址赋予变量 kiana

2. kiana 表示一个变量，变量指向了对象所在的堆的地址，是指向当前对象的一个引用
3. 这个 Person 对象就是 Person 类的一个实例
4. 实例就是通过类来创建的具体某个对象，但实例和对象并不是完全相同的概念
5. 类在实例化后会生成一个对象，我们可以说这个对象是类的一个实例，实例这个概念体现了类和对象之间关系，因此实例和对象的内涵并不完全相同，基于类创建对象的过程也叫做类的实例化



## 类中声明变量的类型

### 成员变量

类的属性（field）就是类中定义的变量，也被称作成员变量，不区分 `static`

可以在声明成员变量的同时对其进行初始化，如果声明成员变量时没有对其初始化，则系统会使用默认值初始化成员变量。

#### 静态变量

类变量：声明了 `static` 的成员变量叫做类变量，也叫做静态变量、全局变量。

- 外部调用：从外部调用静态的类成员时，可以通过类名调用，也可以通过对象名调用。

- 内部访问：静态方法只能访问静态的类成员，不能访问实例的类成员。




静态变量在类加载的时候被分配，在该类对应的类加载器被卸载时被丢弃。



#### 实例变量

实例变量：没有声明 `static` 的成员变量叫实例变量，

- 外部调用：从外部调用实例的类成员，则只能通过对象名调用。

- 内部访问：实例方法既可以访问实例的类成员，也可以访问静态的类成员。实例的类成员是依赖于具体对象的，而静态方法不依赖于任何实例。



### 局部变量

局部变量是在方法中定义的变量或是方法的参数

```java
public class Person {
	  public void sleep() {
		    int time = 8;  // 局部变量
	  }
}
```





## 代码块

### 非静态初始化块

直接在类中定义且没有加 static 关键字的代码块称为非静态初始化块，也叫构造代码块。

非静态初始化块的作用是给对象进行初始化，对象一建立就运行构造初始化块了，而且优先于构造函数执行。



### 静态初始化块

构造方法用于对象的初始化，静态初始化块则用于类的初始化操作。

类初始化时，先执行父类的静态初始化块，再执行子类的静态初始化块。

假设有如下继承关系：`B → A → Object`

则执行静态初始化块的顺序为：`Object → A → B` 





## 双大括号初始化

双大括号初始化（double brace initialization）

第一层花括号：定义了一个继承于 `ArrayList` 的匿名内部类

第二层花括号：实际上是这个匿名内部类的非静态初始化块

通过 new 得到这个 `ArrayList` 的子类的实例并「向上转型」为 `ArrayList` 的引用

```JAVA
@Test
public void test() {
    List<String> list = new ArrayList<String>(){{
        add("a");
        add("b");
        add("c");
    }};
    list.add("d");
}
```







# 关键字🔥

## static关键字

在类中，用 *static* 声明的成员变量为静态变量，也称为类变量，类变量的生命周期和类相同，在整个应用程序执行期间都有效。它有如下特点；

1. 静态变量是该类的公用变量，属于类，被该类的所有实例共享，在类被载入时被显式初始化。
2. 对于该类的所有对象来说，静态变量和静态方法被该类的所有对象共享，也就是说所有的实例对象可以使用静态变量、调用静态方法。
3. 由于静态变量和静态方法在程序调用时被加载到方法区，堆中的对象可以访问方法区，但方法中的静态方法不可以访问堆中的对象（因为对象是否存在也不一定），所以在静态方法中不可直接访问非静态的属性和方法。

总结：*static* 修饰的成员变量和方法，从属于类。未经 *static* 修饰的成员变量和方法，从属于对象。



## super关键字

*super* 是父类对象的直接引用。可以通过 *super* 访问父类中被子类重写或覆盖的方法或属性。

使用 *super* 调用父类的方法，语句没有位置限制，可以在子类中随便调用。

构造方法中的 `super()`

1. 构造方法的名字是与类名一致的。所以子类是无法继承父类构造方法的。
2. 构造方法的作用是初始化成员变量，所以子类的初始化过程中，必须先执行父类的初始化动作。子类的构造方法中默认有 `super() `，表示调用父类的构造方法，父类成员变量初始化后，才可以给子类使用。

若是构造方法的第一行代码没有显式的调用 `super()` 或者 `this(...)`，那么 Java 默认都会调用`super()`，含义是调用父类的无参数构造方法。

```java
class Father{
}

class Son extends Father{
    public Son() {
        super();  // 显示调用super()
    }
}
```



## this关键字

*this* 代表所在类的当前对象的引用（地址值），即对象自己的引用。

创建一个对象分为如下四步：

1. 分配对象空间，并将对象成员变量初始化为 0 或 null

2. 执行属性值的显式初始化
3. 执行构造方法
4. 返回对象的地址给相关的变量

this 的本质是 “创建好的对象的地址” ，由于在构造方法调用前，对象已经创建，因此，在构造方法中也可以使用 *this* 代表当前对象。但 this 不能用于静态方法中，因为调用静态方法前不一定创建了对象。



## final关键字

通过方法的重写可以使子类在父类的基础上改写父类内容，但是随意的改写父类的内容显然是不合适的。为了避免这种随意改写的情况，Java 提供了 *final* 关键字，用于修饰不可改变的内容。

final： 不可改变。可以用于修饰类、方法和变量。

1. 修饰变量：被修饰的变量不可更改

   被 final 修饰的常量名称，一般都有书写规范，所有字母都大写。

   ```java
   // 修饰基本数据类型
   final int a = 10;
   
   // 修饰引用数据类型
   final User u = new User();
   u = new User();      // 报错，因为指向了新的对象，地址改变
   u.setName("kiana");  // 可以修改，因为变量指向的地址不变
   ```

   

2. 修饰方法：被修饰的方法不可以被子类重写，但是可以被重载

   ```java
   修饰符 final 返回值类型 方法名(参数列表){
   	// 方法体
   }
   ```

   

3. 修饰类：被修饰的类不可以被继承

   重写被  final 修饰的方法，编译时就会报错。

   ```java
   final class 类名 {
    // 实现
   }
   ```





# 封装🔥

## 概念

封装（encapsulation）是指类对外隐藏某些属性和方法，对内公开某些属性和方法。

面向对象编程语言是对客观世界的模拟，客观世界里成员变量都是隐藏在对象内部的，外界无法直接操作和修改。 封装可以被认为是一个保护屏障，防止该类的代码和数据被其他类随意访问。要访问该类的数据，必须通过指定的方式。适当的封装可以让代码更容易理解与维护，也加强了代码的安全性。 

原则：将属性隐藏起来，若需要访问某个属性，提供公共方法对其访问。



## 访问修饰符

*public > protected > (default) > private*

1. `private ` 表示私有，只有 *自身的类* 能访问

2. `default ` 表示没有修饰符修饰，只有 *同一个包的类* 能访问（必须同一个包路径）

3. `protected` 表示可以被 *同一个包的类以及其他包中的子类* 访问

4. `public` 表示可以被该项目的 *所有包中的所有类* 访问

|  修饰符   | 同一个类 | 同一个包中 | 不同包子类 | 所有类 |
| :-------: | :------: | :--------: | :--------: | :----: |
|  private  |    √     |            |            |        |
|  default  |    √     |     √      |            |        |
| protected |    √     |     √      |     √      |        |
|  public   |    √     |     √      |     √      |   √    |







## 构造方法

构造方法（constructor）也被称为构造器，用于对象的初始化。

- 通过new关键字调用

- 构造器的方法名必须和类名一致

- 构造器虽然有返回值，但是不能定义返回值类型，因为构造器的返回值的类型必须是本类，所以不能在构造器里使用return返回某个值。

- 如果没有定义构造方法，则编译器会自动定义一个无参的构造函数。

- 如果我们提供了构造方法，系统将不再提供无参数构造方法。 

- 构造方法是可以重载的，既可以定义参数，也可以不定义参数

  ```java
  public class Student {
      // 无参构造方法
      public Student() {
      }
      
      // 有参构造方法
      public Student(String name, int age) {
          this.name = name;
          this.age = age;
      }
  }
  ```



# 继承🔥

## 概念

多个类中存在相同属性和行为时，将这些内容抽取到单独一个类中，那么多个类无需再定义这些属性和行为，只要继承那一个类即可。

**继承（inheritance）**就是子类继承父类的属性和行为， 使得子类对象具有与父类相同的属性、相同的行为。子类可以直接访问父类中的**非私有**的属性和行为。

Java中只有单继承，没有像C++那样的多继承。多继承会引起混乱，使得继承链过于复杂，系统难于维护。尽管Java中类没有多继承，但接口有多继承，因此也可以实现多继承的效果。

如果定义一个类时，没有调用extends，则它的父类是：`java.lang.Object`



## 继承的格式

```java
class 子类 extends 父类 {
	...
}
```



## Object

### toString

打印stu时会显示stu所指对象的地址，实际上是调用了对象的 `toString` 方法，这个方法是 `Object` 定义的，如果没有重写 `toString` 方法，则打印时默认调用父类的方法。

```java
Student stu = new Student();
System.out.println(stu);  // cn.kzq.oo.Student@1540e19d
System.out.println(stu.toString());  // // cn.kzq.oo.Student@1540e19d
```



```java
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```





## 方法的重写

子类通过重写父类的方法，可以用自身的行为替换父类的行为。

方法的重写需要符合下面的三个要点：

1. 方法名、形参列表相同。
2. 返回值类型和声明异常类型，子类小于等于父类。
3. 访问权限，子类大于等于父类。





## 抽象方法

使用 `abstract` 修饰的方法，没有方法体，只有声明。定义的是一种“规范”，就是告诉子类必须要给抽象方法提供具体的实现。

定义格式：

```java
修饰符 abstract 返回值类型 方法名 (参数列表)；
```

代码举例：

```java
public abstract void run()；
```



## 抽象类

抽象类：包含抽象方法的类就是抽象类

通过 `abstract` 方法定义规范，然后要求子类必须定义具体实现。

抽象类的意义在于给子类提供设计模板，子类必须实现相关的抽象方法。

定义格式：

```java
public abstract class Animal {
	public abstract void run()；
}
```

抽象类的使用要点

1. 有抽象方法的类只能定义成抽象类

2. 抽象类不能实例化，即不能用new来实例化抽象类。

3. 抽象类可以包含属性、方法、构造方法。但是构造方法不能用来new实例，只能用来被子类调用。

4. 抽象类只能用来被继承。

5. 抽象方法必须被子类实现。



# 多态🔥

## 概念

多态（polymorphism）指的是同一个方法调用，由于对象不同可能会有不同的行为，同一个方法，具体实现会完全不同。

多态分为编译时多态和运行时多态：

- 编译时多态主要指方法的重载
- 运行时多态指程序中定义的对象引用所指向的具体类型在运行期间才确定



## 多态的实现

- 多态是方法的多态，不是属性的多态（多态与属性无关）。

- 多态的存在要有3个必要条件：

   1. 继承
   2. 方法重写
   3. 父类引用指向子类对象（向上转型）

- 父类引用指向子类对象后，用该父类引用调用子类重写的方法，此时多态就出现了。

```java
public class TestPolymorphism {
    public static void main(String[] args) {
        Animal a = new Animal();
        Cat c = new Cat();
        Dog d = new Dog();    
      	// 调用静态方法 传入不同对象
        animalCry(a);  // 叫了一声
        animalCry(c);  // 喵喵喵
        animalCry(d);  // 汪汪汪
            
    }
    
    // 测试类中定义一个静态方法，参数为实现类的对象
    public static void animalCry(Animal a) {
        a.shout();
    }
}

// 动物是父类，猫和狗是子类
class Animal {
    public void shout() {
        System.out.println("叫了一声");
    }
}
class Cat extends Animal {
    @Override
    public void shout() {
        System.out.println("喵喵喵");
    }
  
    public void catchMouse(){
        System.out.println("抓老鼠");
    }
}
class Dog extends Animal {
    @Override
    public void shout() {
        System.out.println("汪汪汪");
    }
  
    public void seeDoor() {
        System.out.println("看门");
    }
}
```



## 引用类型转换

### 向上转型

- **向上转型**：多态本身是子类类型向父类类型向上转换的过程，这个过程是默认的。

父类引用指向子类对象，我们称这个过程为**向上转型**，属于自动类型转换。

```java
Animal a = new Dog();  // 向上转型  
animalCry(a);  // ✔ 可以调用父类的方法 汪汪汪
a.shoot();     // ✔ 可以调用子类重写的方法
a.seeDoor();   // ✖ 无法调用子类的特有的方法 
```

向上转型时,父类只能调用 **父类方法** 或者 **子类重写的方法**，而子类中的特有方法则是无法调用的.

### 向下转型

- **向下转型**：父类类型向子类类型向下转换的过程，这个过程是强制的。

向下转型是通过父类强制转换为子类,从而来 **调用子类特有的方法**

当使用多态方式调用方法时，首先检查父类中是否有该方法，如果没有，则编译错误。也就是说，不能调用子类拥有，所以，想要调用子类特有的方法，必须做向下转型。

```java
Dog d = (Dog)a;   // 向下转型 将Animal转为Dog
d.seeDoor();      // 看门 （可以调用子类的方法）  
```



## 无法多态的情况



```java
@Getter
@Setter
public class Animal {
    String type = "animal";

    private static void jump() {
        System.out.println("private static void animal jump");
    }
}

@Getter
@Setter
public class Dog extends Animal {
    String type = "dog";

    private static void jump() {
        System.out.println("private static void jump!");
    }
}

public class polymorphicTest {
    public static void main(String[] args) {
        Animal animal = new Dog();
        Dog dog = new Dog();
        System.out.println(animal.type);
        System.out.println(dog.type);
        System.out.println(animal.getType());
        System.out.println(dog.getType());
    }
}

// 输出
animal
dog
dog
dog
```



## ClassCastException

编写以下代码:

```java
// 向上转型
Animal a = new Cat();
// 向下转型
Dog d = (Dog)a;
// 调用Dog类特有的方法
d.seeDoor();
```

这段代码可以通过编译，但是运行时，却报出了 `ClassCastException` ，类型转换异常，因为，已经创建了Cat类型对象，运行时，不能转换成Dog对象。这两个类型并没有任何继承关系，不符合类型转换的定义。

为了避免 `ClassCastException` 的发生，Java提供了 `instanceof` 关键字，给引用变量做类型的校验

```java
// 向上转型
Animal a = new Cat();

// 判断后向下转型，执行子类的特有方法
if (a instanceof Dog) {
    Dog d = (Dog) a;
    d.seeDoor();
} else {
    Cat c = (Cat) a;
    c.catchMouse();
}
```





# 接口🔥

## 概念

接口，是Java语言中一种引用类型，是方法的集合，如果说类的内部封装了成员变量、构造方法和成员方法，那么接口的内部主要就是封装了方法，包含抽象方法、默认方法、静态方法和私有方法。

```java
public interface 接口名称 {
    // 抽象方法
    public abstract void method01;
    
    // 默认方法
    public default void method02{
        // 执行语句
    };
    
    // 静态方法
    public static void method03{
        // 执行语句
    };
    
    // 私有方法
    public private void method04{
        // 执行语句
    };
}
```



## 定义格式

### 含有抽象方法 

抽象方法：使用 `abstract` 关键字修饰，可以省略，**没有方法体**。该方法供子类实现使用。

```java
public interface InterFaceName {
	public abstract void method();
}
```

### 含有默认方法和静态方法 

默认方法：使用` default` 修饰，不可省略，供子类调用或者子类重写。

静态方法：使用` static `修饰，供接口直接调用。

```java
public interface InterFaceName {
	public default void method01() {
    }
    public static void method02() { 
    }
}
```

### 含有私有方法和私有静态方法 

```java
public interface InterFaceName {
	private void method() {
}
```



### 定义常量

接口中只能定义常量

```java
int FLY_SEPPD = 180;
```

在接口中定义的常量等价于以下形式

```java
public static final int FLY_SEPPD = 180;
```



## 接口的实现

类与接口的关系为实现关系，即类实现接口，该类可以称为接口的实现类，也可以称为接口的子类。实现的动作类似继承，格式相仿，只是关键字不同，实现使用 `implements` 关键字。

非抽象子类实现接口：
1. 必须重写接口中所有抽象方法。
2. 继承了接口的默认方法，即可以直接调用，也可以重写。

实现格式：

```java
class 类名 implements 接口名 {
	// 重写接口中抽象方法 [必须]
	// 重写接口中默认方法 [可选]
}
```



## 接口的多继承

一个接口能继承另一个或者多个接口，这和类之间的继承相似。接口的继承使用 `extends` 关键字，子接口继承父接口的方法。如果父接口中的默认方法有重名的，那么子接口需要重写一次。

```java
interface A {
    public default void method() {
        System.out.println("AAA");
    }
}

interface B {
    public default void method() {
        System.out.println("BBB");
    }
}

interface C extends A, B {
    @Override
    default void method() {
        System.out.println("CCC");
    }
}
```



## 接口的多实现

Java的类只能单继承，但接口可以多继承，Java的类如果继承了**多继承的接口**，就起到了多继承类的作用。

```java
interface AA{
    void testA();
}
interface BB{
    void testB();
}
interface CC extends AA, BB{
    void testC();
}
```

Java类可以继承多继承的接口类，实现逻辑上的多继承

```java
// 接口CC继承了AA和BB
class DD implements CC{
    @Override
    public void testA() {
        // 执行语句
    }
    
    @Override
    public void testB() {
        // 执行语句
    }
    
    @Override
    public void testC() {
        // 执行语句
    }
}
```



# 内部类🔥

在 Java 中内部类主要分为成员内部类、匿名内部类、局部内部类。

类文件：`外部类$内部类.class`



## 成员内部类

成员内部类 ：定义在类中方法外的类。

- 内部类可以直接访问外部类的属性，包括私有属性。

- 外部类要访问内部类的成员，必须要建立内部类的对象。

```java
public class Outer {
	  public class Inner {

	  }
}
```

创建内部类对象格式：

```java
外部类名.内部类名 对象名 = new 外部类型().new 内部类型()；
```





## 静态内部类

如果要用 *static* 来修饰的类，那只能用来修饰内部类。

使用 *static* 修饰的内部类之后可以使用 “外部类.内部类” 实例化这个内部类的对象。

```java
public class Outer {
	  public static class Inner {

	  }
}
```

对于其他非静态内部类，则只能先实例化外部类，然后才能调用内部类。





## 匿名内部类

匿名内部类：是内部类的简化写法。它的本质是一个 「带具体实现的」「父类或者父接口的」「匿名的」 子类对象。

格式：

```java
new 父类名或者接口名(){
	// 方法重写
	@Override
	public void method() {
		// 执行语句
	}
};
```



## 局部内部类

局部内部类：定义在方法内部的，作用域只限于本方法，称为局部内部类。

```java
public class Outer {
    public void methodOuter() {
        // 局部内部类
        class Inner {
            int num = 10;  // 或者 final int num = 10;
            public void methodInner() {
                System.out.println(num);
           }
        }
    }
}
```



局部内部类，如果希望访问所在方法的局部变量，那么这个局部变量必须是 有效 *final* 的

解析：new 出来的对象在堆内存当中，局部变量从属于方法，在栈内存当中。方法运行结束之后，立刻出栈，局部变量就会立刻消失，但是 new 出来的对象会在堆当中持续存在，直到垃圾回收消失。

所以当`methodOuter` 方法运行结束后，局部变量 `num ` 就会消失，但局部内部类 `Inner` 仍然在堆中，所以如果想使用 `num`，就必须复制一份，这就要求 `num` 的值是不变的。



# 包机制🔥

包机制是Java中管理类的重要手段，利用包可以很容易对解决类重名的问题，也可以实现对类的有效管理。包对于类，相当于文件夹对于文件的作用。

```java
package cn.kzq.test  // 保证该语句是非注释语句的第一句
```



jdk中的主要包：

| 主要包    | 介绍                                                         |
| --------- | ------------------------------------------------------------ |
| java.lang | 包含一些 Java 语言的核心类，如 String、Math、Integer.System 和 Thread，提供常用功能 |
| java.awt  | 包含了构成抽象窗口工具集（abstract window toolkits ）的多个类，这些类被用来构建和管理应用程序的图形用户界面（GUI） |
| java.net  | 包含执行与网络相关的操作的类                                 |
| java.io   | 包含能提供多种输入/输出功能的类。                            |
| java.util | 包含一些实用工具类，如定义系统将性、使用与目期目历相关的函数。 |



## 静态导入

```java
import static java.lang.Math.PI  // 静态导入当前包的PI属性
import static java.lang.Math.*   // 静态导入当前包的所有属性
```





# FAQ🔥

## 抽象类和接口的区别？

1. 抽象类可以有构造方法，接口中不能有构造方法。
2. 抽象类中的抽象方法的访问类型可以是 public、protected ，接口中的抽象方法只能是 public
3. 抽象类在代码实现方面发挥作用，可以实现代码的重用；接口更多的是在系统架构设计方法发挥作用，主要用于定义模块之间的通信契约。



## 成员变量和局部变量的区别

- 在类中的位置不同
  - 成员变量：类中，方法外
  - 局部变量：方法中或者方法声明上（形式参数）
- 作用范围不一样
  - 成员变量：类中
  - 局部变量：方法中
- 初始化值的不同
  - 成员变量：有默认值
  - 局部变量：没有默认值。必须先定义，赋值，最后使用
- 在内存中的位置不同
  - 成员变量：堆内存
  - 局部变量：栈内存
- 生命周期不同
  - 成员变量：随着对象的创建而存在，随着对象的消失而消失
  - 局部变量：随着方法的调用而存在，随着方法的调用完毕而消失
