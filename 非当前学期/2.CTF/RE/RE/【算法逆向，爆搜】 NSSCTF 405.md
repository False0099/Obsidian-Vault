## 题目：
```python
flag = 'xxxxxxxxxxxxxxxxxxxxx'

s = 'wesyvbniazxchjko1973652048@$+-&*<>'

result = ''

for i in range(len(flag)):

    s1 = ord(flag[i])//17

    print(s1)

    #print("\n")

    s2 = ord(flag[i])%17

    print(s2)

    #print("\n")

    result += s[(s1+i)%34]+s[-(s2+i+1)%34]

print(result)

# result = 'v0b9n1nkajz@j0c4jjo3oi1h1i937b395i5y5e0e$i'
```

## 题解
我们本题的思路是：通过我们的 python 来实现我们的爆搜，同时这个也是我们 python 的一个优秀的语法题, 虽然其实也能用 CPP 写
```cpp
#include <bits/stdc++.h>
using namespace std;
char s[]="v0b9n1nkajz@j0c4jjo3oi1h1i937b395i5y5e0e$i";
char dic[]="wesyvbniazxchjko1973652048@$+-&*<>";
int main(){
	vector<char> ans;
	for(int i=0;i<strlen(s);i+=2){
		for(int j=0;j<=114514;j++){
			int s1=j/17;
			int s2=j%17;
			if(dic[(s1+i/2)%34]==s[i]&&dic[(34-(s2+i/2+1))%34]==s[i+1]){
				ans.push_back((char)j);
				break;
			}
		}
	}
	for(auto u:ans){
		cout<<u;
	}
} 
```