# 内部类
## 定义：在一个类里面再声明一个类，我们在具体实现的时候，可以把这个类看作是一个变量，所以他也可以被 private, public, static 等修饰符修饰。



## 规则：
**在类 A 中的内部类 B, 若 B 中要使用 B 创建对象，则需要 B obj = new A (). New B ();
1. 内部类中不可以声明类方法，类变量，外嵌类的类体中可以用内部类声明对象，作为外嵌类的成员
2. 一个类把内部类看作是自己的成员
3. 外嵌类的类体中可以用内部类成名的对象，作为外嵌类的成员
4. 外嵌类的成员变量在内部类中仍然有效，内部类中的方法也可以调用外嵌类中的方法
5. 内外嵌类和内部类在编译时，生成两个. Class 文件

```java
public class TalkingClock{
	private int interval;
	private bool beep;
	public class TimePrinter implements ActionListener{
		public void actionPerformed(ActionEvent event){
			System.out.println("111");
			if(beep){//这里的访问明显是超越了界限的，这就说明内部类本质上被看作一个类中的变量。
				System.out.println("beep");
			}
		}
	}
}
```
1. 内部类可以简洁的实现回调 (感觉不如 lambda 表达式)
```java
public class Sys{
	static Outer out;
	public class out{
		public void print(String msg){
			System.out.println(msg);
		}
	}
}
	public class Main{
		public static void main(String[] agrc){
			Sys.out.print("mmsl");
		}
	}
```

### 内部类的类型
**局部内部类（没有修饰符的）**

**匿名内部类 （没有名字的内部类）

**静态内部类（）

非匿名内部类也可以有子类
内部类可以是 final 类


# 匿名类
## 定义：没有显式声明的一个类的子类，我们一般用于抽象类和相关的定义中。匿名类通常继承一个父类或实现一个接口。----》直接用大括号加类体创建一个对象的方法）

**匿名类一定是内部类**

```java
package 广告牌;

class Polygon{
	private int t=1;//此时下方的display不能使用这里的t，因为下面的实现实际上是继承了在外部的类之后我们再去重新构建或者重写。
	public void display() {
		System.out.println("in");
	}
}
class AnnoymousDemo{
	public void createClass() {
		Polygon p1=new Polygon() {
			public void display() {
				System.out.println("in ann"+t);
			}
		};
		p1.display();
	}
	
}
public class Main {
	public static void main(String[] agcr) {
		AnnoymousDemo an=new AnnoymousDemo();
		an.createClass();
	}
}
//### 匿名类实现一个接口
interface Polygon {  
   public void display();  
}  
  
class AnonymousDemo {  
   public void createClass() {  
  
      // 匿名类实现一个接口  
      Polygon p1 = new Polygon() {  
         public void display() {  
            System.out.println("在匿名类内部。");  
         }  
      };  
      p1.display();  
   }  
}  
  
class Main {  
   public static void main(String[] args) {  
      AnonymousDemo an = new AnonymousDemo();  
      an.createClass();  
   }  
}
```

```java
new Bank(){
	匿名类的类体
};//该匿名类时bank类的子类
new comparable(){
	匿名类的类体
}//该匿名类是实现了comparable接口的匿名类
```

不能用匿名类声明对象，但却可以直接用匿名类创建一个对象。
匿名子类可以继承父类的方法，也可以重写父类的方法。

# 异常类
## 一、Error 与 Exception 的区别
  Error 是程序无法处理的错误，比如 OutOfMemoryError、ThreadDeath 等。这些异常发生时，Java 虚拟机 (JVM)一般会选择线程终止。此类异常是程序的**致命异常**，是无法捕获处理的。Exception 是程序本身可以处理的异常，这种异常分两大类运行时异常和非运行时异常。程序中应当尽可能去处理这些异常。

   而 Exception 就是非致命性的，比如除数为 0，或超过范围等。
    Java 中的异常，在一个方法运行的过程中，如果发生了异常，那么这个方法生成一个代表该异常的对象，并把它交给运行时系统，运行时系统寻找相应的代码来处理这一异常，我们把生成异常对象并把它提交给运行时系统的过程称为抛弃一个异常。
    运行时系统在方法的调用栈中查找，从生成异常的方法开始进行回溯，直到找到包含相应异常处理的方法为止，这一过程称为捕获一个异常。
