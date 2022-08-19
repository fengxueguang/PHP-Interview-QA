

# http协议入门
## HTTP
HTTP（HyperText Transfer Protocol） 超文本传输协议，是一种详细规定了浏览器和万维网服务器之间互相通信的规则，它允许将HTML从Web服务器传送到Web浏览器。

通常是浏览器，与Web服务器的HTTP端口（默认80）建立一个TCP连接。

## 如何发起一个HTTP请求？
在浏览器地址栏输入一个URL，并访问。

URL:（统一资源定位符） 网址 且是互联网标准的地址

### URL标准格式：

协议://服务器IP[:端口]/路径/[?查询]
例如：https://mp.csdn.net/mp_blog/creation/editor

DNS可以将域名解析为服务器IP地址 默认端口号为：80

## HTTP工作原理
1、客户端连接到Web服务器 HTTP客户端，通常是浏览器，与Web服务器的HTTP端口（默认80）建立一个TCP连接。
2、发送HTTP请求 客户端向Web服务器发送一个请求报文，一个请求报文由请求行、请求头部、空行和请求数据组成。
3、服务器接受请求并返回HTTP响应 Web服务器解析请求，定位请求资源。服务器将资源复本写到TCP套接字，由客户端读取。一个响应由状态行、响应头部、空行和响应数据组成。
4、释放连接TCP连接 若connection 模式为close，则服务器主动关闭TCP连接，客户端被动关闭连接，释放TCP连接;若connection 模式为keepalive，则该连接会保持一段时间，在该时间内可以继续接收请求。
5、客户端浏览器解析HTML内容 客户端浏览器首先解析状态行，查看表明请求是否成功的状态代码。然后解析每一个响应头，响应头告知以下为若干字节的HTML文档和文档的字符集。客户端浏览器读取响应数据HTML，根据HTML的语法对其进行格式化，并在浏览器窗口中显示。

## HTTP协议详解
HTTP是一种无状态的协议，Web浏览器与Web服务器之间不需要建立持久的连接。HTTP请求只能由客户端发起，而数据不能从服务器主动发向客户端。

HTTP遵循请求(Request)/应答(Response)模型，Web浏览器向Web服务器发送请求时,Web服务器处理请求并返回适当的应答,如图 想访问资源=Request 返回资源=Response

## HTTP请求

HTTP请求包括3部分 请求行(请求方法 ) 请求头(消息报头) 请求正文

例如

POST /login.php HTTP/1.1             //请求行
HOST: www.xxser.com                  //请求头
User-Agent: Mozilla/5.0 (Windows NT 6.1;rv:15.0) Gecko/20100101 Firefox/15.0
                                     //空行，代表请求结束
Username=admin&password=admin        //请求正文                                     
1.请求行：三部分 1、说明请求方法 2、说明请求资源地址 3、说明协议版本

请求方法  /资源地址  协议版本
2.请求头：

头部字段名: 值
3.请求正文：

请求数据
HTTP响应

包括响应行 响应头(消息报头) 响应正文

例如

HTTP/1.1 200 OK                        /777777777777777777777777777777/响应行
DATE: Thu, 28 Feb 2021 09:36:47 GMT    //响应头  
Server: BWS /1.0 
Content-Length: 4199
Content-Type: text/html;charset=utf-8
Cache-Control: private 
Expires: Thu, 28 Feb 2021 09:36:47 GMT 
Content-Encoding: gzip
                                       //空白行，代表响应头结束
​
<html>                                 // 响应正文
  <head>
     <title>power!!<title>
  </head>
    ..........    
​



1. 响应行：
协议版本  状态码  状态码描述

2. 响应头：
头部字段名 :   值

3. 响应正文：
返回数据