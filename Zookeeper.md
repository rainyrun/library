# Zookeeper

[Zookeeper安装](http://www.cnblogs.com/kcen/p/7766906.html)

[官方文档](http://zookeeper.apache.org/)

安装

配置

启动
bin/zkServer.sh start
关闭
bin/zkServer.sh stop
查看状态
bin/zkServer.sh status

查看日志
bin/zkServer.sh start-foreground 

特色
1. 集中式的配置中心
2. 自动容错
3. 近实时搜索
4. 查询时自动负载均衡

也有投票机制，集群至少需要3个实例

### zookeeper集群

在每个zoo.cfg里加上

```
server.1=ip:port1:port2
server.2=ip:port1:port2
server.3=ip:port1:port2
```

1，2，3是节点id

port1是通信端口
port2是投票端口


写一个shell文件，启动所有zookeeper，或者关闭所有。

zkcli -server:port 