## 二、Exception 类的继承关系
java 中的所有异常都是由 throwable 类的子类生成的，所有的异常类都是 throwable 类的子类或子类的子类。
![[Pasted image 20230515165556.png]]

其中 Error 类是一些严重的程序不能处理的错误类，包括内存溢出，动态连接失败，虚拟机错误等，java 程序不做处理

其中 Exception 类中是一些程序中可预知的问题，其产生的异常可能会带来意想不到的错误，所以需要我们处理。
	
	
## 三、运行时异常和非运行时异常的区别
**运行时异常都是 RuntimeException 类及其子类异常，如 NullPointerException、IndexOutOfBoundsException 等，这些异常是非检查型异常，程序中可以选择捕获处理，也可以不处理。这些异常一般是由程序逻辑错误引起的，程序应该从逻辑角度尽可能避免这类异常的发生。

**非运行时异常是 RuntimeException 以外的异常，类型上都属于 Exception 类及其子类。从程序语法角度讲是必须进行处理的异常，如果不处理，程序就不能编译通过。如 IOException、SQLException 等以及用户自定义的 Exception 异常，这些是检查型异常。一般情况下不自定义检查型异常。

**用了 try-catch，出错只会影响 try 语句块内，而不会影响语句块外部的语句。

## 四、内置异常类（可以解决的）
#### 非检查异常（应该你自己去注意的）（包括 Error 和 RuntimeException）
![[Pasted image 20230515171158.png]]
#### 检查类异常（你可以交给程序让他来帮你找的）
![[Pasted image 20230515171340.png]]
***TIPS：如果父类方法中构造了一个抛出异常，那么子类的方法中抛出的异常不能比父类方法更加通用，于是，父类没有抛出异常，子类也不能抛出异常。


## 五、内置异常方法
![[Pasted image 20230515171402.png]]
## 六、自定义异常类
```java
class FileFormatException extends IOexception{
	public FileFormatException(){}
		public FileFormatException(String gripe){
			super gripe;
		}
}
```

## 六、语法结构
```java
		try {
            array[k] /= x;//怀疑可能会出错的语句
        } catch (ArithmeticException e) {如果发生了错误A
            System.out.println("divide 0");
            e.printStackTrace();
        } catch (ArrayIndexOutOfBoundsException e||IOException e) {如果发生了错误B
            System.out.println("out of bound");
            e.printStackTrace();
        } finally {//finally里面无论如何都会执行
            for (int i = 0; i < 5; i ++ ) {
                System.out.println(array[i]);
            }
        }
```
***TIPS：finally 中一般写一些必须要进行的，比如要开一定要关，那就算打开的时候出现了异常，也一定要关掉（数据库，网络，文件）。这个时候我们就可以把关闭语句写在 finally 中。

**finally 语句的优先级甚至比我们的 return 还要高，当我们在 try-catch 中先写了一个 return 时，他也是会先去执行 finally，然后再去吧 return 给执行力。

***TIPS：当我们不知道 catch 中要写什么的时候，我们可以反复写若干个 catch 去去枚举可能的错误，或者我们写一个 Exception e;catch 语句中的可以使用|&等逻辑判断。

***TIPS: 我们执行的错误不能有包含关系，如果我们有一个大类，小类将变得不可用。

***TIPS：我们在 catch 后，一般会选择输出错误信息，来让使用者知道问题在哪里。
比如下面这张图就是很典型的一个例子，

