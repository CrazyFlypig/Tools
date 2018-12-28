# Nginx快速入门

按照官方的定义，nginx是一个HTTP服务器，也是一个反向代理服务器。apache应该为大家所熟知，而nginx就是类似apache的提供静态网页的web服务器，相比于apache的多进程多线程的并发模型，而nginx是基于事件的异步IO的并发模型，性能更好，而且nginx是一个轻量级的服务器。 

nginx有一个主进程和几个工作进程。 主进程的主要目的是读取和评估配置，并维护工作进程。 工作进程对请求进行实际处理。 nginx采用基于事件的模型和依赖于操作系统的机制来有效地在工作进程之间分配请求。 工作进程的数量可在配置文件中定义，并且可以针对给定的配置进行修改，或者自动调整到可用CPU内核的数量(请参阅[worker_processes](http://nginx.org/en/docs/ngx_core_module.html#worker_processes))。 

## 0.简介

nginx的模块是通过一个叫指令(directive)的东西来用的。整个配置文件都是由指令来控制的。nginx也有自己内置的指令，比如events, http, server, 和 location等 。

## 1. 启动，停止和重新加载Nginx配置

启动nginx，请运行可执行文件。 当nginx启动后，可以通过使用`-s`参数调用可执行文件来控制它。 使用以下语法： 

```nginx -s signal```

信号(signal)的值可以是一下之一：

* `stop` - 快速关闭服务
* `quit` - 正常关闭服务
* `reload` -重新加载配置文件
* `reopen` - 重新打开日志文件

在将重新配置命令的命令发送到nginx或重新启动之前，配置文件中的更改将不会被应用。要重新加载配置文件，请执行：

`nginx -s reload`

当主进程收到要重新加载配置的信号，它将检查新配置文件的语法有效性，并尝试应用其中提供的配置。 如果这是成功的，主进程将启动新的工作进程，并向旧的工作进程发送消息，请求它们关闭。 否则，**主进程回滚**更改，并继续使用旧配置。 老工作进程，接收关闭命令，停止接受新连接，并继续维护当前请求，直到所有这些请求得到维护。 之后，旧的工作进程退出。 

## 2.配置文件的结构

nginx由配置文件中指定的指令控制的模块组成。 指令分为简单指令和块指令。 一个简单的指令由空格分隔的名称和参数组成，并以分号(`;`)结尾。 块指令具有与简单指令相同的结构，但不是以分号结尾，而是以大括号(`{`和`}`)包围的一组附加指令结束。 如果块指令可以在大括号内部有其他指令，则称为上下文(例如：`events`，`http`，`server`和`location`)。 

配置文件中放置在任何上下文之外的伪指令都被认为是**主上下文**。 `events` 和`http`指令驻留在**主上下文**中，`server`在`http`中的，而`location`在`http`块中。`#`号之后的一行的部分被视为注释。

## 3.提供静态内容服务(静态网站)

通常，配置文件可以包括服务器监听的端口和服务器名称区分的几个`server`块。当nginx决定哪个服务器处理请求后，它会根据服务器块内部定义的`location`指令的参数测试请求头中指定的URI 

`server`块的最终配置应如下所示：

```
server {
    location / {
        root /data/www;
    }

    location /images/ {
        root /data;
    }
}
```

这已经是一个在标准端口`80`上侦听并且可以在本地机器上访问的服务器( `http://localhost/` )的工作配置。 响应以`/images/`开头的URI的请求，服务器将从`/data/images`目录发送文件。 例如，响应`http://localhost/images/logo.png`请求，nginx将发送服务上的`/data/images/logo.png`文件。 如果文件不存在，nginx将发送一个指示`404`错误的响应。 不以`/images/`开头的URI的请求将映射到`/data/www`目录。 例如，响应`http://localhost/about/example.html`请求时，nginx将发送`/data/www/about/example.html`文件。

## 4.设置简单的代理服务器

nginx的一个常见用途是将其设置为代理服务器，这意味着它可作为一个接收请求的服务器，将其传递给代理服务器，从代理服务器中检索响应，并将其发送给客户端。 

用户A通过代理服务器B访问网站C，请求先到代理服务器B，B再转发请求到网站C，代理服务器B是真正访问网站C的，访问之后再把网站C的应答结果发给用户A。这样给用户A的感觉是C直接提供服务的一样，因为看不到B的整个处理过程。代理服务器是一个中间者，是充当转发请求的角色。这种代理也叫`正向代理`。 

使用正向代理是要在客户端进行设置，比如浏览器设置代理服务器的域名或IP，还有端口等。 正向代理的作用有很多，例如，能访问本无法访问的，加速，cache，隐藏访问者的行踪等 。

用户A访问网站B，这个时候网站B充当了web服务器，也充当了反向代理服务器，它充当的代理服务器的角色是这样，假如用户A要得到网站C的内容，而用户A又不能直接访问到(例如网络原因)，而服务器B可以访问到网站C，那服务器可以得到网站C的内容再存起来发给用户A，这整个过程用户A是直接和代理服务器B交互的，用户A不知道网站C的存在，这个web服务器B就是一台反向代理服务器，这个网站C就是上游服务器(upstream servers)。 `反向代理`(reverse proxy)正好与正向代理相反，对于客户端而言代理服务器就像是原始服务器，并且客户端不需要进行任何特别的设置。 

反向代理的作用是，隐藏和保护原始服务器，还有，就是负载均衡。 

反向代理服务器能代理的请求的协议包括http(s)，FastCGI，SCGI，uwsgi，memcached等。我们这里主要集中在http(s)协议。

### 4.1 简单示例 

```
# https
server {
  server_name www.example.com;
  listen       443;
  location /newchart/hollow/small/nsh000001.gif {
    proxy_pass http://image.sinajs.cn/newchart/hollow/small/nsh000001.gif;
  }

  location /newchart/hollow/small/nsz399001.gif {
    proxy_pass http://image.sinajs.cn/newchart/hollow/small/nsz399001.gif;
  }
```

假如我的网站是`www.example.com`这样就能使用`https://www.example.com/newchart/hollow/small/nsh000001.gif`，它指向是`http://image.sinajs.cn/newchart/hollow/small/nsh000001.gif`。

### 4.2 动态转发

#### 4.2.1 负载均衡

```shell
## 负载均衡
upstream name{
    server server1_ip:port;
    server server2_ip:port;
    ## 负载分配方式，这个是每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决	  ## session的问题。
    ip_hash;
}
```

#### 4.2.2 配置代理服务器

```shell
server{
    listen port;	#监听端口
    server_name: proxy_ip;	#配置访问域名
    location / {
        proxy_pass http://name;	#设置被代理服务器的URL，端口或套接字也可以
    }
}
```

