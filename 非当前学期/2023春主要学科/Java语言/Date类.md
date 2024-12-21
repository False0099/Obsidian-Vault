```cpp
import java.util.*;
import java.text.ParseException;
import java.text.SimpleDateFormat;

public class Main {
	public static void main(String [] argc) throws ParseException {
		//创建日期对象（日期、时间）
		Date d=new Date();
		//创建日期格式对象
		SimpleDateFormat s=new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
		//日期转化为字符串
		String str=s.format(d);
		//输出结果
		System.out.println(str);
		//字符串转换为日期
		String t="2021-12-20";
		SimpleDateFormat s1=new SimpleDateFormat("yyyy-MM-dd");
		Date e=s1.parse(t);
		System.out.println(e);
	}
}

```
计算时间差
```java
package test08;
import java.util.*;

public class Village{
	public static void main(String[] argc) {
		Date d=new Date();
		long t=d.getTime();
		System.out.println(t);
		
	}
}

```