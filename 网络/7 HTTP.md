[TOC]

### HTTP

#### TCP与HTTP

<img src="assets/tcp-and-http.jpg" style="zoom:67%;" />

#### URI与URL

##### 1. 概念

URI 包含 URL 和 URN。**URI** 的作用像**身份证号**一样，**URL** 的作用更像**家庭住址**一样。URL 是一种具体的 URI，它不仅唯一标识资源，而且还提供了定位该资源的信息。

<img src="assets/1563596175716.png" alt="1563596175716" style="zoom: 56%;" />

- 资源定位符 URL 是对可以从互联网上得到的资源的位置和访问方法的一种简洁表示。
- URL 给资源的位置提供一种抽象的识别方法，并用这种方法给资源定位。
- 只要能够对资源定位，系统就可以对资源进行各种操作，如存取、更新、替换和查找其属性。
- URL 相当于一个文件名在网络范围的扩展。因此 URL 是与互联网相连的机器上的任何可访问对象的一个指针。

##### 2. **URL的一般格式**

一般格式如下

```java
<协议>://<主机>:<端口>/<路径>
```

- **协议**：FTP：文件传输协议； HTTP：超文本传输协议。

- **://** ：固定格式。

- **主机**：存放资源的主机，在互联网中的域名或IP 地址。

- **端口路径**：指明资源具体路径。

##### 3. **万维网工作过程**

![1574746064533](assets/1574746064533.png)



#### 请求和响应报文

由于 HTTP 是面向正文的 (text-oriented)，因此在报文中的每一个字段都是一些 **ASCII 码串**，因而每个字段的长度都是不确定的。

##### 1. 请求报文

> **请求报文格式？**

<img src="assets/image-20191229152139723.png" alt="image-20191229152139723" style="zoom:50%;" />

GET 请求的参数直接在 URL 中，没有请求体，而 POST 请求的参数通常在请求体中。

如下图所示

```http
POST /search HTTP/1.1        
Accept: image/gif, image/x-xbitmap, image/jpeg, image/pjpeg, application/vnd.ms-excel, application/vnd.ms-powerpoint,
application/msword, application/x-silverlight, application/x-shockwave-flash, */*  
Referer: http://www.google.cn/  
Accept-Language: zh-cn  
Accept-Encoding: gzip, deflate  
User-Agent: Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; .NET CLR 2.0.50727; TheWorld)  
Host: www.google.cn
Connection: Keep-Alive  
Cookie: PREF=ID=80a06da87be9ae3c:U=f7167333e2c3b714:NW=1:TM=1261551909:LM=1261551917:S=ybYcq2wpfefs4V9g;
NID=31=ojj8d-IygaEtSxLgaJmqSjVhCspkviJrB6omjamNrSm8lZhKy_yMfO2M4QMRKcH1g0iQv9u-2hfBW7bUFwVh7pGaRUb0RnHcJU37y-
FxlRugatx63JLv7CWMD6UB_O_r  
										# 空行
hl=zh-CN&source=hp&q=domety  			# 请求体
```

##### 2. 响应报文

<img src="assets/image-20191229152226696.png" alt="image-20191229152226696" style="zoom: 50%;" />

```http
HTTP/1.1 200 OK
Date: Mon, 23 May 2005 22:38:34 GMT
Content-Type: text/html; charset=UTF-8
Content-Encoding: UTF-8
Content-Length: 138
Last-Modified: Wed, 08 Jan 2003 23:11:55 GMT
Server: Apache/1.3.3.7 (Unix) (Red-Hat/Linux)
ETag: "3f80f-1b6-3e1cb03b"
Accept-Ranges: bytes
Connection: close

<html>
	<head>
  		<title>An Example Page</title>
	</head>
	<body>
  		Hello World, this is a very simple HTML document.
	</body>
</html>
```



#### HTTP方法

客户端发送的  **请求报文**  第一行为请求行，包含了**方法**字段。

|    方法     |          说明          | 支持的HTTP协议版本 |
| :---------: | :--------------------: | :----------------: |
|   **GET**   |      **获取资源**      |      1.0、1.1      |
|  **POST**   |    传输实**体主体**    |      1.0、1.1      |
|   **PUT**   |        传输文件        |      1.0、1.1      |
|  **HEAD**   |    获得报文**首部**    |      1.0、1.1      |
| **DELETE**  |        删除文件        |      1.0、1.1      |
| **OPTIONS** |     询问支持的方法     |        1.1         |
|  **TRACE**  |        追踪路径        |        1.1         |
| **CONNECT** | 要求用隧道协议连接代理 |        1.1         |

LINK 和 UNLINK 已被 HTTP/1.1 **废弃**。

##### 1. GET

> 请求访问已被 URI 识别的资源。

当前网络请求中，绝大部分使用的是 GET 方法。

##### 2. POST

> 传输实体的主体。

POST 主要用来**传输数据**，而 GET 主要用来获取资源。

##### 3. PUT

> 上传文件。

由于自身不带验证机制，任何人都可以上传文件，因此**存在安全性**问题，一般**不使用**该方法。

```http
PUT /new.html HTTP/1.1
Host: example.com
Content-type: text/html
Content-length: 16

<p>New File</p>
```

##### 4. HEAD

> 获取**报文首部**

和 GET 方法类似，但是**不返回报文实体主体**部分。

主要用于确认 URL 的有效性以及资源更新的日期时间等。

##### 5. PATCH

> 对资源进行**部分修改**

PUT 也可以用于**修改资源**，但是只能**完全替代**原始资源，PATCH 允许**部分修改**。

```http
PATCH /file.txt HTTP/1.1
Host: www.example.com
Content-Type: application/example
If-Match: "e0023aa4e"
Content-Length: 100

[description of changes]
```

##### 6. DELETE

> 删除文件

与 PUT 功能相反，并且同样**不带验证**机制。

```http
DELETE /file.html HTTP/1.1
```

##### 7. OPTIONS

> 查询**支持的方法**

查询指定的 URI 能够**支持的方法**。

会返回 `Allow: GET, POST, HEAD, OPTIONS` 这样的内容。

##### 8. CONNECT

> 要求在与代理服务器通信时**建立隧道**

使用 **SSL**（Secure Sockets Layer，安全套接层）和 **TLS**（Transport Layer Security，传输层安全）协议把通信内容**加密**后经网络隧道传输。

要求与代理服务器通信时**建立隧道**，实现用隧道协议进行 TCP 通信。

```http
CONNECT www.example.com:443 HTTP/1.1
```

|                           请求                           |               响应                |
| :------------------------------------------------------: | :-------------------------------: |
| CONNECT proxy.hackr.cn:8080 HTTP/1.1 Host:proxy.hacky.cn | HTTP/1.1 200 OK(之后进入网络隧道) |

<img src="assets/1563596242925.png" alt="1563596242925" style="zoom:70%;" />

##### 9. TRACE

> **追踪路径**

服务器会将**通信路径**返回给客户端。

发送请求时，在 Max-Forwards 首部字段中填入数值，每经过一个服务器就会减 1，当数值为 0 时就停止传输。

