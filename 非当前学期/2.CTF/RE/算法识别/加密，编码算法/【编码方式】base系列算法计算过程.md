
## 简介


Base 系列的算法都是把我们的对应铭文转换为我们的一串数字
后，然后再对我们的某一个固定的数字进行取模，然后再根据取模的结果确定我们的对应的字母是多少，然后就有我们的答案了 (**比特级别**)

## 需要内容：
1. 待加密的数据流（）
2. Base 64 字符表

## 过程：
第一步：我们首先把我们的数据流转换为我们的二进制, 这一步，我们可以通过我们的下面方法实现：
```
byte4[0] = (byte3[0] & 0xfc) >> 2;
            byte4[1] = ((byte3[0] & 0x03) << 4) | ((byte3[1] & 0xf0) >> 4);
            byte4[2] = ((byte3[1] & 0x0f) << 2) | ((byte3[2] & 0xc0) >> 6);
            byte4[3] = byte3[2] & 0x3f;
```

第二步：我们把我们的二进制每 6 位取对应 base 64 表，然后得到的答案就是我们的加密结果

第三步：如果有不足的位置，我们
就直接补 0 后再添加。

```cpp
#include <iostream>
#include <string>
#include <vector>
const std::string base64_chars =
    "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    "abcdefghijklmnopqrstuvwxyz"
    "0123456789+/";
std::string base64_encode(const std::vector<uint8_t> &data) {
    std::string encoded;
    int i = 0, j = 0;
    uint8_t byte3[3] = {0};
    uint8_t byte4[4] = {0};
    for (uint8_t byte : data) {
        byte3[i++] = byte;
        if (i == 3) {
            byte4[0] = (byte3[0] & 0xfc) >> 2;
            byte4[1] = ((byte3[0] & 0x03) << 4) | ((byte3[1] & 0xf0) >> 4);
            byte4[2] = ((byte3[1] & 0x0f) << 2) | ((byte3[2] & 0xc0) >> 6);
            byte4[3] = byte3[2] & 0x3f;
            for (i = 0; i < 4; i++) {
                encoded += base64_chars[byte4[i]];
            }
            i = 0;
        }
    }
    if (i != 0) {
        for (int k = i; k < 3; k++) {
            byte3[k] = 0;
        }
        byte4[0] = (byte3[0] & 0xfc) >> 2;
        byte4[1] = ((byte3[0] & 0x03) << 4) | ((byte3[1] & 0xf0) >> 4);
        byte4[2] = ((byte3[1] & 0x0f) << 2) | ((byte3[2] & 0xc0) >> 6);
        for (int k = 0; k < i + 1; k++) {
            encoded += base64_chars[byte4[k]];
        }
        while (i++ < 3) {
            encoded += '=';
        }
    }
    return encoded;
}
int main() {
    std::vector<uint8_t> data = {'H', 'e', 'l', 'l', 'o', ',', ' ', 'B', 'a', 's', 'e', '6', '4', '!'};
    std::string encoded = base64_encode(data);
    std::cout << "Base64 Encoded: " << encoded << std::endl;
    return 0;
}
```

## 魔改方法：
1. 修改字母表。