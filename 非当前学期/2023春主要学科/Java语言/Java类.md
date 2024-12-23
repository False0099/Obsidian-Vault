# 一、对象和对象变量
***类定义一种全新的数据类型，包含一组变量和函数；对象是类这种类型对应的实例。例如在一间教室中，可以将 Student 定义成类，表示“学生”这个抽象的概念。那么每个同学就是 Student 类的一个对象（实例）。

## 1. 源文件声明法则
#### 一个源文件中只能有一个 public 类或 public 接口 。
#### 一个源文件可以有多个非 public 类。
#### 源文件的名称应该和 public 类的类名保持一致。
#### 每个源文件中，先写 package 语句，再写 import 语句，最后定义类。

## 2. 类的修饰符
### 一、范围修饰符
Private 不让别人看到，只能被该类对象方位
Default 该模式下，只能在同一个包中进行访问
Protect 只能被类本身的方法以及子类访问，即使子类在不同的包中也可以访问
Public 所有人都能用，不仅可以跨类，还可以跨包
![[Pasted image 20230627220559.png]]

### 2. Static 修饰符
使用后，该类、变量、函数是一个静态变量，又称为类变量

1.所有 static 成员变量/函数在类中只有一份，被所有类的对象共享；
2.所有普通成员变量/函数在类的每个对象中都有独立的一份；
3.静态函数中只能调用静态函数/变量；普通函数中既可以调用普通函数/变量，也可以调用静态函数/变量。


**static 不能修饰除内部类以外的类

**局部变量不能用 static 修饰，static 只能修饰全局变量

**Static 数据成员可以通过对象名进行访问，也可以通过类名直接访问

**不能修饰构造方法

### 3.final 修饰符
使用 final 修饰符辨明这是一个常量，值只能分配一次，不能更改。当我们用一个 final 加上一个 static 时，我们就有了 c++中类似于 const 的一个修饰符。
如果是 class 加上 final，说明这个方法不能够再被继承了

final 修饰一个 final，子类不能够重载该方法

**不能修饰构造方法

### 4.transient 修饰符
告诉编译器，在类对象序列化的时候，此变量不需要持久保存，
主要是因为该变量可以通过其他变量得到，使用是为了性能

### 5 .volatile 修饰符
指出由多个线程修改此变量，要求编译器优化以保证对此变量的修改能够被正确的处理。

### 6.abstract 修饰符
用于类表明该类是一个抽象类
用于函数，表明是一个抽象方法，即我们声明而不去具体实现
（abstract 不能和 final 或者 static 一起使用）

### native 修饰符
**只修饰方法**用该修饰符修饰的方法在类中没有实现，而是使用 C 或 C++实现。

**不能修饰构造方法

### synchronized 修饰符
**只修饰方法**表明这是一个支持多线程的

**不能修饰构造方法

**注意：java 中没有全局变量，只有方法变量，实例变量（类中的非静态变量），类变量（类中的静态变量）。

方法中的变量不能够有访问修饰符，所以访问修饰符只针对于在类中定义的变量
类变量也可以通过类变量初始化器来进行初始化，类变量初始化器是一个用 static 包含的语句块，且只能初始化一次



```java
class Point {
    private int x;
    private int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public void setX(int x) {
        this.x = x;
    }

    public void setY(int y) {
        this.y = y;
    }

    public int getX() {
        return x;
    }

    public int getY() {
        return y;
    }

    public String toString() {
        return String.format("(%d, %d)", x, y);
    }
}
```

## 3. 相关规则
**在 java 中，要使用一个类，必须要先构造对象，指定其初始状态，然后对对象应用方法。

**在类方法中；在构造方法中使用 this 调用其他构造方法时须放在方法第一句

```java
test(int u,int v){
		this.v=v;
		this(u);
}//编译无法通过
test(int u,int v){
		this(u);
		this.v=v;
}//编译可以通过
```