通常不会使用 TRACE，并且它容易受到 **XST** 攻击（Cross-Site Tracing，跨站追踪）。



#### HTTP状态码

服务器返回的  **响应报文**  中第一行为**状态行**，包含了状态码以及原因短语，用来告知客户端请求的结果。常用 14 种。

| 状态码  |                 类别                 |            含义            |
| :-----: | :----------------------------------: | :------------------------: |
| **1XX** |  Informational（**信息性**状态码）   |     接收的请求正在处理     |
| **2XX** |      Success（**成功**状态码）       |      请求正常处理完毕      |
| **3XX** |   Redirection（**重定向**状态码）    | 需要进行附加操作以完成请求 |
| **4XX** | Client Error（**客户端错误**状态码） |     服务器无法处理请求     |
| **5XX** | Server Error（**服务器错误**状态码） |     服务器处理请求出错     |

##### 1. 1XX 信息

-  **100 Continue** ：表明到目前为止都很正常，客户端可以继续发送请求或者**忽略**这个响应。

##### 2. 2XX 成功

-  **200 OK** ：成功。
-  **204 No Content** ：请求已经**成功处理**，但是返回的响应报文**不包含实体的主体**部分。一般在只需要从客户端往服务器发送信息，而不需要返回数据时使用。
-  **206 Partial Content** ：表示客户端进行了**范围请求**，响应报文包含由 Content-Range 指定范围的实体内容。

##### 3. 3XX 重定向

-  **301 Moved Permanently** ：**永久性**重定向。
-  **302 Found** ：**临时性**重定向。
-  **303 See Other** ：和 302 有着相同的功能，但是 303 明确要求客户端应该采用 GET 方法获取资源。注：虽然 HTTP 协议规定 301、302 状态下重定向时不允许把 POST 方法改成 GET 方法，但是大多数浏览器都会在 301、302 和 303 状态下的重定向把 POST 方法改成 GET 方法。
-  **304 Not Modified** ：如果**请求报文**首部包含**一些条件**，例如：**If-Match，If-Modified-Since，If-None-Match，If-Range，If-Unmodified-Since**，如果**不满足**条件，则服务器会返回 **304 状态码**。（其实这与重定向**没有**关系）。
-  **307 Temporary Redirect** ：临时重定向，与 302 的含义类似，但是 307 要求浏览器**不会**把重定向请求的 POST 方法改成 GET 方法。

##### 4. 4XX 客户端错误

说明是客户端存在错误。

-  **400 Bad Request** ：请求报文中存在**语法错误**。

-  **401 Unauthorized** ：**未认证**。该状态码表示发送的请求需要有**认证信息**（BASIC 认证、DIGEST 认证）。如果之前已进行过一次请求，则表示用户认证失败。

-  **403 Forbidden** ：请求被服务器**拒绝**。

-  **404 Not Found** ：资源未找到。

##### 5. 5XX 服务器错误

-  **500 Internal Server Error** ：服务器正在**执行请求时**发生错误。
-  **503 Service Unavailable** ：服务器暂时处于**超负载**或正在进行**停机维护**，现在无法处理请求。



#### HTTP首部

有 4 种类型的首部字段：**通用首部字段、请求首部字段、响应首部字段和实体首部字段**。

表示方式：

```c
首部字段名：首部字段值
```

字段值对应单个 HTTP 首部字段可以有**多个**值：

```http
cache-control: public, max-age=0
```

各种**首部**字段及其含义如下（不需要全记，仅供查阅）：

##### 1. 通用首部字段

通用首部字段是请求与响应**都可以**使用的字段。

|    首部字段名     |                    说明                    |
| :---------------: | :----------------------------------------: |
| **Cache-Control** |             控制**缓存**的行为             |
|    Connection     | 控制不再转发给代理的首部字段、管理持久连接 |
|       Date        |           创建报文的**日期**时间           |
|      Pragma       |                报文**指令**                |
|      Trailer      |             报文末端的首部一览             |
| Transfer-Encoding |         指定报文主体的传输编码方式         |
|      Upgrade      |               升级为其他协议               |
|        Via        |            代理服务器的相关信息            |
|      Warning      |                  错误通知                  |

######  1. Cache-Control

通过指定首部字段 Cache-Control 的指令，就能操作**缓存**的工作机制。

指令的参数是可选的，多个指令之间通过“,”分隔。首部字段 Cache-Control 的指令可用于请求及响应时。

```http
Cache-Control: private, max-age=0, no-cache
```

**缓存请求指令**

| 指令                  | 参数   | 说明                           |
| --------------------- | ------ | ------------------------------ |
| **no-cache**          | 无     | 强制向源服务器**再次验证**     |
| **no-store**          | 无     | **不缓存**请求或响应的任何内容 |
| **max-age = [ 秒]**   | 必需   | 响应的最大Age值                |
| max-stale( = [ 秒])   | 可省略 | 接收已过期的响应               |
| **min-fresh = [ 秒]** | 必需   | 期望在指定时间内的响应仍有效   |
| no-transform          | 无     | 代理不可更改媒体类型           |
| only-if-cached        | 无     | 从缓存获取资源                 |
| cache-extension       | -      | 新指令标记（token）            |

换言之，无参数值的首部字段可以使用缓存。

从字面意思上很容易把 no-cache **误解**成为不缓存，但事实上 **no-cache 代表不缓存过期的资源**，缓存会向源服务器进行有效期确认后处理资源，也许称为 do-notserve-from-cache-without-revalidation 更合适。**no-store 才是真正地不进行缓存**，请读者注意区别理解。

###### **2. Connection**

- ① 控制**不再转发给代理**的首部字段

<img src="assets/image-20191229160240703.png" alt="image-20191229160240703" style="zoom:60%;" />

```http
Connection: 不再转发的首部字段名
```

- ② 管理**持久**连接

HTTP1.1 之前默认不是持久连接，如果要持久连接需要设置为 Keep-Alive。

```http
Connection : Keep-Alive
```

###### **3. Transfer-Encoding**

规定了传输报文主体时采用的**编码方式**（仅对分块传输编码有效）

###### **4. Upgrade**

用于检测 HTTP 协议及其他协议是否可使用**更高的版本**进行通信，其参数值可以用来指定一个完全不同的通信协议（仅限与客户端和临接服务器之间，因此使用首部字段 Upgrade 时还需要额外指定 connection：Upgrade ）

![image-20191229160604148](assets/image-20191229160604148.png)

使用 **WebSocket** 需要**切换协议**使用这个。

###### **5. Via**

为了追踪客户端和服务器之间的请求和响应报文的传输路径。当报文经过**代理或网关**时，会先在**首部字段 Via 中附加**该服务器的信息，然后在进行转发。多与 TRACE 方法一起使用。

![image-20191229160700159](assets/image-20191229160700159.png)

##### 2. 请求首部字段

是从客户端往服务器端发送**请求报文**中所使用的字段，用于补充请求的附加信息、客户端信息、对响应内容相关的优先级等内容。

