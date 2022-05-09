> 近期太忙没空更新博客，将就看下吧~

### 安全工具开发方向
1. [May I Help You 开发纪实](#may-i-help-you-开发纪实)
2. [个人博客版WAF思路](#个人博客版WAF思路)

### 数据挖掘方向
1. [商业爬虫原型机构建思路](#商业爬虫原型机构建思路)


### 算法


------
> ## May I Help You 开发纪实
2022年5月7日 定制化端口扫描框架思路

分为三个部分和一个核心：
- 核心是用来管理调度这三部分的程序
- 第一部分：
    - 通用端口
    - 特殊端口
- 第二部分：
    - 信息收集
    - 根据收集来的端口信息去各大网站上找漏洞和POC
    - 生成测绘图
        - 端口
        - 漏扫
        - 各大搜索引擎资料
- 第三部分 API：
    - http
    - RGP调试工具接入
    - 诱饵释放
        1. 真假ip
        2. 暴露白痴漏洞

-------

------
> ## 商业爬虫原型机构建思路
2021年3月8日 构建淘宝定向数据爬虫
1. 收集淘宝的指定词语的所有数据、重定向方式、拦截方式、各大框架绕过方式
2. 给收集到的信息分类（默认词云）
3. 给定向爬虫弄个线程池，可以get一个定向爬虫线程的方式获取爬虫
-------

------
> ## 个人博客版WAF思路
- 需求：集成化、省内存、高效、扩展强

- 核心思路：拦截一切看上去不正常的操作

- 扩展思路：
    1. 设置http tcp rpg 三种接口方式通过指令调用功能
    2. 配置一个基于RPG接口的debug客户端
    3. syslog收集日志
    4. 自带web界面，提供框架允许前端接入

-------