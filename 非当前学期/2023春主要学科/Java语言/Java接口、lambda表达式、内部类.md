# 接口
## 1.概念
### 符合某种特征的所有类的一组需求，当中包含一些功能函数，如果一个类符合某个接口，就可以用接口内的方法，我们可以把接口理解为一种合同，签署了这个合同的类，就一定要履行合同里面的所有内容
```java
Public interface Comparable{
	Int compareTo (Object other)
}
Class employee implement Comparable{
	Public int compareTo (Object otherobject){
		Employee other=(Employee) otherobject;
		Return double. Compare (salary, other. Salary);
	}    
 }
```

一些重要接口
Comparable 添加这个接口后，才能对这个类进行比较
Cloneable 添加这个接口后，对象具有使用 clone 方法的能力，即创建一个新对象，对象的内容一致并且不使用同一个内存（String 就是 Cloneable 的）
Iterator 添加这个接口后，对象存在一个迭代器
```java
Class Employee implements Comparable<Employee>{
	public int compareTo(Employee other){
		return Double.compare(salary,other.salary);
	}
}

```

## 3.接口的继承
```java
interface Role {
    public void greet();
    public void move();
    public int getSpeed();
}
interface Hero extends Role {
    public void attack();
}
```
## 4. 接口的实现

**在实现接口中的方法时，一定要用 public 修饰，不能省略

```java
class Zeus implements Hero {
    private final String name = "Zeus";
    public void attack() {
        System.out.println(name + ": Attack!");
    }

    public void greet() {
        System.out.println(name + ": Hi!");
    }

    public void move() {
        System.out.println(name + ": Move!");
    }

    public int getSpeed() {
        return 10;
    }
}


```
## 5.接口的多态（同一个接口被不同的函数构造出不同的函数）
```java
class Athena implements Hero {
    private final String name = "Athena";
    public void attack() {
        System.out.println(name + ": Attack!!!");
    }

    public void greet() {
        System.out.println(name + ": Hi!!!");
    }

    public void move() {
        System.out.println(name + ": Move!!!");
    }

    public int getSpeed() {
        return 10;
    }
}

public class Main {
    public static void main(String[] args) {
        Hero[] heros = {new Zeus(), new Athena()};
        for (Hero hero: heros) {
            hero.greet();
        }
    }
}
```

## 2. 性质
1. 不能用 new 运算实例化一个接口
	X=new comparable ()，错误的

2. 可以声明一个接口的变量
	Comparable x;

3. 接口变量一定引用实现了这个接口的类对象, 之后我们就可以通过这一个对象来调用对应的方法。
	X=new employee ();
	
4. 一个接口可以拓展一个子接口，我们可以用 instanceof 运算来检查某个类是否使用了某一个接口

5. 每一个的函数的参数和返回值都必须与接口中的相同，前缀可以降低可见度，但不能提升可见度
```java
implement curable{
	void cure();
}
class people implement curable{
	public void cure(){
	}
}
```
6. 接口中可以含有常量，在 Java 中，SwingConstant 接口就是一个全部都是常量的接口.


***6、接口是可以被多重继承的，一个类可以同时继承若干个接口，

## 3.与抽象类的关系
接口优于抽象类在于抽象类只能为一个子类提供相应的方法，而一个接口可以为很多的子类提供相应的方法
## 4.静态与私有方法
可以实现，但是违背初衷
## 5.默认方法
用 default 修饰符修饰符修饰一个方法，那么在没有被新的覆盖的情况下，都会采用这个方法。
```java
public interface Comparable<T>{
	default int compareTo(T other){return 0;}
}
```
可以用于接口演化，将老版本的方法现代化的一种技术
## 6.解决默认方法冲突
1. 超类优先，如果超类提供了一个具体方法，同名而且有相同参数的方法会被忽略。
2. 接口冲突：两个接口都同时给出了同名同姓的方法，这时候，我们必须在实现方法是说明我们实现的是那一个接口
 **在我们没有添加默认方法之前，我们只需要在类中实现一个即可，因为理论上来说，这个时候两个是等价的。
 