|     首部字段名      |                      说明                       |
| :-----------------: | :---------------------------------------------: |
|     **Accept**      |        **用户**代理可处理的**媒体类型**         |
|   Accept-Charset    |                  优先的字符集                   |
|   Accept-Encoding   |                 优先的内容编码                  |
|   Accept-Language   |             优先的语言（自然语言）              |
|  **Authorization**  |      **Web 认证信息**（可存放 **Token**）       |
|       Expect        |              期待服务器的特定行为               |
|        From         |               用户的电子邮箱地址                |
|      **Host**       |               请求资源所在服务器                |
|    **If-Match**     |              比较实体标记（ETag）               |
|  If-Modified-Since  |               比较资源的更新时间                |
|    If-None-Match    |        比较实体标记（与 If-Match 相反）         |
|      If-Range       |      资源未更新时发送实体 Byte 的范围请求       |
| If-Unmodified-Since | 比较资源的更新时间（与 If-Modified-Since 相反） |
|    Max-Forwards     |                 最大传输逐跳数                  |
| Proxy-Authorization |         代理服务器要求客户端的认证信息          |
|        Range        |               实体的字节范围请求                |
|     **Referer**     |          对请求中 URI 的**原始获取方**          |
|         TE          |                传输编码的优先级                 |
|     User-Agent      |              HTTP 客户端程序的信息              |

###### **1. Accept**

通知服务器，用户代理能够处理的媒体类型及媒体类型的相对优先级，可以使用 q=num 来代表**权重的优先值**，权重值 num 的取值范围是0-1，可以精确到三位小数，1为权重最大值，默认为1.

> Accept: text/html,application/json;q=0.9, application/xml;q=0.8

###### **2. Accept-Charset**

首部字段可用来通知服务器用户代理支持的字符集及字符集的相对优先顺序，权重用法同 Accept 字段的 q。

###### **3. Accept-Encoding**

首部字段可用来通知服务器用户代理支持的内容编码及编码的优先级，可以一次性指定多种内容编码,采用权重q表示优先级。

```undefined
Accept-Encoding: gzip, deflate, cpmpress, identity
```

###### **4. Accept-Language**

首部字段用来告知服务器，用户代理能够处理的语言，采用权重 q 指定优先级

###### **5. Authorization**

用来告知服务器用户代理的**认证信息**， 用于验证用户身份的凭证。使用 Token 认证时，将 Token 放在这个首部字段下。

###### **6. Host**

指明请求服务器的**域名**， 及服务器所**监听的 Tcp 端口号**，如果没有给定端口，会自动使用被请求服务的默认端口， 用于告知服务器请求资源所处的服务器域名及端口号。**唯一一个==必须被包含==在请求内的首部字段**。如果一个 IP 对应多个域名， 那就用 Host 字段指明服务器主机。

----

###### 7. 条件请求

> 形如 **if-XXXX** 的请求首部字段，都是**条件请求**，服务器接收到附带的请求条件后，只有**当条件满足时**，服务器才会执行

###### **8. if-Match**

通常在请求方法为 GET 时，服务器仅在请求资源的 ETag 值为 if-Match 首部字段值之一时，才会返回资源，当请求方法为 PUT 时，才允许上传资源。ETag 为一份资源独一无二的实体标记，资源更新后实体标记值 ETag 也会更新。

###### **9. if-Modified-since**

通常该字段只用在 GET 请求中，如果资源在 if-Modified-Since 字段值日期之后发生更新，则服务器接受该请求， 否则会返回一个不带响应体的 304（Not Modified），用于确认代理或客户端本地资源的有效性。

###### **10. if-None-Match**

当且仅当服务器上没有任何资源的实体标记 ETag 值与该首部字段中列出的值相对应是，服务器才会返回所请求的资源，否则返回 304。

###### **11. if-Range**

范围请求。用来当满足条件时（当 if-range 字段值中的条件得到满足，通常是满足 last-Modified 或 ETag），是 Range 字段起作用，服务器返回 206 Partial Content，如果 if-Range 字段值中的条件没有得到满足，则作为正常处理返回 200 OK 的**全部**资源。

----

###### **12. Proxy-Authorization**

用于用户代理给代理服务器发送身份验证的凭证。

```jsx
Proxy-Authorization: Basic YWxhZGRpbjpvcGVuc2VzYW1l
```

###### **13. Range**

用于只需获取部分资源的**范围请求**，字段值表明服务器资源的指定范围。

```xml
Range: <unit>=<range-start>-<range-end>, <range-start>-<range-end>
```

###### **14. Referer**

可以根据 Referer 查看请求资源的是从**哪个页面**发起的。

###### **15. TE**

告知服务器客户端能够处理响应的传输编码方式以及相对优先级。

###### **16. User-Agent**

将创建请求的浏览器和用户代理信息等名称传达给服务器。**爬虫**必备。

##### 3. 响应首部字段

响应首部字段是由服务器端向客户端返回**响应报文**中所使用的字段，用于补充响应的附加信息、服务器信息、以及对客户端的附加要求信息。

|      首部字段名      |             说明             |
| :------------------: | :--------------------------: |
|    Accept-Ranges     |   是否接受字节**范围请求**   |
|       **Age**        |   推算资源**创建**经过时间   |
|       **ETag**       |      资源的**匹配信息**      |
|       Location       |   令客户端重定向至指定 URI   |
|  Proxy-Authenticate  | 代理服务器对客户端的认证信息 |
|     Retry-After      |   对再次发起请求的时机要求   |
|      **Server**      |  HTTP 服务器的**安装信息**   |
|       **Vary**       | 代理**服务器缓存**的管理信息 |
| **WWW-Authenticate** | 服务器对客户端的**认证信息** |

###### **1. Accept-Ranges**

用于告知客户端服务器能否处理范围请求。

```swift
Accept-Ranges: none | bytes    // none就是不能
```

###### **2. Age**

能告知客户端，源服务器在**多久前**创建了响应，字段值的单位为秒。

###### **3. ETag**

它是一种将资源以字符串的形式做唯一标识性的方式，服务器会为每份资源分配对应的ETag值，当资源更新时，ETag值也会更新。没有特定的生成算法，通常使用资源最后修改时间戳的哈希值，或散列或版本号。
用处： 1.防止资源的同时更新而导致的相互覆盖（空中碰撞） 2.缓存未更改的资源。

###### **4. Location**

指定需要将页面**重新定向**至的地址。

###### **5. Proxy-Authenticate**

会把由代理服务器所要求的认证信息发送给客户端。指定了获取代理服务器上的资源访问权限而采用的身份验证方式。代理服务器对请求进行验证，以便它进一步传递请求。

###### **6. Retry-After**

告知客户端应该在多久后可以再次发起请求。主要配合状态码 **503**（Service Unavailable）响应。

###### **7. Server**

首部字段 Server 告知客户端当前服务器上安装的 HTTP 服务器应用程序的信息。

###### **8. Vary**

当代理服务器接收到带有 Vary 首部字段指定获取资源的请求时，如果使用的 Accept-Language 字段的值相同，那么就直接从源服务器返回响应，反之，则需要先从源服务器获取资源后才能作为响应返回。

