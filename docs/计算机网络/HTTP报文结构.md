## HTTP报文
HTTP通信过程包括客户端往服务器端发送请求以及服务器端给客户端返回响应两个过程。在这两个过程中就会产生请求报文和响应报文。

### 那么什么是HTTP报文呢？

HTTP报文是用于HTTP协议交互的信息，**HTTP报文本身是由多行数据构成的字符串文本**。客户端的HTTP报文叫做请求报文，服务器端的HTTP报文叫做响应报文。

### HTTP报文由哪几部分构成？各部分都有什么作用？

HTTP报文由**报文首部**和**报文主体**构成，中间由一个空行分隔。 报文首部是客户端或服务器端需处理的请求或响应的内容及属性， 可以传递额外的重要信息。报文首部包括请求行和请求头部，报文主体主要包含应被发送的数据。通常，不一定有报文主体。

HTTP报文首部的结构：由首部字段名和字段值构成的，中间用冒号“:”分割。首部字段格式： 首部字段名:字段值。

例如，在HTTP首部中以Content-Type这个字段来表示报文主体的对象类型：Content-Type:text/html。

上述的Content-Type是首部字段名，text/html是字段值，字段值可以是多个值，例如：Keep-Alive:timeout=15,max=10。

HTTP首部字段通常有4种类型：通用首部，请求首部，响应首部，实体首部。

通用首部字段：请求报文和响应报文两方都会使用的首部。

请求首部字段：从客户端向服务器端发送请求报文时使用的首部。补充了请求的附加内容、客户端信息、响应内容相关优先级等信息。

响应首部字段：从服务器端向客户端返回响应报文时使用的首部。补充了响应的附加内容，也会要求客户端附加额外的内容信息。

实体首部字段：针对请求报文和响应报文的实体部分使用的首部。补充了资源内容更新时间等和实体有关的信息。

HTTP报文结构如图：

报文首部
空行
报文主体
HTTP报文结构基本格式举例：

```
GET / HTTP/1.1
Host: www.enjoytoday.cn
Connection: keep-alive
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.84 Safari/537.36
Accept:text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Referer: http://www.enjoytoday.cn/posts/326
Accept-Encoding: gzip, deflate, sdch
Accept-Language: zh-CN,zh;q=0.8
Cookie: bdshare_firstime=1466032270994; UM_distinctid=15c4ef2ac4e2e4-0d13269271b947-1b2a120b-1fa400-15c4ef2ac4f7b5; un=aGZjYWk=; comment_author=aGZjYWk=; comment_author_email=1710600212@qq.com; comment_author_url=http://www.enjoytoday.cn; c_id=dUhIaTlndmc4MVVYbjRQTGxMRTotMTpFODg3QjgzQjg1NjgxQjQxRUYxNjg2QzJFRkMyQjI2QQ==; JSESSIONID=ADBC8C3DADF6C815D778450C193C6637.ajp13_worker; Hm_lvt_ce55bfda158556585a8b7b246346c8ba=1498560244,1498739070,1498833193,1498917432; Hm_lpvt_ce55bfda158556585a8b7b246346c8ba=1498917597; CNZZDATA1262047894=1598545996-1495973145-%7C1498917578

username=hfcai&sex=man2.
```

## 请求报文及响应报文的结构
###（1）HTTP请求报文
一个HTTP请求报文由请求行（request line）、请求头部（request header）、空行和请求数据4个部分构成。

请求行数据格式由三个部分组成：**请求方法、URI、HTTP协议版本**，他们之间用空格分隔。

该部分位于数据首行，基本格式为：

GET /index.html HTTP/1.1
该部分的请求方法字段给出了请求类型，URI给出请求的资源位置(/index.html)。HTTP中的请求类型包括:GET、POST、HEAD、PUT、DELETE。一般常用的为GET和POST方式。最后HTTP协议版本给出HTTP的版本号。

GET和POST的区别：

- （1）get是从服务器上获取数据，post是向服务器传送数据。

- （2）生成方式不同：

Get：URL输入；超连接；Form表单中method属性为get；Form表单中method为空。

Post只有一种：Form表单中method为Post。

- （3）数据传送方式：Get传递的请求数据按照key-value的方式放在URL后面，在网址中可以直接看到，使用?分割URL和传输数据，传输的参数之间以&相连，如：login.action?name=user&password=123。所以安全性差。

POST方法会把请求的参数放到请求头部和空格下面的请求数据字段就是请求正文（请求体）中以&分隔各个字段，请求行不包含参数，URL中不会额外附带参数。所以安全性高。

- （4）发送数据大小的限制：通常GET请求可以用于获取轻量级的数据，而POST请求的内容数据量比较庞大些。