```java
interface name{
	String a();
}
interface ab{
	String a();
}
class people implements name,ab{
	public String a() {
		return "hello";
	}
}
```
```java
interface Person{
	default String getName(){return ""};
}
interface Named{
	default String getName(){return getClass.getname()};
}
class Strudent implements Person,Named{
	public String getName(){return Person.super.getName();}
}
```
3. 从一个超类继承了方法，并从一个接口扩展了一个默认方法，这个时候我们选择类优先，即先保证实现超类的方法 
## 7.接口与回调
1.指定某个特殊事件发生时应应该采取的采取的方法，在 java 中我们可以直接传输一个大类供函数使用，然后再调用对应的方法，传递一个对象比传递一个方法要灵活得多。
**（回调在某种意义上是一个上转型），使得实例化的接口可以用于我们的方法直接使用**

```java
import java.awt.Toolkit;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.time.Instant;

import javax.swing.*;
public class qwqe213 {
	static class  TimePrinter implements ActionListener{
		public void actionPerformed(ActionEvent event) {
			System.out.println("123123123"+Instant.ofEpochMilli(event.getWhen()));
			Toolkit.getDefaultToolkit().beep();
		}
	}
	public static void main(String[] args) {
		var Listener=new TimePrinter();
		Timer t=new Timer(1000,Listener);
		t.start();
		JOptionPane.showMessageDialog(null, "Quit program?");
		System.exit(0);
		
		
	}

}

```



3. 对象克隆
```java
	var origion =new Employee();
	Employee copy =origion;
	//无论是copy或是origion其中哪一个改变都会影响另一个的值
```
```
```
而我们使用克隆的话就各自都有不同的状态。克隆是一个 protected 方法。

 
# lambda 表达式
1. 为什么引入λ表达式
	λ表达式本质上是一个匿名方法。
	λ表达式有三部分组成：参数列表，箭头（->），以及一个表达式或语句块
	格式：（参数）->{函数体}；注意，**返回值可以有系统自行去判断**！
	
```java
	(String first,Stirng second)->{
		if(first.length()<second.length()) return -1;
		else if(first.length()>second.length()) return 1;
		else return 0;
	}
	//即使λ表达式没有参数，仍然要提供空括号
	()->{for(int i=100;i>=9;i--) System.out.println(i);}
```

2. 函数式接口（以接口为参数的函数）
	使用 lambda 表达式，可以快速的给函数式接口提供他所需要的函数，而不需要再在某一个类中定义。
	比如说我们的 sort 函数，你不添加说明，他就是默认方法排序，你可以通过手写自己的排序，来实现自己所需要的排序。
	再比如我们前面所写的
```java
Timer t=new Timer(1000,Listener);
```
就可以改写成
```java
Timer t=new Timer(1000,event->{System.out.println("111");});
```

3. 一些常用的函数式接口
	 Bitfunction
	 Predicate----->ArrayList 中 removeif 方法中判断是否要删除的依据函数
	 Supplier------>实现懒计算

4. 方法引用
```java
var time=new Timer(1000,System.out::println);
//会生成一个ActionListener，他的actionPerformed方法要调用System.out.println(e);
```

使用的时候如果我们不知道，或者懒得写，我们就可以用两个双引号的方法让系统去自己匹配
用途
1. 匹配函数，用于构建一个函数
```java
personList.sort(person::compare);//类型自动推断
```
2.Stream 流中的应用
```java
personList.stream().sorted(person::compare).foreach(System.out::println)
```
3. 调用对象方法
```java
personList.stream().sorted(person::compare).foreach(p::concat)
```
4.:: new 实例化对象
```java
personList.stream.sorted(person::compare).collect(Collectors.toCollection(ArrayList::new));
```
5. 指代特定实例
```java
Arrays.sort(stringArray,String::compareToIgnoreCase);

```

### 怎么处理 lambda 表达式
延迟运行
# 内部类
	1.出现原因
内部类可以对同一个包中的其他类隐藏
内部类可以访问定义这个类的作用域中的数据
2. 使用方法
3. 