###### **9. WWW-Authenticate**

用于 HTTP 访问**认证**。它会告知客户端适用于访问请求的 URI 所指定资源的认证方案（Basic 或是 Digest）；定义了何种验证方式去获取对资源的连接。

#####  4. 实体首部字段

实体首部字段是包含在请求报文和响应报文中的**实体部分所使用的首部**，用于补充内容的更新时间与实体相关的信息。请求报文和响应报文中都含有的与实体相关的首部。

|    首部字段名    |            说明             |
| :--------------: | :-------------------------: |
|    **Allow**     | 资源可**支持的 HTTP 方法**  |
| Content-Encoding |   实体主体适用的编码方式    |
| Content-Language |     实体主体的自然语言      |
|  Content-Length  | 实体主体部分的大小（bites） |
| Content-Location |     替代对应资源的 URI      |
|   Content-MD5    |     实体主体的报文摘要      |
|  Content-Range   |     实体主体的位置范围      |
|   Content-Type   |     实体主体的媒体类型      |
|   **Expires**    | 实体主体**过期的日期**时间  |
|  Last-Modified   |   资源的最后修改日期时间    |

###### **1. ALlow**

由于枚举资源所支持的 HTTP 方法的**集合**，当服务器接收到不支持的HTTP方法时，会返回 **405**（Method Not Allowed ）作为响应返回，于此同时还会把**所有能支持**的 HTTP 方法写入首部字段 Allow 返回。

```http
Allow: GET, POST, HEAD
```

###### **2. Content-Encoding**

告知客户端服务器对实体的**主体部分**选用的内容**编码**方式。（内容编码是指在不丢失实体信息的前提下所进行的压缩）常用的有 gzip、compress、deflate、 identify;

###### **3. Content-Language**

告知客户端实体主体使用的自然语言。

```http
Content-Language: zh-CN
```

###### **4. Content-Location**

首部字段 Content-Location 给出与报文主体部分相对应的 URI。和首部字段 Location 不同，Content-Location 表示的是报文主体返回的资源对应的 URI。

###### **5. Content-type**

说明了实体主体部分的**媒体类型**，和首部字段 Accept 一样，字段值用 type/subtype 形式赋值

###### **6. Expires**

会将**资源失效的日期**告知客户端，缓存服务器在接收到含有首部字段 Expires 的响应后，会以缓存来应答请求，在 Expires 字段值指定的时间之前，响应的副本会一直保存，当超过指定的时间后，缓存服务器在请求发送过来时，会转向源服务器请求资源。



#### HTTP应用

##### 1. 用单台虚拟主机实现多个域名

HTTP/1.1 规范允许一台 HTTP 服务器搭建多个 web 站点。

即使物理层面只有一台服务器，但只要使用**虚拟主机**的功能，则可以假想已具有**多台服务器**。

在互联网上，域名通过 DNS 服务映射到 IP 地址（域名解析）之后访问目标网站。可见，当请求发送到服务器时，已经是以 IP 地址形式访问了。

所以，如果一台服务器内托管了 www.tricorder.jp 和 www.hackr.jp 这两个域名，当收到请求时就需要弄清楚究竟要访问哪个域名。一个 IP 地址可能对应多个域名。

在相同的 I P地址下，由于虚拟主机可以寄存多个不同主机名和域名的 web 网站，因此在发送 HTTP 请求时，必须在 **Host 首部**内完整指定主机名或域名的 **URI**。

##### 2. 代理、网关、隧道

这些应用程序和服务器可以将请求转发给通信线路上的下一站服务器，并且接收从那台服务器发送的响应再转发给客户端。

###### ① 代理

代理服务器的基本行为就是接收客户端发送的请求后**转发**给其他服务器。代理**不改变**请求 URI，会直接发送给前方持有资源的目标服务器。持有资源实体的服务器被称为**源服务器**。从源服务器返回的响应经过代理服务器后再传给客户端。

![image-20191229155243444](assets/image-20191229155243444.png)

每次 经过代理服务器转发请求或响应时，会**追加写入 Via 首部**信息。

代理一般按两种基准分类：一是是否使用缓存，二是是否会修改报文。

透明代理：不对报文做任何修改。

**缓存代理**：会预先将资源的副本缓存保存在代理服务器上，会定期检查资源的有效性。当代理再次接收到对相同资源的请求时，就可以不从源服务器那里获取资源，而是将之前缓存的资源作为响应返回。

![image-20191229155538500](assets/image-20191229155538500.png)

使用代理服务器的理由有：利用缓存技术减少网络带宽的流量，组织内部针对特定网站的访问控制，以获取访问日志为主要目的等等。即：

- **缓存**
- **负载均衡**
- **网络访问控制**
- **访问日志记录**

代理服务器分为**正向代理和反向代理**两种：

- 用户察觉得到正向代理的**存在**。

<img src="assets/1563596330167.png" alt="1563596330167" style="zoom:70%;" />

- 而反向代理一般位于**内部网络**中，用户察觉**不到**。

<img src="assets/1563596340136.png" alt="1563596340136" style="zoom:67%;" />

###### ② 网关

网关的工作机制和代理十分相似。而网关能使通信线路上的服务器提供**非 HTTP 协议**服务。利用网关能提供通信的安全性，因为可以在客户端与网关之间的通信线路上加密以确保连接的安全。

<img src="assets/image-20191229155707212.png" alt="image-20191229155707212" style="zoom:60%;" />

###### **③ 隧道**

隧道可按要求建立起一条与其他服务器的通信线路，届时使用 SSL 等加密手段进行通信。隧道的目的是确保客户端能与服务器进行安全通信。

隧道本身**不会去解析** HTTP 请求。也就是说，请求保持原样中转给之后的服务器。隧道会在通信双方断开连接时结束。

![image-20191229155737735](assets/image-20191229155737735.png)

##### 3. 连接管理

###### ① 短连接与长连接

在HTTP/1.0中默认使用短连接。也就是说，客户端和服务器每进行一次HTTP操作，就建立一次连接，任务结束就中断连接。当客户端浏览器访问的某个HTML或其他类型的Web页中包含有其他的Web资源（如JavaScript文件、图像文件、CSS文件等），每遇到这样一个Web资源，浏览器就会重新建立一个HTTP会话。

而从HTTP/1.1起，默认使用长连接，用以保持连接特性。使用长连接的HTTP协议，会在响应头加入这行代码：

```javascript
Connection:keep-alive
```

在使用长连接的情况下，当一个网页打开完成后，客户端和服务器之间用于传输HTTP数据的TCP连接不会关闭，客户端再次访问这个服务器时，会继续使用这一条已经建立的连接。Keep-Alive不会永久保持连接，它有一个保持时间，可以在不同的服务器软件（如Apache）中设定这个时间。实现长连接需要客户端和服务端都支持长连接。

HTTP协议的长连接和短连接，实质上是TCP协议的长连接和短连接。

当浏览器访问一个包含多张图片的 HTML 页面时，除了请求访问的 HTML 页面资源，还会请求图片资源。如果每进行一次 HTTP 通信就要新建一个 TCP 连接，那么开销会很大。

