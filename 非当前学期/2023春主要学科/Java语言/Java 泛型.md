## 一、应用
在我们使用 Java 自带的数据结构式，通常用的就是我们的泛型，泛型用通俗的语言来讲就是把数据类型给变得能够随着用户的不同需求而发生改变。Java 是强解释性语言，范型是为了复杂继承或实现结构的边界约束，T 这是个空类，你这个就应该打印一个实例地址。
```java
List<Integer> a=new LinkedList<>();
```
## 二、手写泛型
一个泛型中可以有一个或者更多个占位符。
```java
class people<T>{
	private T name;
	private T cnt;
	private T sa;
	public void setname(T name) {
		this.name=name;
	}
	public T getname() {
		return this.name;
	}
}
```

## 三、泛型方法的实现
```java
class people3<T>{
	public void show(T name) {
		System.out.println(name);
	}
	public static<T> void show1(T name) {
		System.out.print("can can need ");
		System.out.println(name);
	}//静态方法由于属于全局，所以我们要在函数定义返回值前声明，而且这样的声明与原来的类中定义的泛型时无关的
	
}
```

在使用泛型方法是，如果我们设置的参数是没有歧义的，那当然最好，比如说
```java
String middle=Array.get("1","114514");
```
但是，如果我们出现有些不一样的，容易造成歧义的情况时，我们就会有不同的结果
```java
double middle=ArrayAlg.get(1,1.1);(可能会报错)
```
因为，在这里，虚拟机在处理这一段代码是，会先找到两个类的最近公共祖先类（？），就比如非常抽象的 Number 类和 Comparable 接口。所以就会导致我们最后有错误。，要补救这一点，我们最好都写成 double。
## 四、泛型接口
```java
interface n<T>{
	public void Name(T a);
}

class people4<T> implements n<T>{
	public void Name(T a) {
		System.out.println(a);
	}
}
//我们也可以直接实现具体类型
class people4 implements n<String>{
	public void Name(String a){
		System.out.println(a);
	}
}
```
## 五、泛型擦除模式（虚拟机）
操作：先在虚拟机中定义一个泛型变量，然后为这一个变量设置一个很广泛的类型。
```java
public T lower------->public Object lower;
```
之后再根据你所指定的类型，我们进行一个强制类型转换
```java
Object lower----->(String) lower
```
性质：1.无论你使用哪一种泛型，在判断类型时，都是相同的。背后的原理在于反射。擦除的含义就是会擦除所有的你输入的类型
2.
```java
class people4<T> implements n<T>{
	public void Name(T a) {
		System.out.println(a);
	}
}
people4<Integer>==people4<String>//结果为true
```

```java
Class DateInterval extends Pair<LocalDate>{
	public void setSecond(LocalDate second){
		if(second.compareTo(getfirst())>=0){
			super.setSecond(second);
		}
	}//覆盖原有的setSecond方法
}
```
我们对上述的类进行类型擦除后，就会变出两个方法
一个是这一类中所包含的
```java
setSecond(LocalDate second);
```
另一个是父类中的
```java
setSecond(Object second);
```
为了解决这个问题，我们会生成一个桥方法，同时存储上面两个方法，依次来保持我们的多态


总结：
***1. 虚拟机中没有泛型，只有普通的类和方法
2.所有的类型参数都会替换为他们的限定类型
3.合成桥的方法保持多态

## 六、泛型通配符（约束）
java 的继承，并不是泛型的继承。
通配符可以通过设置上边界和下边界来防止所有类型的都能被传进去
extend 表示 T 必须是某一个的子类，super 表示 T 必须是某一个的父类

上边界：读取但是不写入
下边界：写入不读取
```java
package 验证码;
import java.util.*;
class people4<T>{
	private T name;
	public T getname() {
		return name;
	}
	public void setname(T a) {
		this.name=a;
	}
	<T extends Comparable<T>>(表示我们有一个类型T，必须是在comparable的子类当中，也就是说T一定要可比较)
	public void show(people4<?> A){
		//?表示多个类型，T表示单个类型
		this.name=(T) A.getname();//墙砖
		System.out.println(name);
	}
}
public class create {
	public static void main(String[] s) {
		Set<String> u=new HashSet<>();
		people4<Number> p=new people4<>();
		people4<Integer> p1=new people4<>();
		p1.setname(111);
		p.show(p1);//没加？前，p1会报错,p不会报错。
		
	}
}


```
对于一个泛型，我们可以有多个限制条件，每个限制条件之间用&符号间隔。
## 泛型中的 ？
一种通配符，可以解决原本存在的继承类型问题，例如
```java
public static void print(Pair <Employee> p){
	.......
}
```
我们现在有一个子类方法，叫做 Pair< Manager >，但是我们也想要调用上面的一个方法。这个时候，就需要我们使用通配符？使得上述的泛型能够兼容他的子类
![[Pasted image 20230625221904.png]]
同样的，我们也可以添加一个超类限制，即某一个类不能超过某一个超类
![[Pasted image 20230625222000.png]]

## 泛型中的继承原则
```java
Manager[] top=.....
pair<Employee> result=ArrayAlg.minmax(top);//错误的，无法编译
```
无法编译的原因在于，我们要求左边的类是 Employee，但右边只能提供一个 pair < manager >类型，导致我们错误。

**无论 S 和 T 有什么关系，pair< S >和 pair< T >一般都没有关系 

其他的注意点：
1. 不能用基本类型实例化类型参数
2. 运行时类型查询只适用于原始类型
```java
if(a instanceof Pair<String>) 错误的
if(a instanceof Pair<T>) 错误的
if(a instanceof Pair) 正确的
if(a instanceof Pair<?>) 正确的
```
4. 不能创建参数化类型的数组（泛型数组）
	其实是不准确的，准确的来说只是不能够创建数组
```java
Pair<Stirng>[] //合法的
new Pair<String>[10] //不合法
```
5. 不能实例化类型变量
6. 不能构造泛型数组
7. 泛型类不能用静态
8. 不能抛出或捕获泛型类的实例
9. 可以取消对检查型异常的检查