Get：1~2KB。get方法提交数据的大小直接影响到了URL的长度，但HTTP协议规范中其实是没有对URL限制长度的，限制URL长度的是客户端或服务器的支持的不同所影响。

Post：没有要求。post方式HTTP协议规范中也没有限定，起限制作用的是服务器的处理程序的能力。

- （5）提交数据的安全：POST比GET方式的安全性要高。Get安全性差，Post安全性高。

通过GET提交数据，用户名和密码将明文出现在URL上，如果登录页面有浏览器缓存，或者其他人查看浏览器的历史记录，那么

就可以拿到用户的账号和密码了。安全性将会很差。



其中HTTP协议版本有两种：HTTP1.0/HTTP1.1、HTTP1.0/HTTP1.1的区别：

HTTP1.0对于每个连接都只能传送一个请求和响应，请求完服务器返回响应就会关闭，HTTP1.0没有Host字段。

而HTTP1.1在同一个连接中可以传送多个请求和响应，多个请求可以重叠和同时进行，HTTP1.1必须有Host字段。

请求头部紧跟着请求行，该部分主要是用于描述请求正文，其基本格式如下:

```
Host: www.enjoytoday.cn
Connection: keep-alive
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.84 Safari/537.36
......
```


主要是用于说明请求源、连接类型、以及一些Cookie信息等。

请求正文和请求头部通过一个空行进行隔开，一般用于存放POST请求类型的请求正文，如上的请求体为:

username=hfcai&sex=man
###（2）HTTP响应报文
HTTP响应报文由状态行（HTTP版本、状态码（数字和原因短语））、响应头部、空行和响应体4个部分构成。

其中响应头部和响应体同样也是通过一个空行进行隔开，如下为一个简单的示例：

```
	HTTP/1.1 200 OK
  Date: Sat, 01 Jul 2017 14:51:26 GMT
  Server: Apache/2.4.7 (Ubuntu)
  Set-Cookie: JSESSIONID=84C993F5E433C4DE9BFBA57150FFC065.ajp13_worker;path=/;HttpOnly
  Content-Language: zh-CN
  Vary: Accept-Encoding
  Content-Encoding: gzip
  Content-Length: 7333
  Keep-Alive: timeout=5, max=100
  Connection: Keep-Alive
  Content-Type: text/html;charset=UTF-8
  <html>
  <head>
  <title>title of html.</html>
  </head>
  <body>
  <h1>Hello world!</h1>
  </body>
  </html>
```

或者有一些浏览器响应头部在Header中显示，响应体在Reponse中显示。

状态行主要给出响应HTTP协议的版本号、响应返回状态码、响应描述，同样是单行显示。格式为：

HTTP/1.1 200 OK
状态码告知从服务器端返回的请求的状态，一般由一个三位数组成,分别以整数1～5开头组成。各个响应的类型对应的含义:

1XX 请求正在处理

2XX 请求成功 200 OK 正常处理  204 no content 请求处理成功但没有资源可返回 206 Partial Content 对资源的某一部分请求

3XX 重定向 301 Moved Permanenly请求资源的URI已经更新（永久移动），客户端会同步更新URI。

302 Found 资源的URI已临时定位到其他位置，客户端不会更新URI。

303 See Other 资源的URI已更新，明确表示客户端要使用GET方法获取资源。

304 Not Modified 当客户端附带条件请求访问资源时资源已找到但未符合条件请求。

307 Temporary Redirect临时重定向

4XX 客户端错误 400 Bad Request 请求报文中存在语法错误，一般为参数异常。401 Unauthorized 发送的请求需要HTTP认证。

403 Forbiddden 不允许访问，对请求资源的访问被服务器拒绝   404 Not Found 无法找到请求的资源，请求资源不存在。

405 请求的方式不支持。

5XX 服务器错误 500 Internal Server Error 服务器的内部资源出故障，服务器在执行请求时发生了错误。

503 Service Unavailable 服务器暂时处于超负载状态或正在进行停机维护，无法处理请求，服务器正忙。

响应头部主要是返回一些服务器的基本信息，以及一些Cookie值等。如上的响应头为:

```
Date: Sat, 01 Jul 2017 14:51:26 GMT
Server: Apache/2.4.7 (Ubuntu)
Set-Cookie: JSESSIONID=84C993F5E433C4DE9BFBA57150FFC065.ajp13_worker;path=/;HttpOnly
Content-Language: zh-CN
Vary: Accept-Encoding
Content-Encoding: gzip
Content-Length: 7333
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: text/html;charset=UTF-8响应体为请求需要得到的具体数据，可以为任何类型数据，一般网页浏览返回的为html文件内容，如上面的响应体为：
```

```
<html>
  <head>
  <title>title of html.</html>
  </head>
  <body>
  <h1>Hello world!</h1>
  </body>
 </html>
```



 