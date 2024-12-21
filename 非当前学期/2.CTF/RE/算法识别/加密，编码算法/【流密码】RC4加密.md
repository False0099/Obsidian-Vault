## 简介
我们的 RC 4 加密属于一种流加密方式，秘钥长度可变，他加解密使用相同的秘钥。
所谓流密码，就是把我们的密钥进行扩展后，再去和我们的原来的密码进行我们的相关处理
## 加密流程：

### 初始化 s 盒（密钥扩展）
第一步：初始化 s 盒 (s 盒不进行初始化，默认为 0）, 其中，我们的 key 是某一个隐藏的参数。
 ```cpp
int rc4_init(unsigned char*s, unsigned char*key, unsigned long Len)
{
        int i = 0, j = 0;
        unsigned char k[256] = { 0 };
        unsigned char tmp = 0;
        for (i = 0; i < 256; i++) {
                s[i] = i;
                k[i] = key[i%Len];
        }
        for (i = 0; i < 256; i++) {
                j = (j + s[i] + k[i]) % 256;
                tmp = s[i];//打乱S盒的值
                s[i] = s[j];//交换s[i]和s[j]
                s[j] = tmp;
        }
        return 0;
}
```


在 IDA 中的常见形式：
```cpp
int __cdecl sub_8048CC2(int s_box, int key, unsigned int keylen)
{
  char *v3; // ecx
  unsigned int v4; // eax
  char v5; // ST13_1
  int result; // eax
  unsigned int v7; // et1
  signed int i; // [esp+14h] [ebp-114h]
  signed int j; // [esp+14h] [ebp-114h]
  int U; // [esp+18h] [ebp-110h]
  char K[256]; // [esp+1Ch] [ebp-10Ch]
  unsigned int v12; // [esp+11Ch] [ebp-Ch]

  v12 = __readgsdword(0x14u);
  for ( i = 0; i <= 255; ++i )
  {
    *(_BYTE *)(i + s_box) = i;                  // a1[i]=i
    v3 = K;
    K[i] = *(_BYTE *)(i % keylen + key);        // v11[i]=key[i%keylen]
  }
  U = 0;
  for ( j = 0; j <= 255; ++j )
  {
    v4 = (unsigned int)((*(unsigned __int8 *)(j + s_box) + U + (unsigned __int8)K[j]) >> 31) >> 24;// v4=((a1[j]+v10+v11[j])>>31)>>24,干扰变量，无效
    U = (unsigned __int8)(v4 + *(_BYTE *)(j + s_box) + U + K[j]) - v4;// v10=v10+s_box[j]+k[j]
    v5 = *(_BYTE *)(j + s_box);                 // tmp=s_box[j]
    v3 = (char *)U;                             // 干扰变量，无效
    *(_BYTE *)(s_box + j) = *(_BYTE *)(U + s_box);// s_box[j]=s_box[i]
    *(_BYTE *)(s_box + U) = v5;                 // s_box[j]=tmp
  }
  v7 = __readgsdword(0x14u);
  result = v7 ^ v12;
  if ( v7 != v12 )
    sub_806FA00(v3);
  return result;
}
```


### 加密操作：
```cpp
int rc4_crypt(unsigned char*s, unsigned char*data, unsigned long Len)
{
        int i = 0, j = 0, t = 0;
        unsigned char tmp;

        for (int k = 0; k < Len; k++)
        {
                i = (i + 1) % 256;
                j = (j + s[i]) % 256;
                tmp = s[i];
                s[i] = s[j];//交换s[i]和s[j]
                s[j] = tmp;
                t = (s[i] + s[j]) % 256;
                data[k] ^= s[t];//将明文与密钥流（打乱的s盒）进行异或加解密
        }
        return 0;
}
```

在 IDA 中的常见形式：
```cpp
unsigned int __cdecl RC4(int Data, unsigned int Datalength, int Key, int Keylength)
{
  char *v4; // ecx
  char v5; // ST2B_1
  unsigned int result; // eax
  unsigned int v7; // et1
  int uu; // [esp+1Ch] [ebp-11Ch]
  int vv; // [esp+20h] [ebp-118h]
  unsigned int i; // [esp+24h] [ebp-114h]
  char S_box[256]; // [esp+2Ch] [ebp-10Ch]
  unsigned int v12; // [esp+12Ch] [ebp-Ch]

  v12 = __readgsdword(0x14u);
  Sbox_init((int)S_box, Key, Keylength);
  LOBYTE(uu) = 0;
  LOBYTE(vv) = 0;
  for ( i = 0; i < Datalength; ++i )
  {
    uu = (unsigned __int8)(uu + 1);             // i=(i+1)%256
    vv = (unsigned __int8)(S_box[uu] + vv);     // j=(j+s_box[i])%256
    v5 = S_box[uu];
    S_box[uu] = S_box[vv];
    S_box[vv] = v5;                             // swap(S_box[uu],S_box[vv])
    v4 = S_box;                                 // 安全相关变量，可以忽视
    *(_BYTE *)(Data + i) ^= S_box[(unsigned __int8)(S_box[uu] + S_box[vv])];// Data[i]^=S_box[(S_box[uu]+S_box[vv])%256]
  }
  v7 = __readgsdword(0x14u);
  result = v7 ^ v12;
  if ( v7 != v12 )
    sub_806FA00(v4);
  return result;
}
```

## 识别特征：
算法在初始化 s 盒的时候有一个循环次数为 256 的操作

之后根据秘钥打乱 s 盒

最后输入铭文的每个字节，从我们的位置得到最后的结果。

## 解密需要的数据：
1. 我们的密钥
2. 我们加密后的数据
## C++实现：
```cpp
#include <iostream>
#include <string>
#include <array>
#include <vector>
#include <algorithm>
using namespace std;

class RC4 {
    public:
        explicit RC4(void) {};
        void reset(const vector<uint8_t> &key, size_t len);
        void crypt(const vector<uint8_t> &in, vector<uint8_t> &out, size_t len);
        ~RC4(void) {};

    private:
        array<uint8_t, 256> sbox;
        uint8_t idx1;
        uint8_t idx2;
        explicit RC4(const RC4&) = delete;
        explicit RC4(const RC4&&) = delete;
        const RC4& operator=(const RC4&) = delete;
        const RC4&& operator=(const RC4&&) = delete;
};

void RC4::reset(const vector<uint8_t> &key, size_t len) {
    uint8_t j = 0;

    for (auto i = 0; i < sbox.size(); i++)
        sbox[i] = i;
    idx1 = 0; idx2 = 0;

    for (auto i = 0; i < sbox.size(); i++) {
        j += sbox[i] + key[i % len];
        swap(sbox[i], sbox[j]);
    }
}

void RC4::crypt(const vector<uint8_t> &in, vector<uint8_t> &out, size_t len) {
    uint8_t j = 0;

    for (auto i = 0; i < len; i++) {
        idx1++; idx2 += sbox[idx1];
        swap(sbox[idx1], sbox[idx2]);
        j = sbox[idx1] + sbox[idx2];
        out[i] = in[i] ^ sbox[j];
    }
}

int main(int argc, char **argv) {
    if (argc != 3) {
        cout << "Usage: " << argv[0] << " <key> <message>" << endl;
        return 1;
    }

    auto keylen = string(argv[1]).length(), msglen = string(argv[2]).length();
    RC4 rc4; vector<uint8_t> key(keylen, 0), msg(msglen, 0);
    key.assign(argv[1], argv[1] + keylen);
    msg.assign(argv[2], argv[2] + msglen);

    cout << "message: ";
    for_each(msg.begin(), msg.end(), [] (uint8_t i) { cout << i; });
    cout << endl;
    rc4.reset(key, keylen);
    rc4.crypt(msg, msg, msglen);
    cout << "encrypt: ";
    for (auto i : msg) { cout << hex << (i >> 4); cout << hex << (i & 0x0f) << " "; }
    cout << endl;
    rc4.reset(key, keylen);
    rc4.crypt(msg, msg, msglen);
    cout << "decrypt: ";
    for_each(msg.begin(), msg.end(), [] (uint8_t i) { cout << i; });
    cout << endl;

    return 0;
}
```


## C++ 解密实现：
第一步：利用 Key初始化我们的 S 盒
第二步：把我们的输入流和我们 S 盒进行加密处理，处理的结果就是我们的解密结果。


```cpp
#include <stdio.h>
#include <string.h>
typedef unsigned long ULONG;
void rc4_init(unsigned char *s, unsigned char *key, unsigned long Len) //初始化函数
{
    int i =0, j = 0;
    char k[256] = {0};
    unsigned char tmp = 0;
    for (i=0;i<256;i++) {
        s[i] = i;
        k[i] = key[i%Len];
    }
    for (i=0; i<256; i++) {
        j=(j+s[i]+k[i])%256;
        tmp = s[i];
        s[i] = s[j]; //交换s[i]和s[j]
        s[j] = tmp;
    }
 }
void rc4_crypt(unsigned char *s, unsigned char *Data, unsigned long Len) //加解密
{
    int i = 0, j = 0, t = 0;
    unsigned long k = 0;
    unsigned char tmp=0;
    for(k=0;k<Len;k++) {
        i=(i+1)%256;
        j=(j+s[i])%256;
        tmp = s[i];
        s[i] = s[j]; //交换s[x]和s[y]
        s[j] = tmp;
        t=(s[i]+s[j])%256;
        Data[k] ^= s[t];
     }
}
unsigned int FindIndex(char *string,char c)
{
    int i;
    for(i=0; i<strlen(string); i++)
    {
        if(c == string[i]) return i;
    }
}
void base64_decode(unsigned char *input)
{
    unsigned char output[256] = {0};
    char *string = {"0123456789+/abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"};
    int index = 0;
    int input_length = strlen((char*)input);
    int i=0;
    for(i=0;i<input_length;i+=4)
    {
        if(i+1 >= input_length) break;
        output[index++] = (FindIndex(string,input[i])<<2) + (FindIndex(string,input[i+1])>>4);
        if(i+2 >= input_length) break;
        output[index++] = (FindIndex(string,input[i+1])<<4) + (FindIndex(string,input[i+2])>>2);
        if(i+3 >= input_length) break;
        output[index++] = (FindIndex(string,input[i+2])<<6) + (FindIndex(string,input[i+3]));
    }
    printf("result:");
    puts((char*)output);
    printf("HEX：");
    for(i=0;i<strlen((char*)output);i++)
    {
        printf("%x ",output[i]);
    }
    printf("\n");
}
int main()
{
    unsigned char s[257] = {0}; //S-box
    int i=0;
    unsigned char key[257] = {"Flag{This_a_Flag}\x00"};     //key
    unsigned char pData[] = {0xe8,0xd8,0xbd,0x91,0x87,0x1a,0x01,0x0e,0x56,0x0f,0x53,0xf4,0x88,0x96,0x82,0xf9,0x61,0x42,0x0a,0xf2,0xab,0x08,0xfe,0xd7,0xac,0xfd,0x5e,0x00};   //密文
    ULONG len = strlen((char*)pData);
    rc4_init(s,(unsigned char *)key,strlen((char*)key)); //已经完成了初始化
    rc4_crypt(s,(unsigned char *)pData,strlen((char*)pData));//加密
    for(int i=0; i<strlen((char*)pData); i++)
    {
        pData[i] ^= key[i % strlen((char*)key)];
    }
    printf("rc4 decrypt  : %s\n",pData);
    base64_decode(pData);
    return 0;
}
```