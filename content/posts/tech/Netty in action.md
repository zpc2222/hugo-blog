---
title: "Netty实战"
date: 2022-09-05T00:17:58+08:00
lastmod: 2022-09-05T00:17:58+08:00
author: ["藏锋"]
keywords: 
- netty
- 网络编程
categories: 
- tech
tags: 
- netty
- 网络编程
description: "Java代码编写的一个客户端+服务端的demo实例，入门级"
weight:
slug: ""
draft: false # 是否为草稿
comments: true
reward: true # 打赏
mermaid: true #是否开启mermaid
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示路径
cover:
    image: "" #图片路径例如：posts/tech/123/123.png
    caption: "" #图片底部描述
    alt: ""
    relative: false
---


# 概念
| 组件               | 名                   | 概念                                                                                  |
| ------------------ | -------------------- | ------------------------------------------------------------------------------------- |
| IO                 | 流                   | 各种各样的流（文件、数组、缓冲、管道。。。）的处理（输入输出）                        |
| Channel            | 通道                 | 代表一个连接，每个Client请对会对应到具体的一个Channel                                 |
| ChannelPipeline    | 责任链               | 每个Channel都有且仅有一个ChannelPipeline与之对应，里面是各种各样的Handler             |
| handler            | 事件                 | 用于处理出入站消息及相应的事件，实现我们自己要的业务逻辑                              |
| EventLoopGroup     | I/O线程池            | 负责处理Channel对应的I/O事件                                                          |
| ChannelInitializer | Channel初始化器      |                                                                                       |
| ChannelFuture      | 执行结果             | 代表I/O操作的执行结果，通过事件机制，获取执行结果，通过添加监听器，执行我们想要的操作 |
| ByteBuf            | 字节序列             | 通过ByteBuf操作基础的字节数组和缓冲区                                                 |
| ServerBootstrap    | 服务器端启动辅助对象 |                                                                                       |
| Bootstrap          | 客户端启动辅助对象                     |                                                                                       |
# 流程图
![](https://zpc-1306994356.cos.ap-nanjing.myqcloud.com/ob/202208291110698.png)


# 客户端代码：
```java
import io.netty.buffer.ByteBuf;  
import io.netty.channel.ChannelHandler;  
import io.netty.channel.ChannelHandlerContext;  
import io.netty.channel.SimpleChannelInboundHandler;  
import io.netty.util.CharsetUtil;  
  
@ChannelHandler.Sharable //这个注解是为了线程安全，如果你不在乎是否线程安全，不加也可以  
public class ClientHandler extends SimpleChannelInboundHandler<ByteBuf> { //这里的类型可以是ByteBuf，也可以是String，还可以是对象，根据实际情况来  
    //channelRead0 消息读取方法，注意名称中有个0  
    //ChannelHandlerContext：通道上下文，代指Channel；  
    //ByteBuf：字节序列，通过ByteBuf操作基础的字节数组和缓冲区，因为JDK原生操作字节麻烦、效率低，所以Netty对字节的操作进行了封装，实现了指数级的性能提升，同时使用更加便利；  
    @Override  
    protected void channelRead0(ChannelHandlerContext channelHandlerContext, ByteBuf byteBuf) throws Exception {  
        System.out.println("接收到的消息：" + byteBuf.toString(CharsetUtil.UTF_8));//CharsetUtil.UTF_8：这个是JDK原生的方法，用于指定字节数组转换为字符串时的编码格式。  
    }  
    @Override  
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {  
        //出现异常的时候执行的动作（打印并关闭通道）  
        System.out.println("exceptionCaught:" + cause.toString());  
        if ("java.io.IOException: 远程主机强迫关闭了一个现有的连接。".equals(cause.toString()) || "java.io.IOException: Connection reset by peer".equals(cause.toString())) {  
            System.out.println("与服务端断联");  
            ctx.close().sync();  
            ctx.flush();  
        }  
    }  
}
```
## 客户端启用
```Java
import io.netty.bootstrap.Bootstrap;  
import io.netty.buffer.Unpooled;  
import io.netty.channel.ChannelFuture;  
import io.netty.channel.ChannelInitializer;  
import io.netty.channel.EventLoopGroup;  
import io.netty.channel.nio.NioEventLoopGroup;  
import io.netty.channel.socket.SocketChannel;  
import io.netty.channel.socket.nio.NioSocketChannel;  
import io.netty.util.CharsetUtil;  
  
import java.net.InetSocketAddress;  
  
public class ClientStart {  
    private final String host;  
    private final int port;  
  
    public ClientStart(String host, int port) {  
        this.host = host;  
        this.port = port;  
    }  
  
    public static void main(String[] args) throws Exception {  
        new ClientStart("192.168.20.125", 8089).run();  
    }  
  
    public void run() throws Exception {  
        /**  
         * @Description 配置相应的参数，提供连接到远端的方法  
         **/  
        EventLoopGroup group = new NioEventLoopGroup();//I/O线程池  
        try {  
            Bootstrap bs = new Bootstrap();//客户端辅助启动类  
            bs.group(group).channel(NioSocketChannel.class)//实例化一个Channel  
                    .remoteAddress(new InetSocketAddress(host, port)).handler(new ChannelInitializer<SocketChannel>()//通道Channel的初始化工作，如加入多个handler，都在这里进行；  
                    {  
                        @Override  
                        protected void initChannel(SocketChannel socketChannel) throws Exception {  
                            /**  
                             * 连接建立后，都会自动创建一个管道pipeline，这个管道也被称为责任链，保证顺序执行，同时又可以灵活的配置各类Handler，这是一个很精妙的设计，既减少了线程切换带来的资源开销、避免好多麻烦事，同时性能又得到了极大增强。  
                             */  
                            socketChannel.pipeline().addLast(new ClientHandler());//添加我们自定义的Handler  
                        }  
                    });  
  
            //连接到远程节点；等待连接完成  
            ChannelFuture future = bs.connect().sync();//这里的sync()表示采用的同步方法，这样连接建立成功后，才继续往下执行；  
            //发送消息到服务器端，编码格式是utf-8  
            future.channel().writeAndFlush(Unpooled.copiedBuffer("Hello World", CharsetUtil.UTF_8));  
            //阻塞操作，closeFuture()开启了一个channel的监听器（这期间channel在进行各项工作），直到链路断开  
            future.channel().closeFuture().sync();  
        } finally {  
            group.shutdownGracefully().sync();  
        }  
    }  
}
```


# 服务端实现：
```java
  
import io.netty.buffer.ByteBuf;  
import io.netty.buffer.Unpooled;  
import io.netty.channel.ChannelHandler;  
import io.netty.channel.ChannelHandlerContext;  
import io.netty.channel.ChannelInboundHandlerAdapter;  
import io.netty.util.CharsetUtil;  
import io.netty.util.ReferenceCountUtil;  
  
import static java.lang.System.*;  
  
@ChannelHandler.Sharable  
public class ServerHandler extends ChannelInboundHandlerAdapter {  
    @Override  
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {  
        //处理收到的数据，并反馈消息到到客户端  
        ByteBuf byteBuf = (ByteBuf) msg;  
        byte[] bytes = new byte[byteBuf.readableBytes()];  
        byteBuf.readBytes(bytes);  
  
        out.println("收到客户端发过来的消息: " +new String(bytes, CharsetUtil.UTF_8));  
        //写入并发送信息到远端（客户端）  
        ctx.writeAndFlush(Unpooled.copiedBuffer("你好，我是服务端，我已经收到你发送的消息", CharsetUtil.UTF_8));  
  
        // 引用计数器及时申请释放不再引用的对象  
        ReferenceCountUtil.release(byteBuf);  
    }  
  
    @Override  
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {  
        //出现异常的时候执行的动作（打印并关闭通道）  
        out.println("exceptionCaught:" + cause.toString());  
        if ("java.io.IOException: 远程主机强迫关闭了一个现有的连接。".equals(cause.toString()) || "java.io.IOException: Connection reset by peer".equals(cause.toString())) {  
            out.println("ExceptionCaught: Client Disconnect The Connection.");  
            ctx.close().sync();  
            ctx.flush();  
        }  
    }  
}
```

## 服务端启动:
```java 
import io.netty.bootstrap.ServerBootstrap;  
import io.netty.channel.ChannelFuture;  
import io.netty.channel.ChannelInitializer;  
import io.netty.channel.EventLoopGroup;  
import io.netty.channel.nio.NioEventLoopGroup;  
import io.netty.channel.socket.SocketChannel;  
import io.netty.channel.socket.nio.NioServerSocketChannel;  
  
import java.net.InetAddress;  
import java.net.InetSocketAddress;  
import java.net.NetworkInterface;  
import java.net.SocketException;  
import java.util.Enumeration;  
import java.util.LinkedList;  
import java.util.List;  
  
public class ServerStart {  
    private final int port;  
  
    public ServerStart(int port) {  
        this.port = port;  
    }  
  
    public static void main(String[] args) throws Exception {  
        new ServerStart(8089).run();  
    }  
  
    /**  
     * 创建两个EventLoopGroup的实例，一个负责接收客户端的连接，另一个负责处理消息I/O  
     * @throws Exception  
     */    public void run() throws Exception {  
        String url = getIpAddress().get(0);  
  
        InetSocketAddress address = new InetSocketAddress(url, port);//设置监听端口  
        //Netty的Reactor线程池，初始化了一个NioEventLoop数组，用来处理I/O操作,如接受新的连接和读/写数据  
        // 1.创建两个事件组,boss用于处理请求的accept事件,work用于请求的read和write事件  
        EventLoopGroup bossGroup = new NioEventLoopGroup();  
        EventLoopGroup workerGroup = new NioEventLoopGroup();  
        try {  
            ServerBootstrap b = new ServerBootstrap();//用于启动NIO服务  
            b.group(bossGroup,workerGroup).channel(NioServerSocketChannel.class) //通过工厂方法设计模式实例化一个channel  
                    .localAddress(address)  
                    .childHandler(new ChannelInitializer<SocketChannel>() {  
                        //ChannelInitializer是一个特殊的处理类，他的目的是帮助使用者配置一个新的Channel,用于把许多自定义的处理类增加到pipline上来  
                        @Override  
                        public void initChannel(SocketChannel ch) throws Exception {//ChannelInitializer 是一个特殊的处理类，他的目的是帮助使用者配置一个新的 Channel。  
                            ch.pipeline().addLast(new ServerHandler());//配置childHandler来通知一个关于消息处理的InfoServerHandler实例  
                        }  
                    });  
  
            //绑定服务器，该实例将提供有关IO操作的结果或状态的信息  
            ChannelFuture channelFuture = b.bind(address).sync();  
            System.out.println("在" + address + "上开启监听");  
  
            //阻塞操作，closeFuture()开启了一个channel的监听器（这期间channel在进行各项工作），直到链路断开  
            channelFuture.channel().closeFuture().sync();  
        } finally {  
            bossGroup.shutdownGracefully().sync();//关闭EventLoopGroup并释放所有资源，包括所有创建的线程  
            workerGroup.shutdownGracefully().sync();//关闭EventLoopGroup并释放所有资源，包括所有创建的线程  
        }  
    }  
  
    private static List<String> getIpAddress() {  
        List<String> list = new LinkedList<>();  
        try {  
            for (Enumeration<NetworkInterface> en = NetworkInterface.getNetworkInterfaces(); en.hasMoreElements(); ) {  
                NetworkInterface intf = en.nextElement();  
                String name = intf.getName();  
                if (!name.contains("docker") && !name.contains("lo")) {  
                    for (Enumeration<InetAddress> enumIpAddr = intf.getInetAddresses(); enumIpAddr.hasMoreElements(); ) {  
                        InetAddress inetAddress = enumIpAddr.nextElement();  
                        if (!inetAddress.isLoopbackAddress()) {  
                            String ipaddress = inetAddress.getHostAddress();  
                            if (!ipaddress.contains("::") && !ipaddress.contains("0:0:") && !ipaddress.contains("fe80")) {  
                                list.add(ipaddress);  
                            }  
                        }  
                    }  
                }  
            }  
        } catch (SocketException ex) {  
            String ip = "127.0.0.1";  
            list.add(ip);  
        }  
        return list;  
    }  
  
}
```