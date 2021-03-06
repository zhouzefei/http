###HTTP与服务端通信
---
> #####事物：一个HTTP事物由一条（从客户端发往服务器的）请求和一个（从服务器发回客户端的）的响应结果组成。这种通信通过HTTP报文的格式化数据块进行。

#####当在浏览器输入URL到浏览器呈现页面内容的过程中发生了什么？
简单的来讲就是从url中分离ip地址和端口号，建立tcp连接，发起报文，读取响应，关闭连接。

我们来看看具体的过程：
<center>
![Alt text](https://github.com/zhouzefei/http/blob/master/images/1.png)
![Alt text](https://github.com/zhouzefei/http/blob/master/images/2.png)
</center>

#####应用层：
应用层决定了向用户提供应用服务时通信的活动。如FTP(File Transfer Protocol,文件传输协议)和DNS(Domain Name System，域名系统)服务。HTTP协议也处于这一层。（https就是在http与tcp之间加了一个tls或者ssl的安全层）
![Alt text](https://github.com/zhouzefei/http/blob/master/images/3.png)


####补充一下：DNS查询

* 浏览器检查域名是否在缓存当中
* 如果缓存中没有，就去调用 gethostbyname 库函数（操作系统不同函数也不同）进行查询
* gethostbyname 函数在试图进行DNS解析之前首先检查域名是否在本地 Hosts 里，Hosts 的位置 不同的操作系统有所不同
* 如果 gethostbyname 没有这个域名的缓存记录，也没有在 hosts 里找到，它将会向 DNS 服务器发送一条 DNS 查询请求。DNS 服务器是由网络通信栈提供的，通常是本地路由器或者 ISP 的缓存 DNS 服务器。

<br/>

#####传输层：
这一层信息传送的协议数据单元称为段或报文。传输层为两个端系统的会话层之间，提供建立、维护和取消传输连接的功能，将上层数据分段并提供端到端的数据传输以及端到端的差错控制和流量控制。包含的协议：tcp、udp。重要设备网关。三次握手（三次握手完成之后客户端与服务器才正式开始传送数据）是为了准确无误地将数据送达目标处。保持tcp连接的正确运行通过源ip地址、源端口号、目的IP地址、目的端口号定义一条唯一的连接；
![Alt text](http://images2015.cnblogs.com/blog/729721/201601/729721-20160122135057734-1065153150.png)

#####网络层：
基本数据单位为ip数据报。处理网络上流动的数据包，规定哪条传输线路将数据包传送到对方计算机。重要设备路由器。ip地址和mac地址。
IP协议的作用是把各种数据包传送给对方，而要保证确实传送到对方那里。其中两个重要的条件是IP地址和MAC地址。IP地址指明了节点被分配到的地址，MAC地址是指网卡所属的固定地址。在中转时会利用下一站中转设备的mac地址搜索下一个中转目标。

譬如：我要寄一个快递，我只要把我的货物放到集散中心，就可以知道快递公司是否肯收件发货。快递公司的集散中心检查货物的送达地址，明确下一站送往哪个集散中心。

![Alt text](http://images2015.cnblogs.com/blog/729721/201601/729721-20160122135049468-988334811.png)

#####数据链路层：
为网络层提供可靠的数据传输。基本数据单位为帧，以太网协议。重要设备：网桥和交换机
#####物理层：
提供一个传输数据可靠的物理媒体。物理层确保原始的数据可以在各种无力媒体上传输。设备：中继器和集线器。

各种协议与HTTP协议的关系：
![Alt text](http://images2015.cnblogs.com/blog/729721/201601/729721-20160122135130797-400222415.png)

<br/>
备注：套接字－socket
TCP会遇到同时为多个应用程序进程提供并发服务的问题，多个tcp连接或多个应用程序进程可能需要通过同一个TCP协议端口传输数据。为了区别不同的应用程序进程和连接，许多操作系统为应用程序和tcp/ip协议交互提供了套接字接口。应用层可以和传输层通过socket接口区分不同应用程序。<font color='#f00'>通常情况下Socket连接就是TCP连接，因此Socket连接一旦建立，通信双方即可开始相互发送数据内容，直到双方连接断开。</font>

---

>请求报文和响应报文

HTTP报文是简单的格式化数据块。由对报文描述的起始行，包含属性的首部，可选的、包含数据的主体。
<center>![Alt text](https://github.com/zhouzefei/http/blob/master/images/3.png)
![Alt text](https://github.com/zhouzefei/http/blob/master/images/4.png)
</center>


起始行：请求行，响应行，方法，状态码，原因短语（ok），版本号
首部：名/值对的列表
实体：HTTP要传输的内容
<center>
![Alt text](https://github.com/zhouzefei/http/blob/master/images/5.png)
</center>
####常见状态码：
1xx: 信息性状态码 100, 101
100: 接收到请求的初始部分，请客户端继续。发送这个状态码服务器在接收到请求之后必须响应。Expect

----------
2xx: 成功状态码 200：OK
201: 用于创建服务器对象的请求（如：put）
202: 请求已被接受，但服务器还未对其执行任何动作。不能保证服务器会完成这个请求，只是意味者接受请求时是有效的。
<font color="#f00">203: 实体首部包含的信息不实来源于源端服务器，而是来自资源副本。</font>
204: 响应报文中包含若干首部和状态行，但没有实体部分。主要用于浏览器不转为显示新文档的情况下，对其进行更新（比如刷新一个表单页面）。
205: 告知浏览器清除当前页面中所有html表单元素
<font color="#f00">206: 成功执行一个部分或Range请求，范围请求成功。</font>

----------
3xx: 重定向状态码
<font color="#f00">301: 请求url已被移除，响应的location首部应包含资源现在所处的url。</font>
<font color="#f00">302: 临时重定向，使用location首部给的url来临时定位资源，以后还使用老的url。</font>
303:用另一个url来获取资源，目的允许post请求的响应将客户端定向到某个资源上去。
<font color="#f00">304: Not Modified 未修改，资源未被修改，告诉浏览器不用请求该资源，直接使用本地的资源即可。</font>
305: 必须通过一个代理来访问资源。代理的位置由location首部给出。客户端是相对某个特定资源来解析这条响应。
307:与301类似，但客户端应该使用location首部给出的url来临时定位资源，将来的请求应该使用老的url。

----------
4xx: 客户端错误状态码
400: 告知客户端发送错误请求
<font color="#f00">401: 获取资源访问权之前进行认证，未授权</font>
<font color="#f00">403: 服务器拒绝请求。</font>
<font color="#f00">404: Not Found 请求的URL资源并不存在</font>
405: 发起请求中带有所请求的url不支持的方法时，应该在响应中包含<font color="#f00">allow首部</font>告知客户端所请求的资源可以使用哪些方法。
406: 客户端指定参数来说明它们愿接受什么类型的实体，服务器没有与客户端可接受的url相匹配的资源时
407: 与401状态码类似，但用于要求对资源进行认证的代理服务器
408: 如果客户端完成请求所花的时间太长，服务器可以回送此状态码，并关闭连接。
409: 请求可能在资源上引发的一些冲突，服务器担心请求会引发冲突发送此状态码，响应中应包含描述冲突的主体。
410: 与404类似，只是服务器曾经拥有过此资源。用于web站点维护。
411: 服务器要求在请求报文中包含<font color="#f00">content-length,当服务器接受到包含了无效内容长度标头的字段时会返回此错误代码</font>使用。
412: 客户端发起条件请求，且其中一个条件失败了的时候使用。客户端包含Excpet首部发起就是条件请求。
413: 客户端发送的实体主体部分比服务器能够活着希望处理的要大时，用此状态码
414: 客户端所发请求中的请求url比服务器能够或者希望处理的要长时，使用此状态码。
415: 服务器无法理解或无法支持客户端所发实体的内容类型时，使用此状态码
416: 请求报文所请求的是指定资源的某个范围，而此范围无效或无法满足时，使用此状态码
417: 请求的expect请求首部包含了一个期望，但服务器无法满足此期望时，使用此状态码。

----------
5xx: 服务器端错误状态码
<font color="#f00">500: Internal Server Error 服务器内部错误</font>
501: 客户端发起的请求超出服务器能力范围
502: Bad Gateway 前面代理服务器联系不到后端的服务器时出现
<font color="#f00">503: 服务器现在无法为请求提供服务，但将来可以。如果服务器知道什么时候资源变为可用，在响应中包含一个retry-after首部。</font>
504: 与408类似。响应来自一个网关或代理，在等待另一服务器对其请求进行响应时超时了。
505: 服务器收到的请求使用了它无法或不愿支持的协议版本时


####常见的首部（通用首部，请求首部，响应首部和实体首部）
<center>![Alt text](https://github.com/zhouzefei/http/blob/master/images/6.png)</center>

#####通用首部：请求报文和响应报文都会使用。
| 首部|描述|
|:------------- |:-------------|
|<font color=#ff0000>Cache-Control</font>|控制缓存行为|
|Connection| 是否需要持久连接|
|Date|报文创建时间|
|Pragma|报文指令|
|<font color=#ff0000>Trailer</font>|报文末端的首部一览|
|<font color=#ff0000>Transfer-Encoding</font>|指定报文主体的传输编码方式|
|<font color=#ff0000>Via</font>|报文经过的中间节点|
|<font color=#ff0000>Upgrate</font>|发送端可能想要升级使用的新版本或协议|
|Warning|错误通知|

#####1.Cache-Control
请求：
![Alt text](https://github.com/zhouzefei/http/blob/master/images/8.png)

响应：
![Alt text](https://github.com/zhouzefei/http/blob/master/images/9.png)
max-age=0
![Alt text](https://github.com/zhouzefei/http/blob/master/images/10.png)

####2.Trailer
事先说明在报文主体后记录了哪些首部字段。在http1.1中分块传输编码。
![Alt text](https://github.com/zhouzefei/http/blob/master/images/11.png)

####3.upgrade
检测HTTP协议与其它协议是否可使用更高的版本进行通信。
![Alt text](https://github.com/zhouzefei/http/blob/master/images/12.png)

####4.via
![Alt text](https://github.com/zhouzefei/http/blob/master/images/13.png)



#####请求报文首部：

| 首部|描述|
|:------------- |:-------------|
|<font color=#ff0000>Accept</font>|希望接受的类型。MIME类型|
|Accept-Encoding|申明可接收的编码方法，通常指定压缩方法。|
|Accept-Language|浏览器申明自己接收的语言|
|Accept-Charset|浏览器可接受的字符集|
|User-Agent|客户端使用的操作系统和浏览器的名称和版本|
|<font color=#ff0000>Authorization</font>|web认证信息401|
|Expect|请求的特定的服务器行为，Expect: 100-continue|
|From|请求发送者的email地址|
|Host|发送请求时，<font color=#ff0000>该头域是必需的</font>。用于指定被请求资源的Internet主机和端口号。因为虚拟主机运行在同一饿ip上|
|IF-Match|只有请求内容与实体相匹配才有效|
|<font color=#ff0000>If-Modified-Since</font>| 比较资源更新时间（Last-Modified），如果一致返回304，读取本地缓存文件，不一致从服务器上读取心的文件内容。get请求。见(图If-Modified-Since)  |
|<font color=#ff0000>If-None-Match</font>| If-None-Match配合ETag使用，response中添加ETag信息。当用户再次请求该资源时，在请求头If-None-Match中加入ETag值，如果服务器验证资源etag没有改变则304，否则加入新的资源和etag。以提高网站性能。见(图If-None-Match)|
|<font color=#ff0000>IF-Range</font>|如果实体未改变，服务器发送客户端丢失的部分，否则发送整个实体。参数也为Etag。如果请求报文中的Etag与服务器目标内容的Etag相等，即没有发生变化，那么应答报文的状态码为206。如果服务器目标内容发生了变化，那么应答报文的状态码为200。|
|Range|如果服务器支持范围请求，就请求资源的指定范围|
|IF-Unmodified-Since|只在实体在指定时间之后未被修改才请求成功。与if-modified-since相反|
|Proxy-Authorization|与代理进行认证时使用|
|Referer|包含一个URL，用户从该URL代表的页面出发访问当前请求的页面|
|<font color=#ff0000>max-Forward</font>|在通往源端服务器的路径上将请求转发给其它代理或网关的最大次数，与TRACE一起使用|
|Proxy-Authorization|与代理进行认证时使用|
|Proxy-Connection|与代理建立连接时使用|
|TE|HTTP客户端程序的信息。与Accept-Encoding功能相似，但是用于传输编码，还可以伴随trailer字段的分块传输编码方式。如果用trailer时只需TE:trailers|

1.Accept
![Alt text](https://github.com/zhouzefei/http/blob/master/images/14.png)
q标示权重，0～1

2.Authorization
![Alt text](https://github.com/zhouzefei/http/blob/master/images/15.png)

3.Except
告知服务器希望出现某种特定的行为，因为服务器无法理解客户端的期望就会报417，所以客户就先写明所期望的扩展
![Alt text](https://github.com/zhouzefei/http/blob/master/images/16.png)

4.if-xxx称为条件请求
* 只有if-match与etag匹配服务器才接受请求
![Alt text](https://github.com/zhouzefei/http/blob/master/images/17.png)

---
* If-Modified-Since(304):
<center>![Alt text](https://github.com/zhouzefei/http/blob/master/images/18.png)</center>

---
* If-None-Match——ETag(304)
与if-match相反,不一致表示有改动需要获取最新的资源
<center>![Alt text](https://github.com/zhouzefei/http/blob/master/images/19.png)</center>

---
* If-Range
<center>![Alt text](https://github.com/zhouzefei/http/blob/master/images/20.png)</center>

5.Max-Forwards
通过trace或options方法发送max－forwards，max－forwards值减1后不再进行转发，直接返回响应
<center>![Alt text](https://github.com/zhouzefei/http/blob/master/images/21.png)</center>


#####响应报文首部：

| 首部|描述|
|:------------- |:-------------|
|<font color=#ff0000>Accept-Ranges</font>|是否接受字节范围请求|
|Age|推算资源创建经过时间|
|ETag|资源的匹配信息|
|Location|另客户端重定向到uri|
|Proxy-Authenticate|代理服务器对客户端的认证信息|
|Retry-After|告知客户端多久之后再次发送请求，主要配合503或3**|
|Server|告知客户端当前服务器应用程序信息|
|Vary|代理服务器缓存的管理信息,可对缓存进行控制|
|WWW-Authenticate|服务器对客户端的认证信息,Proxy-Authenticate类似|

1.Age
源服务器子啊多久前创建了响应。如果创建响应的是缓存服务器，Age就是指缓存后的响应再次发起认证完成的时间值。代理创建响应必须带Age
![Alt text](https://github.com/zhouzefei/http/blob/master/images/22.png)
2.Vary
![Alt text](https://github.com/zhouzefei/http/blob/master/images/23.png)


#####实体首部：包含在请求报文和响应报文中的实体部分所使用的首部，用于补充内容的更新时间与实体相关的信息
| 首部|描述|
|:------------- |:-------------|
|<font color=#ff0000>Allow</font>|资源可支持的HTTP方法|
|Content-Encoding|实体主体适用的编码方式|
|Content-Language|主体的自然语言|
|Content-Length|主体的大小|
|Content-Location|替代对应的资源uri|
|Content-MD5|主体的报文摘要|
|Content-Range|主体的位置范围|
|Content-Type|主体的媒体类型，发送的实体类型|
|Expires|主体过期的日期时间|
|Last-Modified|资源最后修改日期时间|
1.Allow
405错误提示
![Alt text](https://github.com/zhouzefei/http/blob/master/images/24.png)

2.Content-Length
411错误无效长度
实体长度，又表示传输长度。有了Transfer-Encoding，则不能有Content-Length。如果有Transfer-Encoding，则优先采用Transfer-Encoding里面的方法来找到对应的长度。使用Content-Length首部是为了能够检测出服务器崩溃而导致的报文截尾，并对共享持久连接的多个报文进行正确分段。

3.Content-MD5
![Alt text](https://github.com/zhouzefei/http/blob/master/images/25.png)

4.Content-Range
字节
![Alt text](https://github.com/zhouzefei/http/blob/master/images/26.png)

5.Content-Type
![Alt text](https://github.com/zhouzefei/http/blob/master/images/27.png)


#####Cookie首部：
| 首部|描述|
|:------------- |:-------------|
|Cookie|服务器接收到Cookie|
|Set-Cookie|开始状态管理使用的Cookie|

![Alt text](https://github.com/zhouzefei/http/blob/master/images/28.png)
![Alt text](https://github.com/zhouzefei/http/blob/master/images/29.png)



<br/>
>代理

####正向代理
位于客户端和原始服务器之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求<font color="#f00">并指定目标</font>，然后代理向原始服务器转交请求并将获得的内容返回客户端。
![Alt text](https://github.com/zhouzefei/http/blob/master/images/30.png)

使用场景1：我访问不了谷歌，但是我能访问一个代理服务器，这个代理服务器能访问到谷歌

使用场景2：我要在本地发起一个http请求，域名是www.maihaoche.com。
![Alt text](https://github.com/zhouzefei/http/blob/master/images/31.png)

VPN网关通过对数据包的加密和数据包目标地址的转换实现远程访问。

####反向代理
对于客户端而言它就像原始服务器，并且不需要指定目标。举个例子，用户访问http://www.maihaoche.com/zhouzefei,在maihaoche上不存在zhouzefei的资源，图就从另外一台服务器上取回来。作为自己的内容发送给用户。客户端向反向代理发送普通请求，反向代理判断向哪个原始服务器转交请求。

反向代理可以为后端多台服务器提供负载平衡，或为后端较慢的服务器提供缓冲服务。反向代理还可以启用高级url策略和管理技术，从而使处于不同web服务器的web页面同时存在于同一个url空间下。

CDN的基本思路是尽可能避开互联网上有可能影响数据传输速度和稳定性的瓶颈和环节，使内容传输的更快、更稳定。通过在网络各处放置反向代理节点服务器所构成的在现有的互联网基础之上的一层智能虚拟网络，CDN系统能够实时地根据网络流量和各节点的连接、负载状况以及到用户的距离和响应时间等综合信息将用户的请求重新导向离用户最近的服务节点上。其目的是使用户可就近取得所需内容，解决 Internet网络拥挤的状况，提高用户访问网站的响应速度。

---

####实用场景描述：
node前后端分离方案：
#####只做静态资源服务器
<center>![Alt text](https://github.com/zhouzefei/http/blob/master/images/32.png)</center>

#####node需要对java返回的数据做处理
<center>![Alt text](https://github.com/zhouzefei/http/blob/master/images/33.png)</center>
<br/>
>解决HTTP的无状态问题

![Alt text](https://github.com/zhouzefei/http/blob/master/images/34.png)

当程序为某个客户端的请求创建session时会先检查这个客户端是否已包含一个session id，如果已经包含，就根据sessionid将session检索出来。如果不包含则为客户端创建一个session并且生成一个关联的session id，然后将sessionid在响应中返回给客户端。
<center>![Alt text](https://github.com/zhouzefei/http/blob/master/images/37.png)图六</center>
1. 通过cookies保存
服务器将生成的唯一的sessionid通过set-cookie返回给客户端，在客户端发起新的请求时，在cookie中将携带这个sessionid，这样服务器就能找到客户端对应的session。图六
2. 通过URL回写实现
服务器在发送给浏览器页面的所有连接中都携带sessionid的参数。
3. 表单
4. url参数
<br/>


* 未来协议spdy：一个tcp上并行多个http减少建立时间，请求优先级，http头部压缩，服务器主动推送对象
* Server push/hint 服务器主动推送对象（可以想象成服务器帮客户端预取）
	