![[C~ZU@D6_V({_I5)Y1D(0`70.png]]
```java
try {
   array[k] /= x;
} catch (Exception e) {
    e.printStackTrace();
}
```


## 七、抛出异常
```java
import java.io.IOException;
import java.util.Scanner;

public class Main {

    private static void foo() throws IOException, NoSuchFieldException {
        Scanner sc = new Scanner(System.in);
        int x = sc.nextInt();
        if (x == 1)
            throw new IOException("找不到文件！！！");
        else
            throw new NoSuchFieldException("自定义异常");
    }

    public static void main(String[] args) {
        try {
            foo();
        } catch (IOException e) {
            System.out.println("IOException!");
            e.printStackTrace();
        } catch (NoSuchFieldException e) {
            System.out.println("NoSuchFieldException!");
            e.printStackTrace();//堆栈轨迹
        }
    }
}

```
***TIPS: throw 表示我们知道这个错误，但我们不在乎这个错误。


## 八、try-with-resources
***可以保证在语句执行完毕后确保每个资源都被自动关闭。
```java
import java.io.*;

public class Main {

    public static void main(String[] args) {
        String line;
        try (
                BufferedReader br = new BufferedReader(new FileReader("input.txt"));
                BufferedWriter bw = new BufferedWriter(new FileWriter("output.txt"));
        ) {
            while ((line = br.readLine()) != null) {
                System.out.println("Line => " + line);
                bw.write("copy: " + line + "\n");
            }
            bw.flush();
        } catch (IOException e) {
            System.out.println("IOException in try block =>" + e.getMessage());
        }
    }
}
```

## 九、优化方法或相关规范
### 1.再次抛出异常与异常链
*当我们想要判断一个子系统是否出现问题，我们当然可以直接一个 throw 完事完事，但这样有可能会

## 2. 堆栈轨迹
#### 1.定义：堆栈轨迹是我们用于分析一个某个特定点上所有挂起方法调用的一个列表，即在某一个 timestamp 上，哪一个方法/函数正在被我们调用（使用）。例如我们在调用阶乘的时候，就会得到下面的结果。
**一般我们在阅读的时候都是从下往上阅读这就是我们出现的时间顺序，也是从外到内的顺序**
#### 2. 方法：我们构建一个 StackWalker 类类，该类具有访问堆栈轨迹的能力。

### 3. 早抛出，晚捕获

# 断言
## 一、语法形式

### 1、assert condition;
**这里 condition 是一个必须为真 (true)的表达式。如果表达式的结果为 true，那么断言为真，并且无任何行动如果表达式为 false，则断言失败，则会抛出一个 AssertionError 对象。这个 AssertionError 继承于 Error 对象，而 Error 继承于 Throwable，Error 是和 Exception 并列的一个错误对象，通常用于表达系统级运行错误。

### 2 、asser condition: expr;这里 condition 是和上面一样的，这个冒号后跟的是一个表达式，通常用于断言失败后的提示信息，说白了，它是一个传到 AssertionError 构造函数的值，如果断言失败，该值被转化为它对应的字符串，并显示出来。

# 日志
## 一、基本日志

```java
logger.getGlobal().info("File->Open menu item selected");//默认情况下会打印下面的语句
//May 10,2013,xxxxxxxxx
//Info:File->Open menu item selected

logger.getGlobal().setLevel(Level.OFF);
//取消所有日志
```

## 二、高级日志
创建自己的日志记录器，而不是采用全局日志记录器。
```java
private static final Logger myLogger=Logger.getLogger（“com.company.myapp”）
//该句话中，com.company表明层次
```
类似于包，日志也有层次结构，而且结构更强，一般而言我们有七个层次：

![[Pasted image 20230515202124.png]]

```java
Logger.setlevel(Level.all)//开启所有等级的日志

```
### 三、方法
1. 所有级别的日志都有记录方法
```java
logger.warning(message);
logger.fine(message)；
logger.log(Level.ALL,message);//修改特定等级的日志
```
2. 我们想要获得调用类的准确方法和信息
```java
void logp(level l,String classname,String methodname,String message)
```

## 四、用途
记录异常
```java

int read(String file,String pattern){
	........
}
if(....){
	var e=new IOException("iosadf");
	logger.throwing("com.mycompany.mylib.Reader","read",e);
	throw e;
}
```

## 五、修改日志管理器配置（）
## 六、本地化
本地化的应用程序包含资源包中的本地特定信息。资源包包括一组映射，分别对应各个本地化环境。一个资源包可能将字符串"readingFile"映射成"读取文件"