<img src="assets/image-20191229153710786.png" alt="image-20191229153710786" style="zoom:42%;" />

**长连接**只需要建立一次 TCP 连接就能进行**多次 HTTP 通信**。

长连接如下图所示

![image-20191229153753544](assets/image-20191229153753544.png)

- 从 **HTTP/1.1** 开始==**默认是长连接**==的，如果要断开连接，需要由**客户端或者服务器端提出断开**，使用 `Connection : close`；
- 在 HTTP/1.1 之前默认是**短连接**的，如果需要使用**长连接**，则使用 `Connection : Keep-Alive`。

###### ② 流水线

默认情况下，HTTP 请求是按**顺序**发出的，下一个请求只有在当前请求**收到响应之后**才会被发出。由于受到网络延迟和带宽的限制，在下一个请求被发送到服务器之前，可能需要等待很长时间。

**流水线**是在同一条长连接上连续发出请求，而不用等待响应返回，这样可以减少延迟。

![image-20191229153820798](assets/image-20191229153820798.png)

##### 4. Cookie

HTTP 协议是**无状态**的，主要是为了让 HTTP 协议尽可能简单，使得它能够处理大量事务。HTTP/1.1 引入 Cookie 来**保存状态信息**。

> 如何理解HTTP协议是无状态的？

```
HTTP协议是无状态的（stateless），指的是协议对于事务处理没有记忆能力，服务器不知道客户端是什么状态。也就是说，打开一个服务器上的网页和上一次打开这个服务器上的网页之间没有任何联系。HTTP是一个无状态的面向连接的协议，无状态不代表HTTP不能保持TCP连接，更不能代表HTTP使用的是UDP协议（无连接）。

缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快。
```

Cookie 是**服务器**发送到**用户浏览器**并保存在本地的一小块数据，它会在浏览器之后向同一服务器再次发起请求时被**携带**上，用于告知服务端两个请求是否来自同一浏览器。由于之后**每次请求都会需要携带** Cookie 数据，因此会带来额外的性能开销（尤其是在移动环境下）。

Cookie 曾一度用于客户端数据的存储，因为当时并没有其它合适的存储办法而作为唯一的存储手段，但现在随着现代浏览器开始支持各种各样的存储方式，Cookie 渐渐被**淘汰**。新的浏览器 API 已经允许开发者直接将数据存储到本地，如使用 **Web storage API**（本地存储和会话存储）或 **IndexedDB**。

###### ① 用途

- **会话状态管理**（如用户登录状态、购物车、游戏分数或其它需要记录的信息）
- **个性化设置（**如用户自定义设置、主题等）
- **浏览器行为跟踪**（如跟踪分析用户行为等）

###### ② 创建过程

服务器发送的**响应报文**包含 ==**Set-Cookie**== 首部字段，客户端得到响应报文后把 **Cookie** 内容保存到浏览器中。

```http
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: yummy_cookie=choco			// 设置cookie信息
Set-Cookie: tasty_cookie=strawberry		// 设置cookie信息

[page content]
```

客户端之后对同一个服务器发送请求时，会从浏览器中取出 Cookie 信息并**通过 Cookie 请求首部字段**发送给服务器。

```http
GET /sample_page.html HTTP/1.1
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry		// 请求时携带cookie信息
```

###### ③ 分类

- **会话期** Cookie：浏览器**关闭**之后它会被**自动删除**，也就是说它仅在会话期内有效。
- **持久性** Cookie：指定**过期时间**（**Expires**）或有效期（**max-age**）之后就成为了持久性的 Cookie。

```http
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;	// 指定了cookie的过期时间
```

###### ④ 作用域

Domain 标识指定了哪些**主机**可以接受 Cookie。如果不指定，默认为当前文档的主机（不包含子域名）。如果指定了 Domain，则一般包含子域名。例如，如果设置 Domain=mozilla.org，则 Cookie 也包含在子域名中（如 developer.mozilla.org）。

**Path 标识**指定了主机下的哪些路径可以接受 Cookie（该 URL 路径必须存在于请求 URL 中）。以字符 %x2F ("/") 作为路径分隔符，子路径也会被匹配。例如，设置 Path=/docs，则以下地址都会匹配：

- /docs
- /docs/Web/
- /docs/Web/HTTP

###### ⑤ HttpOnly

浏览器通过 `document.cookie` 属性可**创建新的 Cookie**，也可通过该属性访问**非 HttpOnly 标记**的 Cookie。

```js
document.cookie = "yummy_cookie=choco";
document.cookie = "tasty_cookie=strawberry";
console.log(document.cookie);
```

标记为 **HttpOnly** 的 Cookie **不能被 JavaScript 脚本调用**。**跨站脚本攻击 (XSS)** 常常使用 JavaScript 的 `document.cookie` API 窃取用户的 Cookie 信息，因此使用 HttpOnly 标记可以在一定程度上避免 ==**XSS 攻击**==。

```http
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
```

###### ⑥ Secure

标记为 Secure 的 Cookie 只能通过被 **HTTPS** 协议加密过的请求发送给服务端。即便设置了 Secure 标记，敏感信息也不应该通过 Cookie 传输，因为 Cookie 有其固有的不安全性，Secure 标记也无法提供确实的安全保障。

###### ⑦ Session

除了可以将用户信息通过 **Cookie 存储**在用户**浏览器**中，也可以利用 **Session 存储在服务器端**，存储在服务器端的信息更加安全。

Session 可以存储在服务器上的**文件、数据库或者内存**中。也可以将 Session 存储在 **Redis** 这种内存型数据库中，效率会更高。

使用 Session 维护用户登录状态的过程如下：

- 用户进行登录时，用户提交包含用户名和密码的表单，放入 HTTP **请求报文**中；
- 服务器验证该用户名和密码，如果正确则把用户信息存储到 **Redis** 中，它在 Redis 中的 Key 称为 **Session ID**；
- 服务器返回的响应报文的 **Set-Cookie** 首部字段包含了这个 **Session ID**，客户端收到响应报文之后将该 Cookie 值**存入浏览器**中；
- 客户端之后对同一个服务器进行请求时会包含该 **Cookie** 值，服务器收到之后提取出 **Session ID**，从 Redis 中取出用户信息，继续之前的业务操作。

应该注意 Session ID 的安全性问题，不能让它被恶意攻击者轻易获取，那么就不能产生一个容易被猜到的 Session ID 值。此外，还需要经常**重新生成** Session ID。在对安全性要求极高的场景下，例如**转账**等操作，除了使用 Session 管理用户状态之外，还需要对**用户进行重新验证**，比如重新输入密码，或者使用**短信验证码**等方式。

###### ⑧ 浏览器禁用 Cookie

此时无法使用 Cookie 来保存用户信息，**只能使用 Session**。除此之外，不能再将 Session ID 存放到 Cookie 中，而是使用 **URL 重写技术**，将 Session ID **作为 URL 的参数进行传递**。

###### ⑨ Cookie与Session选择

