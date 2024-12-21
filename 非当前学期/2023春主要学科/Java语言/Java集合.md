# Collection 接口
### 1. 集合可以理解为一个动态的对象数组，不同的是集合中的对象内容可以任意扩充

### 2.集合的特点：性能高，容易扩展和修改

### 3. 常用子类 List Set Queue

## 通用方法
```java
add()//向集合中添加一个元素
addall()//将一个集合中的所有元素都移到另一个集合中
clear()//移除所有元素
```

# List 接口 (Extends Collection)
### 1.list 接口中可以存放任意的数据，并且当中内容可以重复

### 2 . 常用子类 ArrayList Vector（双向链表）

通用方法
```java
isEmpty()//判断集合是否为空
indexOf()//查找指定对象是否存在，存在就返回第一次出现的位置
```

ArrayList 方法
1. 构造方法
ArrayList ()//构造一个空 ArrayList
ArrayList (Collection c) 根据给定集合的元素来构建。
**注：构造时可以不用泛型



```java
List<Stirng> lists=null;
lists=new ArrayList<String>();
lists.add("A");
lists.add("B");
for(int i=0;i<lists.size();i++){
	System.out.println(lists.get(i));
}
list.remove(0);

```

Linkedlist 方法
构造
```java

LinkedList()//狗列表
LinkedList(Collection c)//根据给定集合元素创建链表
LinkedList li=new LinkedList();

```

必备方法：
```java
<E> add();
<E> indexof();
<E> get();
```
区别：
ArrayList：性能高，线程不安全
Vector：性能差，性能安全

# Set 接口
1. 不能加入重复元素，但是可以排序
2. 常用子类
	1. hashset
	2. treeset
hashset 不带有排序
```java
Set<Integer> set=null;
set=new Hashset<int>();
s.add(1);
s.add(2);
s.add(3);
System.out.println(s);
```
Treeset 自带排序
```java
Set<Integer> set=null;
set=new Treeset<Integer>()l
s.add(1);
s.add(2);
s.add(114514);
```

必备方法：
```java
add();
clear();
remove();
contain();
```

# Iterator 接口
**除了 iterator 方法之外，我们还有其他的方法去遍历我们的集合，比如说我们的 for each 方法。迭代器中的 

构造
```java
Iterator it=Collection.iterator();
```

1. 集合输出的标准操作
	将元素一个一个进行判断，判断其是否
2. 常用方法
```java
hasNext()//判断是否有下一个元素
next()//进入下一个元素；
remove()//移除当前位置
```

```java
List<int> lists=new ArrayList<int>();
lists.add(1);
lists.add(2);
lists.add(3);
Iterator<int> it=lists.iterator();
while(it.hasnext()){
	System.out.println(it.next());
}
```

3. 拓展方法
```java
//遍历方法2，可以让我们不用一定要顺序遍历
iterator.forEachRemaining(element->//lambda表达式);
```

# Map 接口
1. 以键值对的方式保存我们可以理解为一个特殊的数组（）
2. 一个数值只能对应一个答案，如果有更新，则会覆盖前面的答案。
3. 常用子类
	1. hashmap，无序存放，唯一
	2. hashtable，无序存放，唯一
4. 常用方法
	Put (key, value); 添加一个映射
	Get (key); 返回关键字对印度个值
	ContainKey (key); 是否包含关键字
	Remove (key); 删除关键字
	Size (); 大小
	Isempty () ;是否为空
	Clear (); 情况
	**Map. Entry (K, V)//获取所有对象的集合*****, 只有添加上这个之后，我们才拥有遍历 map 中元素的能力***
	**Map. Value (K, V)//获取所有的值的集合
	Map. Keyset (K, V)//获取所有键的集合**	
	CeilingEntry (key)//返回大于等于 key 的最小元素，不存在就返回 null
	FloorEntry (key)//返回小于等于 key 的最大元素，不存在就返回 null	
	**Map.foreach（lambda 表达式）//用 lambda 表达式中的方法来遍历我们的 map 集合**

```java
Map<String,String> map=new hashmap<String><String>();
map.put("key1","111");
map.put("key2","11111");
map.put("key3","114514");
String str=map.get("key1")l
System.out.println(str);
if(map.containsKey("keys")){
	System.out.println("yes");
}else{
	System.out.println("NO");
}
if(map.containsValue("value")){
	System.out.printf("yess");
}else{
	System.out.println("NOO");
}
Set<String> s=map.keySet();
Iterator<String> it=s.iterator();
while(i.hasNext()){
	System.out.println(i.next());
}
Set<String> s=map.values();
Iterator<String> it=s.iterator();
while(it.hasNext()){
	System.out.println(it.next());
}
mp.forEach((k,v)->System.out.println((k+v)));
```
# Hashtable 接口
1. 构造
```java
Hashtable();
Hashtable(int capacity,float loadFactor);
```
# Queue 接口
1. 主要实现我们数据结构中的队列、循环队列、优先队列等队列相关的问题。
2. 常用接口 
	1. LinkedListQueue（队列）
	2. CircularArrayQueue（循环队列）
	3. priorityqueue (默认小根堆？)

![[Pasted image 20230626210411.png]]

```java
Queue<Integer> q=new PriorityQueue<Integer>();
q.add(1);
q.add(2);
q.add(114514);
q.add(1919810);
List<int> res=new ArrayList<int>();
while(k--){
	res.add(heap.remove());
}
```
# Stack 接口
1. 基本上和你数据结构里学的 Stack 没有任何区别，区别只是在语法方面。
```java
Stack<Integer> s1,s2;
s1=new Stack<Integer>();
```


## Vector (向量)
基本上和我们 C++中一样的，我们不妨背一下函数。
构造方法
```java
vector();
vector(指定容量)
vector(指定容量，指定增量(用完时一次性增加的个数))
```

**java 中的排序字符串的排序默认是按照字母从大写到小写，字母从小到大的顺序来排序的。



