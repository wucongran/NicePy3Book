- 1.  向浏览器输入网址 http://www.baidu.com/

- 2. 查询本地计算机有没有存储域名baidu.com 对应的服务器IP, 如果存储了,浏览器直接向目标服务器发起三次握手的连接请求;如果没有存储, 则向DNS服务器发起"查询baidu.com对应服务器IP"的请求, DNS服务器返回baidu.com对应的目标IP后, 向目标IP发起三次握手的连接请求,如果连接成功,则在本地备份目标IP

> 将请求发送到目标IP的具体细节(为什么MAC地址与IP地址缺一不可) : 在OSI七层协议中MAC地址属于第二层**数据链路层**, IP地址属于第三层**网络层**, 浏览器发出的请求时, 会发起ARP广播, 查询本地网络中,是否存在目标IP主机, 如果存在, 直接将浏览器请求的MAC地址定为目标IP主机的MAC地址, 如果不存在, 则将浏览器请求的MAC地址定为本地网络环境中路由器A的MAC地址, 路由器A会将浏览器请求转发到另一个网络的路由器B(请求的mac地址被修改, 修改为路由器B的mac地址), 路由器B子网内如果存在目标主机IP, 则直接将浏览器请求的ip地址修改为目标主机的MAC地址, 如果不存在, 则转发给浏览器C, 通过不断地修改请求MAC地址完成了浏览器请求在互联网内的层层接力,最终到达目标IP主机
> ![请求与响应](http://upload-images.jianshu.io/upload_images/3203841-e17833fbaa241608.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 3. 服务器接收到请求后, 如果设置反向代理服务软件(如Nginx), Nginx会将请求转发到服务对应的端口(以Django服务为例, 如果Django运行在3309端口, Nginx会将请求从80端口转发到3309端口)

- 4. Django接收到请求后, 中间件会对请求进行预处理, 然后请求会根据正则, 匹配主目录下的urls.py, 匹配成功后, 会将请求分发给独立的App, 在App内也含有urls.py, 通过正则匹配, 请求会被对应到App内相应的视图函数
>![服务器处理请求](http://upload-images.jianshu.io/upload_images/3203841-546bd3615dc98665.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 5. 视图函数会处理请求, 产生响应, 响应通过中间件的处理后, 离开Django, 返回给Nginx, Nginx 会将响应返回给浏览器(这里简化返回路线)

> 关于三次握手和四次挥手:
> #### 三次握手:
> - 1. 浏览器发起连接请求(浏览器: 我们明天早上8点见面, 收到请回复)
> - 2. 服务器接收到请求后, 返回响应(服务端: 确认收到, 我们明天早上八点见面)
> - 3. 浏览器发出接收到服务器响应的确认信息(浏览器: 我收到了你的确认信息!)
> #### 四次挥手:
> - 1. 浏览器发出断开连接的请求**(浏览器: 我该问的都问完了, 没有其它的问题了)**
> - 2. 服务端返回响应(服务端确认了浏览器不会再发送请求的信息)**(服务器: 我收到了你问完了的信息了,待我把你前面的问题回答完, 我们就断开连接)**
> - 3. 服务端发出断开连接的请求**(服务器: 我把你的问题都回答完了, 你确认没有其他要问的了么?)**
> - 4. 浏览器返回响应(浏览器确认了服务端已经将资源发送完毕的信息)**(浏览器: 我没有其它要问的, 断开连接吧!)**
> ![三次握手与四次挥手.png](http://upload-images.jianshu.io/upload_images/3203841-4773037398849b29.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)