Date birthday=new Date()//new语句构造了一个新对象，同时构造了一个对象变量```
```java
	string s=birthday.toString()//可以运行
	Date dealline;//定义了一个对象变量，但没有引用任何对象
	String s=birthday.toString()//不可以正常运行，编译错误
```
***
***1.成员对象在没有说明的情况下，默认值都是 “0”，各种各样的 0

# 二、对象的生成
### 1. 我们用 new 操作符生成一个对象
### 2. 对象的使用
对象的使用是通过一个引用类型的变量来实现，包括引用对象的成员变量和方法，通过运算符. 可以实现对变量的访问和方法的调用
```java
BirthDate date;
int day;
day=date.day;
date.tomorrow();
```
注意：
	1. new 的含义与 C++不同
	2. 构造函数是一个特殊放啊，函数名和类名称一样
	3. new 的过程可以为调用构造函数完成准备工作
	4. 构造函数可以不写，不写由编译器默认生成
	5. 深层的生成机制，Class< T >
## 3. 构造相关的构造器
#### 1. 构造器与类同名
#### 2. 每个类可以有一个以上的编译器
#### 3. 每个每个构造器可以有 0-正无穷个参数
#### 4. 构造器没有返回值
#### 5. 构造器一般都伴随着 new 操作使用
## 4. 构造中存在的技巧或方法
#### 1.重载
多个方法有相同的名字、不同的参数，便出现了重载，编译器会选择一个最合适的方法来运行函数。如果没有输入值，会按照默认字段初始化
#### 2. 无参构造
专门写了或者啥都没写才能用；
#### 3. 显示字段初始化
在执行构造器之前先定义
#### 4. 调用另一个构造器 (常用于继承)

## 5. 析构函数相关
***一般不构造，因为 java 会自己析构
***如果一定要自己构造，那么就要按照下面的方法去构造

```java

```
# 三、var 类型，一种万能的类型
***var 是弱化类型的定义，可代替任何类型，编译器会根据上下文来判断你到底是想用什么类型，在无法确定自己将用的是什么类型时就可以使用var，类似 OBJECT 但是效率比OBJECT高。（类似于auto）

# 四、名字空间以及访问规则
由于 java 编译器为每一个类乘胜一个字节码文件，且文件名与类名相同，因此同名的类可能会有冲突。
为了解决这一问题，java 提供包（package）来管理类名空间

## 打包
java 中用 package 语句来讲一个 java 源文件中的类达成一个包。
package 语句作为 java 源文件的第一条语句，指明该文件中定义的类所在的包。



用于保证类名的唯一性，包不同，类名相同也不会出错
## 1.package的导入
为了能使用 java 中已经提供的类，我们需要用 import 语句引入所需要的类。

import  java.util.*;

如果两个包内都有相同的类，就会出现编译错误，我们需要加一行import语句来强调用的是哪一个
```java
import java.util.*;

import java.sql.*;

import java.util.Date;
```

## 2.静态导入

 一种import语句允许导入静态方法和静态字段，而不只是类
例如：

```java
```import static java.lang.System.*
```
就可以使用 System 类的静态方法和静态字段而不用加前缀
out.println("gOODbye“）；

另外还可以导入特定的方法或字段
import static java.lang.System.out;

## 3.包中增加类

包的名字放在源文件的开头

package com.horstmann.corejava;

如果没有前置 package 语句，源文件中的类就属于无名包。

# 五、类中的类型转换
## 上转型
**假设 A 是 B 的父类，如果我们用子类 A 创建一个对象 a，并且把这个对象赋给父类 B，那么我们就称对象 a 是对象 b 的上转型
（父类引用指向子类对象）

## 下转型
把原本的父类强行通过强制类型转换变成他的某一个子类。

注意：
1. 上转型对象**不能操作子类新增的成员变量***，**不能调用子类新增的方法。***
2. 上转型对象**可以访问子类继承或隐藏的成员变量**，也可以调用子类继承的方法或子类重写的实例方法
3. 如果子类重写了父类的某个实例方法后，当用上转型对象调用这个实例方法时，一定是**调用了子类重写的实例方法**。

# 五、类的 UML 图
1.UML 包含三层，第一层是名字层，第二层是变量，第三层是方法


