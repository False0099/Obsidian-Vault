## 初始化：
```python
self.driver=webdriver.Chrome()
self.driver.get(self.url);#获取我们的当前网页的信息
```

## 获取信息：
Selenium 种获取定位信息的方法存在下面的几种：
第一种，通过我们的 ID 获得我们的信息。
假设我们通过 F 12 看到我们有一条这样的标签：
![[Pasted image 20240305085542.png]]
那么我们就可以通过下面的语句来获得我们的信息
```python
img_tag=self.driver.find_element(By.ID,'livenews-id-1-202301272620080422')
```

第二种：通过我们的 XSS 获取我们的标签：
假设我们需要爬取的信息没有 ID，我们就需要去复制它的 XSS 标签，然后再去获得我们的信息。
![[Pasted image 20240305085943.png]]
```python
loginName=self.driver.find_element(By.XPATH,'//*[@id="loginName"]')
```

## 爬取数据：
对于我们的一个数据，我们在获得了标签之后，就可以通过我们的 `get_attribute` 函数来获得我们标签内指定下标后的内容。

例如：我们想要获得学校验证码的对应图片，我们的方法就是访问当中的 src 下标。

![[Pasted image 20240305090434.png]]

```python
src = img_tag.get_attribute('src')
```


## 发送数据：
假设我们现在处于一个扽故状态，需要向我们的元素种发送信息，那么我们就可以通过我们的下面的函数来实现：
例如：
![[Pasted image 20240305090546.png]]
```python
name_ele = self.driver.find_element(By.XPATH, '//input[@id="loginName"]')

        name_ele.send_keys(self.name)

        pswd_ele = self.driver.find_element(By.XPATH, '//input[@id="loginPwd"]')

        pswd_ele.send_keys(self.pswd)

        vcode_ele = self.driver.find_element(By.XPATH, '//input[@id="verifyCode"]')

        vcode_ele.send_keys(vcode)
```

## 进行交互：
![[Pasted image 20240305090800.png]]

```cpp
        login_ele = self.driver.find_element(By.XPATH, '//button[@id="studentLoginBtn"]')

        login_ele.click()

        time.sleep(1)
```