Cookie 和 Session **都是用来跟踪浏览器用户身份的会话方式**，但是两者的应用场景不太一样。

 **Cookie 一般用来保存用户信息** 比如 ①我们在 Cookie 中保存已经登录过得用户信息，下次访问网站的时候页面可以自动帮你登录的一些基本信息给填了；②一般的网站都会有保持登录也就是说下次你再访问网站的时候就不需要重新登录了，这是因为用户登录的时候我们可以存放了一个 Token 在 Cookie 中，下次登录的时候只需要根据 Token 值来查找用户即可(为了安全考虑，重新登录一般要将 Token 重写)；③登录一次网站后访问网站其他页面不需要重新登录。**Session 的主要作用就是通过服务端记录用户的状态。** 典型的场景是购物车，当你要添加商品到购物车的时候，系统不知道是哪个用户操作的，因为 HTTP 协议是无状态的。服务端给特定的用户创建特定的 Session 之后就可以标识这个用户并且跟踪这个用户了。

**Cookie 数据保存在客户端(浏览器端)，Session 数据保存在服务器端。**

Cookie 存储在客户端中，而Session存储在服务器上，相对来说 Session 安全性更高。如果要在 Cookie 中存储一些敏感信息，不要直接写入 Cookie 中，最好能将 Cookie 信息加密然后使用到的时候再去服务器端解密。

- Cookie 只能存储 **ASCII** 码字符串，而 Session 则可以存储**任何类型的数据**，因此在考虑数据**复杂**性时首选 Session；
- Cookie 存储在浏览器中，容易被恶意查看。如果非要将一些隐私数据存在 Cookie 中，可以将 **Cookie 值进行加密**，然后在服务器进行解密；
- 对于大型网站，如果用户所有的信息都存储在 Session 中，那么开销是非常大的，因此不建议将所有的用户信息都存储到 Session 中。

##### 5. 缓存

###### ① 优点

- 缓解服务器压力；
- 降低客户端获取资源的延迟：缓存通常位于**内存**中，读取缓存的速度更快。并且缓存服务器在地理位置上也有可能比源服务器来得近，例如浏览器缓存。

###### ② 实现方法

- 让**代理服务器**进行缓存；
- 让**客户端浏览器**进行缓存。

###### ③ Cache-Control首部字段

HTTP/1.1 通过 **Cache-Control** 首部字段来控制缓存。

**I 禁止进行缓存**

**no-store** 指令规定**不能**对请求或响应的任何一部分进行缓存。**禁止缓存**。

```http
Cache-Control: no-store
```

**II 强制确认缓存**

no-cache 指令规定缓存服务器需要先向源服务器验证缓存资源的有效性，只有当缓存资源有效时才能使用该缓存对客户端的请求进行响应。

```http
Cache-Control: no-cache
```

**III 私有缓存和公共缓存**

private 指令规定了将资源作为**私有缓存**，只能被单独用户使用，一般存储在**用户浏览器**中。

```http
Cache-Control: private
```

public 指令规定了将资源作为**公共缓存**，可以被多个用户使用，一般存储在**代理服务器**中。

```http
Cache-Control: public
```

**IV 缓存过期机制**

**max-age** 指令出现在**请求报文**，并且缓存资源的缓存时间小于该指令指定的时间，那么就能接受该缓存。

**max-age** 指令出现在**响应报文**，表示缓存资源在缓存服务器中保存的时间。

```http
Cache-Control: max-age=31536000
```

**Expires** 首部字段也可以用于告知缓存服务器该资源什么时候会过期。

```http
Expires: Wed, 04 Jul 2012 08:26:05 GMT
```

- 在 HTTP/1.1 中，会优先处理 max-age 指令；
- 在 HTTP/1.0 中，max-age 指令会被忽略掉。

###### ④ 缓存验证

需要先了解 **ETag** 首部字段的含义，它是资源的唯一标识。URL 不能唯一表示资源，例如 `http://www.google.com/` 有中文和英文两个资源，只有 **ETag** 才能对这两个资源进行**唯一标识**。

```http
ETag: "82e22293907ce725faf67773957acd12"
```

可以将缓存资源的 ETag 值放入 **If-None-Match** 首部，服务器收到该请求后，判断缓存资源的 ETag 值和资源的最新 ETag 值是否一致，如果一致则表示缓存资源有效，返回 304 Not Modified。

```http
If-None-Match: "82e22293907ce725faf67773957acd12"
```

**Last-Modified** 首部字段也可以用于缓存验证，它包含在源服务器发送的响应报文中，指示源服务器对资源的最后修改时间。但是它是一种弱校验器，因为只能精确到一秒，所以它通常作为 ETag 的备用方案。如果响应首部字段里含有这个信息，客户端可以在后续的请求中带上 If-Modified-Since 来验证缓存。服务器只在所请求的资源在给定的日期时间之后对内容进行过修改的情况下才会将资源返回，状态码为 200 OK。如果请求的资源从那时起未经修改，那么返回一个不带有实体主体的 304 Not Modified 响应报文。

```http
Last-Modified: Wed, 21 Oct 2015 07:28:00 GMT
```

```http
If-Modified-Since: Wed, 21 Oct 2015 07:28:00 GMT
```

###### 6. 内容协商

通过内容协商返回**最合适的内容**，例如根据浏览器的默认语言选择返回中文界面还是英文界面。

###### ① 类型

**I 服务端驱动型**

客户端设置特定的 **HTTP 首部字段**，例如 Accept、Accept-Charset、Accept-Encoding、Accept-Language，服务器根据这些字段返回特定的资源。

它存在以下问题：

- 服务器很难知道客户端浏览器的全部信息；
- 客户端提供的信息相当冗长（HTTP/2 协议的首部压缩机制缓解了这个问题），并且存在隐私风险（HTTP 指纹识别技术）；
- 给定的资源需要返回不同的展现形式，共享缓存的效率会降低，而服务器端的实现会越来越复杂。

**II 代理驱动型**

服务器返回 300 Multiple Choices 或者 406 Not Acceptable，客户端从中选出最合适的那个资源。

###### ② Vary

```html
Vary: Accept-Language
```

在使用内容协商的情况下，只有当缓存服务器中的缓存满足内容协商条件时，才能使用该缓存，否则应该向源服务器请求该资源。

例如，一个客户端发送了一个包含 Accept-Language 首部字段的请求之后，源服务器返回的响应包含 `Vary: Accept-Language` 内容，缓存服务器对这个响应进行缓存之后，在客户端下一次访问同一个 URL 资源，并且 Accept-Language 与缓存中的对应的值相同时才会返回该缓存。

##### 6. 编码

###### ① 压缩传输的内容编码

内容编码将==**实体主体**==进行**压缩**，从而减少传输的数据量。请求首部不压缩。

常用的内容编码有：gzip、compress、deflate、identity。

浏览器发送 Accept-Encoding 首部，其中包含有它所支持的压缩算法，以及各自的优先级。服务器则从中选择一种，使用该算法对响应的消息主体进行压缩，并且发送 Content-Encoding 首部来告知浏览器它选择了哪一种算法。由于该内容协商过程是基于编码类型来选择资源的展现形式的，响应报文的 Vary 首部字段至少要包含 Content-Encoding。

