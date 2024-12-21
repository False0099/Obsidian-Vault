首先，我们把我们的一个程序进行我们的 UPX 脱壳后拖入 IDA，我们得到下面的结果（已经经过处理）：
```cpp
int Main()
{
  int v0; // eax
  int result; // eax
  int v2; // ecx
  unsigned int v3; // et1
  unsigned int i; // [esp+Ch] [ebp-FCh]
  unsigned int CharsinintSize; // [esp+10h] [ebp-F8h]
  unsigned int Sizeof_Input_With_Base64; // [esp+14h] [ebp-F4h]
  int charsinint; // [esp+1Ah] [ebp-EEh]
  int v8; // [esp+1Eh] [ebp-EAh]
  int v9; // [esp+22h] [ebp-E6h]
  int v10; // [esp+26h] [ebp-E2h]
  __int16 charsinintend; // [esp+2Ah] [ebp-DEh]
  char Input_With_Base64[30]; // [esp+2Ch] [ebp-DCh]
  int CipherStart; // [esp+4Ah] [ebp-BEh]
  int v14; // [esp+4Eh] [ebp-BAh]
  int v15; // [esp+52h] [ebp-B6h]
  int v16; // [esp+56h] [ebp-B2h]
  int v17; // [esp+5Ah] [ebp-AEh]
  int v18; // [esp+5Eh] [ebp-AAh]
  int v19; // [esp+62h] [ebp-A6h]
  int v20; // [esp+66h] [ebp-A2h]
  int v21; // [esp+6Ah] [ebp-9Eh]
  int v22; // [esp+6Eh] [ebp-9Ah]
  int v23; // [esp+72h] [ebp-96h]
  int v24; // [esp+76h] [ebp-92h]
  __int16 CipherEnd; // [esp+7Ah] [ebp-8Eh]
  char Input; // [esp+7Ch] [ebp-8Ch]
  unsigned int v27; // [esp+FCh] [ebp-Ch]

  v27 = __readgsdword(0x14u);
  charsinint = 'galF';                          // v7是一个字符串数组
  v8 = 'ihT{';
  v9 = '_a_s';
  v10 = 'galF';
  charsinintend = '}';
  CharsinintSize = strlen(&charsinint);
  CipherStart = '8D8E';
  v14 = '19DB';
  v15 = 'A178';
  v16 = '65E1';
  v17 = 'F35F';
  v18 = '9884';
  v19 = 'F286';
  v20 = '4169';
  v21 = '2FA2';
  v22 = 'F8BA';
  v23 = 'A7DE';
  v24 = '5DFC';
  CipherEnd = 'E';
  printf((int)"Please input your flag:");
  read(0, &Input, 256);
  if ( strlen(&Input) != 21 )
    exit(0);
  v0 = Base64((int)&Input);
  Strcat((int)Input_With_Base64, v0);
  Sizeof_Input_With_Base64 = strlen(Input_With_Base64);
  for ( i = 0; i < Sizeof_Input_With_Base64; ++i )
    Input_With_Base64[i] ^= *((_BYTE *)&charsinint + i % CharsinintSize);// Input_With_Base64[i]^=Charinint[i%CharinintSize]
  RC4((int)Input_With_Base64, Sizeof_Input_With_Base64, (int)&charsinint, CharsinintSize);
  if ( !Strcmp(Input_With_Base64, &CipherStart) )
    exit(0);
  printf((int)"right!");
  result = 0;
  v3 = __readgsdword(0x14u);
  v2 = v3 ^ v27;
  if ( v3 != v27 )
    sub_806FA00(v2);
  return result;
}
```

可以看到，我们的流程如下所示：
原始数据-> `Base64` -> `RC4` 加密->异或加密

于是，我们解密的流程就可以如下所示：
密文与我们的 flag 异或->RC 4 解密->Base 64 解密

脚本如下所示：
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