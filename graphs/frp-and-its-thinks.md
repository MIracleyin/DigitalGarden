---
title: frp and its thinks
created: 2023-07-24
date: 2023-07-31
tags:
- graph
- dev-ops
---
## 缘起

课题组远程访问内网服务器一直是一个需要解决的问题。去年，我们使用向日葵、花生壳等软件，由于各种限制最后都没有继续用下去。最后决定使用一台拥有公网 IP 的云服务器，配置 frp 服务，并实现多台内网服务器的远程 ssh 功能。

## FRP 简介

frp 是一个专注于内网穿透的高性能反向代理应用，支持 TCP、UDP、HTTP、HTTPS 等多种协议。可以将内网服务以安全、便捷的方式通过具有公网 IP 节点的中转暴露到公网。

通过在具有公网 IP 的节点上部署 frp 服务端，可以轻松地将内网服务穿透到公网，同时提供诸多专业的功能特性，客户端服务端通信支持 TCP、KCP 以及 Websocket 等多种协议。

由于需求是实现远程 ssh，只会用到 TCP 协议，并且 frp 采用 Golang 实现，支持跨平台，单纯使用不需要额外依赖。所以使用 frp 可以很好的满足我们的需求。

## FRP 部署

1. 可以在 Github 的 [Release](https://github.com/fatedier/frp/releases) 内下载，注意要按照自己的平台选择下载相应编译版本；
1. 在自己的 PC 机解压下载好的应用程序；
1. 分别向云服务器与需要内网穿透的服务器上发送应用程序。这里，云服务器作为服务端，发送的是「frps」与「frps.ini」两个文件，而需要内网穿透的服务器作为客户端发送的是「frpc」与「frpc.ini」两个文件，这里的文件可以放在任意位置，并按需要给予文件相应的读写权限；
1. frp 的配置文件是「\*.ini」的两个文件，按照自己的需要修改对应内容以满足功能需求，所有可以配置的文件可以在「\*_full.ini」中，我们按照基础的远程 ssh 功能配置以上两个文件；
1. 对于服务端，我们使用基础配置；

    ```shell
    [common]
    bind_port = 7000
    ```

1. 对于客户端，我们同样使用基础配置；

    ```shell
    [common]
    server_addr = xx.xx.xx.xx # 服务端的公网 IP
    server_port = 7000        # 与bind_port 保持一致
    
    [ssh]
    type = tcp                # 协议，ssh 使用 tcp 传输
    local_ip = 127.0.0.1      # 表示本机，目测可以使用 0.0.0.0 代替
    local_port = 22           # 默认的 ssh 端口
    remote_port = 6000        # 云服务器监听转发的端口
    ```

1. 首先在服务端运行 frps，

    ```shell
    ./frps -c ./frps.ini # -c 是指按照 config 文件配置服务端
    ```

    ![image-20211212152718591](https://blog-1254050354.cos.ap-nanjing.myqcloud.com/img/blog/2021-12/image-20211212152718591-0d461e.png)

    当出现 frps 启动成果时，服务端配置完毕，这时服务器在 `bind_port` 端口上会持续监听客户端的登陆；

1. 接着在客户端运行 frpc

    ```shell
    ./frpc -c ./frpc.ini # -c 是指按 config 文件配置服务端
    ```

    首先我们看服务端现象，服务端提示有 [ssh] 请求成果

    ![image-20211212154437017](https://blog-1254050354.cos.ap-nanjing.myqcloud.com/img/blog/2021-12/image-20211212154437017-8deec4.png)

    接着我们看启动后的客户端现象，客户端提示有 [ssh] 代理成功

    ![image-20211212154647802](https://blog-1254050354.cos.ap-nanjing.myqcloud.com/img/blog/2021-12/image-20211212154647802-ab4598.png)

    此时，服务端与客户端已经建立链接，此时使用外网的 PC 机，便可以经过服务端的反向代理访问到客户端的内网服务器；

1. 我们使用 ssh 指令验证结果，`-oPort=remote_port`，`username` 为内网服务器用户名，`serverip` 为云服务器外网 IP

    ```shell
    ssh -oPort=xxxx username@serverip 
    ```

    ![image-20211212162941720](https://blog-1254050354.cos.ap-nanjing.myqcloud.com/img/blog/2021-12/image-20211212162941720-e4021c.png)  
    可以访问。

## 一些思考

1. 我们设计一个小实验说明一下 frp 究竟做了些什么，首先我们定义实验拓扑关系：  
    ![image-20211212162248229](https://blog-1254050354.cos.ap-nanjing.myqcloud.com/img/blog/2021-12/image-20211212162248229-49f776.png)

1. 验证如下：  
    远程 PC ping 云服务器结果，可以通

    ![image-20211212161659834](https://blog-1254050354.cos.ap-nanjing.myqcloud.com/img/blog/2021-12/image-20211212161659834-2dc88d.png)

    远程 PC ping 客户端内网服务器结果，无法通

    ![image-20211212161930369](https://blog-1254050354.cos.ap-nanjing.myqcloud.com/img/blog/2021-12/image-20211212161930369-d095e0.png)

    服务端云服务器 ping 客户端内网服务器结果，无法通

    ![image-20211212162051173](https://blog-1254050354.cos.ap-nanjing.myqcloud.com/img/blog/2021-12/image-20211212162051173-e3e1ac.png)  
    客户端内网服务器 ping 服务端云服务器结果，可以通

    ![image-20211212162232336](https://blog-1254050354.cos.ap-nanjing.myqcloud.com/img/blog/2021-12/image-20211212162232336-042656.png)

    结果符合预期；

1. frp 是怎样通过反向代理，使得远程 PC 机可以访问内网服务器的呢？我们以下图解释  
    ![image-20211212163946233](https://blog-1254050354.cos.ap-nanjing.myqcloud.com/img/blog/2021-12/image-20211212163946233-228fa3.png)  
    首先，我们看服务端和客户端之间的关系，服务端开放监听 `band_port` ，由于客户端可以 ping 通服务端，所以通过 `bind_port` 服务端可以将信息在该端口传输至客户端，至此「反向代理」的信息通路搭建。接着，由于 PC 机可以访问服务端，而服务端开放监听 `remote_port` ，因此当 PC 向服务端发送的信息就会被 frp 通过 `bind_port` 转发至客户端的 22 端口，即默认的 ssh 端口，因此远程 ssh 成功。

## 一些经验

- 基于思考实验我们可以得出，对于云服务器有两个端口是非常重要的，一是 `band_port` ，其决定了服务端和客户端的通信，二是 `remote_port` ，其决定了远程 PC 机和服务端的通信。因此，在许多云服务器供应商的防火墙策略中，要记得把这两个参数设置成对 TCP 协议开放，大多数无法成功远程的原因都在于防火墙没有开放。
- frp 当前版本 `0.38.0` 文件中有一个 systemmd 文件夹，这个文件夹内有一些内置的 server 文件，这些文件可以很方便的使用 Linux 与 macOS 的 systemctl 部署成系统级服务，其步骤如下：

    1. 将服务端或客户端对应的 `*.service` 文件复制到 `/etc/systemd/system/` 内；
    1. 修改 `ExecStart=` 的值，与之前启动 frp 的指令相同，`/your/frp/path/frpc -c /your/frp/path/frpc.ini` 并注意要使用绝对路径；

        ```shell
        ExecStart= /your/frp/path/frpc -c /your/frp/path/frpc.ini #修改这个
        sudo systemctl enable frps # 服务端设置开机启动
        sudo systemctl enable frpc # 客户端设置开机启动
        
        sudo systemctl start frps # 服务端开启服务
        sudo systemctl start frpc # 客户端开启服务
        ```

    1. 我们还可以使用以下指令查看 frp 的运行状态如下

        ```shell
        service frps status
        service frpc status
        ```

        ![image-20211212194739137](https://blog-1254050354.cos.ap-nanjing.myqcloud.com/img/blog/2021-12/image-20211212194739137-61b713.png)

## 参考文献

1. [frp 官方文档](https://gofrp.org/docs/)
1. [自己动手部署内网穿透：frp](https://zhuanlan.zhihu.com/p/341551501)