###### ② 分块传输编码

Chunked Transfer Encoding，可以把数据分割成多块，让浏览器逐步显示页面。

##### 7. 范围请求

如果网络出现中断，服务器只发送了一部分数据，范围请求可以使得客户端只请求服务器**未发送的那部分数据**，从而避免服务器重新发送所有数据。

###### ① Range

在请求报文中添加 **Range** 首部字段指定请求的**范围**。

```http
GET /z4d4kWk.jpg HTTP/1.1
Host: i.imgur.com
Range: bytes=0-1023
```

请求成功的话服务器返回的响应包含 **==206== Partial Content** 状态码。

```http
HTTP/1.1 206 Partial Content
Content-Range: bytes 0-1023/146515
Content-Length: 1024
...
(binary content)
```

###### ② Accept-Ranges

响应首部字段 **Accept-Ranges** 用于告知客户端是否能处理范围请求，可以**处理使用 bytes**，否则使用 **none**。

```http
Accept-Ranges: bytes
```

###### ③ 响应状态码

- 在请求成功的情况下，服务器会返回 **206** Partial Content 状态码。
- 在请求的范围越界的情况下，服务器会返回 **416** Requested Range Not Satisfiable 状态码。
- 在不支持范围请求的情况下，服务器会返回 **200** OK 状态码。

##### 8. 多部分对象集合

一份报文主体内可含有**多种类型**的实体同时发送，每个部分之间用 **boundary** 字段定义的分隔符进行分隔，每个部分都可以有首部字段。

例如，上传多个表单时可以使用如下方式：

```html
Content-Type: multipart/form-data; boundary=AaB03x	// 指定Boundary

--AaB03x
Content-Disposition: form-data; name="submit-name"

Larry
--AaB03x
Content-Disposition: form-data; name="files"; filename="file1.txt"
Content-Type: text/plain

... contents of file1.txt ...
--AaB03x--
```



#### HTTP/2.0

##### 1. HTTP/1.1新特性

- 默认是**长连接**
- 支持**流水线**
- 支持同时打开多个 TCP 连接
- 支持**虚拟主机**
- 新增状态码 100
- 支持**分块传输编码**
- 新增缓存处理指令 max-age

##### 2. HTTP/1.x 缺陷

HTTP/1.x 实现简单是以牺牲性能为代价的：

- 客户端需要使用**多个连接**才能实现并发和缩短延迟；
- **不会压缩请求和响应首部**，从而导致不必要的网络流量；
- 不支持有效的资源优先级，致使底层 TCP 连接的利用率低下。

##### 3. 二进制分帧层

HTTP/2.0 将**报文**分成 **HEADERS** 帧和 **DATA** 帧，它们都是二进制格式的。

<img src="assets/1563596477939.png" alt="1563596477939" style="zoom:62%;" />

在通信过程中，只会有**一个 TCP 连接**存在，它承载了任意数量的**双向数据流（Stream）**。

- 一个数据流（Stream）都有一个唯一标识符和可选的优先级信息，用于承载双向信息。
- **消息**（Message）是与逻辑请求或响应对应的完整的一系列帧。
- **帧**（Frame）是最小的通信单位，来自不同数据流的帧可以交错发送，然后再根据每个帧头的数据流标识符重新组装。

<img src="assets/1563596492865.png" alt="1563596492865" style="zoom:57%;" />

##### 4. 服务端推送

HTTP/2.0 在客户端请求一个资源时，会把相关的资源**一起**发送给客户端，客户端就不需要再次发起请求了。例如客户端请求 page.html 页面，服务端就把 script.js 和 style.css 等与之相关的资源一起发给客户端。

<img src="assets/1563596507274.png" alt="1563596507274" style="zoom:70%;" />

##### 5. 首部压缩

HTTP/1.1 的首部带有大量信息，而且每次都要重复发送。

HTTP/2.0 要求客户端和服务器同时维护和更新一个包含之前**见过的首部字段表**，从而避免了重复传输。

不仅如此，HTTP/2.0 也使用 **Huffman** 编码对首部字段进行**压缩**。

<img src="assets/1563596523232.png" alt="1563596523232" style="zoom:57%;" />



#### GET和POST比较

##### 1. 作用

GET 用于获取资源，而 POST 用于传输实体主体。

##### 2. 参数

GET 和 POST 的请求都能使用**额外的参数**，但是 GET 的参数是以查询字符串出现在 **URL** 中，而 POST 的参数存储在**实体主体**中。不能因为 POST 参数存储在实体主体中就认为它的安全性更高，因为照样可以通过一些抓包工具（Fiddler）查看。     

因为 **URL** 只支持 **ASCII** 码，因此 GET 的参数中如果存在中文等字符就需要先进行**编码**。例如 `中文` 会转换为 `%E4%B8%AD%E6%96%87`，而空格会转换为 `%20`。**POST** 参数支持**标准字符集**。

```
GET /test/demo_form.asp?name1=value1&name2=value2 HTTP/1.1
```

```
POST /test/demo_form.asp HTTP/1.1
Host: w3schools.com
name1=value1&name2=value2
```

##### 3. 安全

安全的 HTTP 方法不会改变服务器状态，也就是说它只是可读的。

GET 方法是**安全**的，而 POST 却不是，因为 POST 的目的是传送实体主体内容，这个内容可能是用户上传的表单数据，上传成功之后，服务器可能把这个数据存储到数据库中，因此状态也就发生了改变。

安全的方法除了 GET 之外还有：HEAD、OPTIONS。

不安全的方法除了 POST 之外还有 PUT、DELETE。

##### 4. 幂等性

幂等的 HTTP 方法，同样的请求被执行一次与连续执行多次的效果是一样的，服务器的状态也是一样的。换句话说就是，幂等方法不应该具有副作用（统计用途除外）。

所有的==**安全方法也都是幂等**==的。

在正确实现的条件下，**GET，HEAD，PUT 和 DELETE** 等方法都是**幂等**的，而 **POST** 方法**不是**。

GET /pageX HTTP/1.1 是幂等的，连续调用多次，客户端接收到的结果都是一样的：

```http
GET /pageX HTTP/1.1
GET /pageX HTTP/1.1
GET /pageX HTTP/1.1
GET /pageX HTTP/1.1
```

POST /add_row HTTP/1.1 不是幂等的，如果调用多次，就会增加多行记录：

```http
POST /add_row HTTP/1.1   -> Adds a 1nd row
POST /add_row HTTP/1.1   -> Adds a 2nd row
POST /add_row HTTP/1.1   -> Adds a 3rd row
```

**DELETE** /idX/delete HTTP/1.1 是**幂等**的，即使不同的请求接收到的状态码不一样：

```http
DELETE /idX/delete HTTP/1.1   -> Returns 200 if idX exists
DELETE /idX/delete HTTP/1.1   -> Returns 404 as it just got deleted
DELETE /idX/delete HTTP/1.1   -> Returns 404

```

##### 5. 可缓存

如果要对响应进行缓存，需要满足以下条件：

- 请求报文的 **HTTP 方法本身是可缓存**的，包括 GET 和 HEAD，但是 PUT 和 DELETE 不可缓存，POST 在多数情况下不可缓存的。
- 响应报文的**状态码是可缓存**的，包括：200, 203, 204, 206, 300, 301, 404, 405, 410, 414, and 501。
- 响应报文的 Cache-Control 首部字段没有指定不进行缓存。

##### 6. XMLHttpRequest

为了阐述 POST 和 GET 的另一个区别，需要先了解 XMLHttpRequest：

> XMLHttpRequest 是一个 API，它为客户端提供了在客户端和服务器之间传输数据的功能。它提供了一个通过 URL 来获取数据的简单方式，并且不会使整个页面刷新。这使得网页只更新一部分页面而不会打扰到用户。XMLHttpRequest 在 AJAX 中被大量使用。

- 在使用 XMLHttpRequest 的 POST 方法时，浏览器会先发送 Header 再发送 Data。但并不是所有浏览器会这么做，例如火狐就不会。
- 而 GET 方法 Header 和 Data 会一起发送。

> **就下面的找几个点和面试官侃侃而谈即可，不可能全部都记得，想到什么讲什么吧**

- GET 被强制服务器支持
- 浏览器对URL的长度有限制，所以GET请求不能代替POST请求发送大量数据
- GET请求发送数据更小
- GET请求是不安全的
- GET请求是幂等的
    - 幂等的意味着对同一URL的多个请求应该返回同样的结果
- POST请求不能被缓存
- POST请求相对GET请求是「安全」的
    - 这里安全的含义仅仅是指是非修改信息
- GET用于信息获取，而且是安全的和幂等的
    - 所谓安全的意味着该操作用于获取信息而非修改信息。换句话说，GET 请求一般不应产生副作用。就是说，它仅仅是获取资源信息，就像数据库查询一样，不会修改，增加数据，不会影响资源的状态。

- POST是用于修改服务器上的资源的请求
- 发送包含未知字符的用户输入时，POST 比 GET 更稳定也更可靠



**引申：说完原理性的问题，我们从表面上来看看GET和POST的区别：**

- GET是从服务器上获取数据，POST是向服务器传送数据。 GET和 POST只是一种传递数据的方式，GET也可以把数据传到服务器，他们的本质都是发送请求和接收结果。只是组织格式和数据量上面有差别，http协议里面有介绍
- GET是把参数数据队列加到提交表单的ACTION属性所指的URL中，值和表单内各个字段一一对应，在URL中可以看到。POST是通过HTTP POST机制，将表单内各个字段与其内容放置在HTML HEADER内一起传送到ACTION属性所指的URL地址。用户看不到这个过程。 因为GET设计成传输小数据，而且最好是不修改服务器的数据，所以浏览器一般都在地址栏里面可以看到，但POST一般都用来传递大数据，或比较隐私的数据，所以在地址栏看不到，能不能看到不是协议规定，是浏览器规定的。
- 对于GET方式，服务器端用Request.QueryString获取变量的值，对于POST方式，服务器端用Request.Form获取提交的数据。 没明白，怎么获得变量和你的服务器有关，和GET或POST无关，服务器都对这些请求做了封装  
- GET传送的数据量较小，不能大于2KB。POST传送的数据量较大，一般被默认为不受限制。但理论上，IIS4中最大量为80KB，IIS5中为100KB。 POST基本没有限制，我想大家都上传过文件，都是用POST方式的。只不过要修改form里面的那个type参数
- GET安全性非常低，POST安全性较高。 如果没有加密，他们安全级别都是一样的，随便一个监听器都可以把所有的数据监听到。



#### 参考资料

- **上野宣. 图解 HTTP[M]. 人民邮电出版社, 2014.**
- [MDN : HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP)
- [HTTP/2 简介](https://developers.google.com/web/fundamentals/performance/http2/?hl=zh-cn)
- [htmlspecialchars](http://php.net/manual/zh/function.htmlspecialchars.php)
- [Difference between file URI and URL in java](http://java2db.com/java-io/how-to-get-and-the-difference-between-file-uri-and-url-in-java)
- [How to Fix SQL Injection Using Java PreparedStatement & CallableStatement](https://software-security.sans.org/developer-how-to/fix-sql-injection-in-java-using-prepared-callable-statement)
- [浅谈 HTTP 中 Get 与 Post 的区别](https://www.cnblogs.com/hyddd/archive/2009/03/31/1426026.html)
- [Are http:// and www really necessary?](https://www.webdancers.com/are-http-and-www-necesary/)
- [HTTP (HyperText Transfer Protocol)](https://www.ntu.edu.sg/home/ehchua/programming/webprogramming/HTTP_Basics.html)
- [Web-VPN: Secure Proxies with SPDY & Chrome](https://www.igvita.com/2011/12/01/web-vpn-secure-proxies-with-spdy-chrome/)
- [File:HTTP persistent connection.svg](http://en.wikipedia.org/wiki/File:HTTP_persistent_connection.svg)
- [Proxy server](https://en.wikipedia.org/wiki/Proxy_server)
- [What Is This HTTPS/SSL Thing And Why Should You Care?](https://www.x-cart.com/blog/what-is-https-and-ssl.html)
- [What is SSL Offloading?](https://securebox.comodo.com/ssl-sniffing/ssl-offloading/)
- [Sun Directory Server Enterprise Edition 7.0 Reference - Key Encryption](https://docs.oracle.com/cd/E19424-01/820-4811/6ng8i26bn/index.html)
- [An Introduction to Mutual SSL Authentication](https://www.codeproject.com/Articles/326574/An-Introduction-to-Mutual-SSL-Authentication)
- [The Difference Between URLs and URIs](https://danielmiessler.com/study/url-uri/)
- [Cookie 与 Session 的区别](https://juejin.im/entry/5766c29d6be3ff006a31b84e#comment)
- [COOKIE 和 SESSION 有什么区别](https://www.zhihu.com/question/19786827)
- [Cookie/Session 的机制与安全](https://harttle.land/2015/08/10/cookie-session.html)
- [HTTPS 证书原理](https://shijianan.com/2017/06/11/https/)
- [What is the difference between a URI, a URL and a URN?](https://stackoverflow.com/questions/176264/what-is-the-difference-between-a-uri-a-url-and-a-urn)
- [XMLHttpRequest](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)
- [XMLHttpRequest (XHR) Uses Multiple Packets for HTTP POST?](https://blog.josephscott.org/2009/08/27/xmlhttprequest-xhr-uses-multiple-packets-for-http-post/)
- [Symmetric vs. Asymmetric Encryption – What are differences?](https://www.ssl2buy.com/wiki/symmetric-vs-asymmetric-encryption-what-are-differences)
- [Web 性能优化与 HTTP/2](https://www.kancloud.cn/digest/web-performance-http2)
- [HTTP/2 简介](https://developers.google.com/web/fundamentals/performance/http2/?hl=zh-cn)
- https://www.jianshu.com/p/14cd2c9d2cd2
