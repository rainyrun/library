# OpenVSwitch

## OpenFlow 协议

![OpenFlow组件](images/ovs/OpenFlow组件.png)

OpenFlow交换机由硬件平面上的OpenFlow表项和软件平面上的安全通道构成，OpenFlow表项为OpenFlow的关键组成部分，由Controller下发来实现控制平面对转发平面的控制。

一个OpenFlow交换机可以有若干个OpenFlow实例，每个OpenFlow实例可以单独连接控制器，相当于一台独立的交换机，根据控制器下发的流表项指导流量转发。

![OpenFlow交换机和控制器](images/ovs/OpenFlow交换机和控制器.png)

OpenFlow交换机实际在转发过程中，依赖于OpenFlow表项，转发动作则是由交换机的OpenFlow接口完成。OpenFlow接口有下面三类。

- 物理接口：比如交换机的以太网口等。可以作为匹配的入接口和出接口。
- 逻辑接口：比如聚合接口、Tunnel接口等。可以作为匹配的入接口和出接口。
- 保留接口：由转发动作定义的接口，实现OpenFlow转发功能。

### OpenFlow 流表

OpenFlow通过用户定义的流表来匹配和处理报文。所有流表项都被组织在不同的Flow Table中，在同一个Flow Table中按流表项的优先级进行先后匹配。一个OpenFlow的设备可以包含一个或者多个Flow Table。

一条OpenFlow的表项（Flow Entry）由匹配域（Match Fields）、优先级（Priority）、处理指令（Instructions）和统计数据（如Counters）等字段组成

![流表项组成](images/ovs/流表项组成.png)

(1)Match Fields

流表项匹配规则，可以匹配入接口、物理入接口，流表间数据，二层报文头，三层报文头，四层端口号等报文字段等。

(2)Priority

流表项优先级，定义流表项之间的匹配顺序，优先级高的先匹配。

(3)Counters

流表项统计计数，统计有多少个报文和字节匹配到该流表项。

(4)Instructions & Actions

流表项动作指令（Instructions & Actions）集，定义匹配到该流表项的报文需要进行的处理。当报文匹配流表项时，每个流表项包含的指令集就会执行。这些指令会影响到报文、动作集以及管道流程。 交换机不需要支持所有的指令类型，并且控制器可以询问OpenFlow交换机所支持的指令类型。 具体的指令类型参见下表：

|Instruction|处理|
|---|---|
|Meter|对匹配到流表项的报文进行限速|
|Apply-Actions|立即执行Action|
|Clear-Actions|清除动作集（Action Set）中的所有Action。|
|Write-Actions|更改动作集（Action Set）中的所有Action。|
|Write-Metadata|更改流表间数据，在支持多级流表时使用。|
|Goto-Table|进入下一级流表。|

每个流表表项的指令集中每种指令类型最多只能有一个，指令的执行的优先顺序为：

Meter –> Apply-Actions -> Clear Actions -> Write-Actions -> Write-Metadata -> Goto-Table

常见Action动作的类型如下：

|必选的 action|内容|
|---|---|
|Output|转发到指定的OpenFlow端口|
|Drop|无直接动作，指令集中无output动作则丢弃该报文|
|Group|处理报文到指定Group|


|可选的 action|内容|
|---|---|
|Set-Queue|设置报文的队列id|
|Push-Tag/Pop-Tag|写入/弹出标签 例如VLAN, MPLS等|
|Set-Field|修改报文的头字段|
|Change-TTL|修改TTL，Hop Limit等字段|

(5)Timeouts

流表项的超时时间，包括了Idle Time和Hard Time。

Idle Time：在Idle Time时间超时后如果没有报文匹配到该流表项，则此流表项被删除。

Hard Time：在Hard Time时间超时后，无论是否有报文匹配到该流表项，此流表项都会被删除。

(6)Cookie

Controller下发的流表项的标识

### 流表处理流程

![流表处理过程](images/ovs/流表处理过程.png)

只能往后跳，不能往前跳。

![流表匹配流程](images/ovs/流表匹配流程.png)

每个流表（Flow Table）都包含一个Table Miss流表项，该表项用于定义在流表中没有匹配的报文的处理方式，该表项的匹配域为通配，即匹配任何报文，优先级为0，Instructions与正常表项相同。通常，如果Table-Miss表项不存在，默认行为是丢弃报文。

## OpenFlow 运行机制

OpenFlow协议目前支持的三种报文类型

- Controller to Switch消息：由Controller发起、Switch接收并处理的消息。这些消息主要用于Controller对Switch进行状态查询和修改配置等管理操作，可能不需要交换机响应。Controller to Switch消息主要包含以下几种类型：
    - Features：用于控制器发送请求来了解交换机的性能，交换机必须回应该报文。
    - Modify-State：用于管理交换机的状态，如流表项和端口状态。该命令主要用于增加、删除、修改OpenFlow交换机内的流表表项，组表表项以及交换机端口的属性。
    - Read-State：用于控制器收集交换机各方面的信息，例如当前配置，统计信息等 。
    - Flow-Mod：Flow-Mod消息用来添加、删除、修改OpenFlow交换机的流表信息。Flow-Mod消息共有五种类型：ADD、DELETE、DELETE-STRICT、MODIFY、MODIFY-STRICT。
    - Packet-out：用于通过交换机特定端口发送报文 ，这些报文是通过Packet-in消息接收到的。通常Packet-out消息包含整个之前接收到的Packet-in消息所携带的报文或者buffer ID（用于指示存储在交换机内的特定报文）。这个消息需要包含一个动作列表，当OpenFlow交换机收到该动作列表后会对Packet-out消息所携带的报文执行该动作列表。如果动作列表为空，Packet-out消息所携带的报文将被OpenFlow交换机丢弃。
    - Asynchronous-Configuration：控制器使用该报文设定异步消息过滤器来接收其只希望接收到的异步消息报文，或者向OpenFlow交换机查询该过滤器。该消息通常用于OpenFlow交换机和多个控制器相连的情况。
- 异步（Asynchronous）消息: 由Switch发送给Controller，用来通知Switch上发生的某些异步事件的消息，主要包括Packet-in、Flow-Removed、Port-Status和Error等。例如，当某一条规则因为超时而被删除时，Switch将自动发送一条Flow-Removed消息通知Controller，以方便Controller作出相应的操作，如重新设置相关规则等。异步消息具体包含以下几种类型：
    - Packet-in：转移报文的控制权到控制器。对于所有通过匹配流表项或者Table Miss后转发到Controller端口的报文均要通过Packet-in消息送到Controller。也有部分其他流程（如TTL检查等）也需要通过该消息和Controller交互。Packet-in既可以携带整个需要转移控制权的报文，也可以通过在交换机内部设置报文的Buffer来仅携带报文头以及其Buffer ID传输给Controller。Controller在接收到Packet-in消息后会对其接收到的报文或者报文头和Buffer ID进行处理，并发回Packet-out消息通知OpenFlow交换机如何处理该报文。
    - Flow-Removed：通知控制器将某个流表项从流表的移除。通常该消息在控制器发送删除流表项的消息或者流表项的定时器其超时后产生。
    - Port-Status：通知控制器端口状态或设置的改变。
- 同步（Symmetric）消息: 顾名思义，同步（Symmetric）消息是双向对称的消息，主要用来建立连接、检测对方是否在线等，是控制器和OpenFlow交换机都会在无请求情况下发送的消息，包括Hello、Echo和Experimenter三种消息，这里我们介绍应用最常见的前两种：
    - Hello：当连接启动时交换机和控制器会发送Hello交互。
    - Echo：用于验证控制器与交换机之间连接的存活，控制器和OpenFlow交换机都会发送Echo Request/Reply消息。对于接收到的Echo Request消息必须能返回Echo Reply消息。Echo消息也可用于测量控制器与交换机之间链路的延迟和带宽。

信道建立过程解析

OpenFlow控制器和OpenFlow交换机之间建立信道连接的基本过程，具体步骤如下：

1. OpenFlow交换机与OpenFlow控制器之间通过TCP三次握手过程建立连接，使用的TCP端口号为6633。
2. TCP连接建立后，交换机和控制器就会互相发送hello报文。Hello报文负责在交换机和控制器之间进行版本协商，该报文中OpenFlow数据头的类型值为0。
3. 功能请求（Feature Request）：控制器发向交换机的一条OpenFlow 消息，目的是为了获取交换机性能，功能以及一些系统参数。该报文中OpenFlow 数据头的类型值为5。
4. 功能响应（Feature Reply）：由交换机向控制器发送的功能响应（Feature Reply）报文，描述了OpenFlow交换机的详细细节。控制器获得交换机功能信息后，OpenFlow协议相关的特定操作就可以开始了。
5. Echo请求（Echo Request）和Echo响应（EchoReply）属于OpenFlow中的对称型报文，他们通常用于OpenFlow交换机和OpenFlow控制器之间的保活。通常echo请求报文中OpenFlow数据头的类型值为2，echo响应的类型值为3。不同厂商提供的不同实现中，echo请求和响应报文中携带的信息也会有所不同。

### OpenFlow 消息处理

OpenFlow流表下发分为主动和被动两种机制：

- 主动模式下，Controller将自己收集的流表信息主动下发给网络设备，随后网络设备可以直接根据流表进行转发。
- 被动模式下，网络设备收到一个报文没有匹配的FlowTable记录时，会将该报文转发给Controller，由后者进行决策该如何转发，并下发相应的流表。被动模式的好处是网络设备无需维护全部的流表，只有当实际的流量产生时才向Controller获取流表记录并存储，当老化定时器超时后可以删除相应的流表，因此可以大大节省交换机芯片空间。



## Faucet and ovs

```sh
export PATH=$PATH:/usr/local/share/openvswitch/scripts
ovs-ctl start

# 只启动 ovsdb-server
ovs-ctl --no-ovs-vswitchd start
# 只启动 ovs-vswitch
ovs-ctl --no-ovsdb-server start
# 初始化db，仅在第一次创建db时
ovs-vsctl --no-wait init

ovs-vswitchd --pidfile --detach --log-file
```

### switch

faucet.yaml 配置

```yaml
dps:
    switch-1:
        dp_id: 0x1
        timeout: 3600
        arp_neighbor_timeout: 3600
        interfaces:
            1:
                native_vlan: 100
            2:
                native_vlan: 100
            3:
                native_vlan: 100
            4:
                native_vlan: 200
            5:
                native_vlan: 200
vlans:
    100:
    200:
```

ovs 配置

```sh
ovs-vsctl add-br br0 \
         -- set bridge br0 other-config:datapath-id=0000000000000001 \
         -- add-port br0 p1 -- set interface p1 ofport_request=1 \
         -- add-port br0 p2 -- set interface p2 ofport_request=2 \
         -- add-port br0 p3 -- set interface p3 ofport_request=3 \
         -- add-port br0 p4 -- set interface p4 ofport_request=4 \
         -- add-port br0 p5 -- set interface p5 ofport_request=5 \
         -- set-controller br0 tcp:127.0.0.1:6653 \
         -- set controller br0 connection-mode=out-of-band
```

运维命令

```sh
# 导出流表
ovs-ofctl dump-flows br0
# 查看2个流表差异
ovs-ofctl diff-flows flow1 br0

# tracing
# 从br0交换机的p1口进入的报文，处理过程是什么。-generate 表示实际产生报文
ovs-appctl ofproto/trace br0 in_port=p1
ovs-appctl ofproto/trace br0 in_port=p1,dl_src=00:11:11:00:00:00,dl_dst=00:22:22:00:00:00 -generate




dump-flows () {
  ovs-ofctl -OOpenFlow13 --names --no-stat dump-flows "$@" \
    | sed 's/cookie=0x5adc15c0, //'
}

save-flows () {
  ovs-ofctl -OOpenFlow13 --no-names --sort dump-flows "$@"
}

diff-flows () {
  ovs-ofctl -OOpenFlow13 diff-flows "$@" | sed 's/cookie=0x5adc15c0 //'
}

# create a bridge named br0 and add ports eth0 and vif1.0 to it
ovs-vsctl add-br br0
ovs-vsctl add-port br0 eth0
ovs-vsctl add-port br0 vif1.0
```

### Router

faucet.yaml

```yaml
dps:
    switch-1:
        dp_id: 0x1
        timeout: 3600
        arp_neighbor_timeout: 3600
        interfaces:
            1:
                native_vlan: 100
            2:
                native_vlan: 100
            3:
                native_vlan: 100
            4:
                native_vlan: 200
            5:
                native_vlan: 200
vlans:
    100:
        faucet_vips: ["10.100.0.254/24"]
    200:
        faucet_vips: ["10.200.0.254/24"]
routers:
    router-1:
        vlans: [100, 200]
```

运维命令

```sh
# p1接收的包写到文件里p1.pcap
ovs-vsctl set interface p1 options:pcap=p1.pcap
# dump 包
/usr/sbin/tcpdump -evvvr sandbox/p1.pcap

# 场景：host1(ip=10.100.0.1, mac=00:01:02:03:04:05) 向host2(ip=10.200.0.1)发包
# Step 1: Host ARP for Router
ovs-appctl ofproto/trace br0 in_port=p1,dl_src=00:01:02:03:04:05,dl_dst=ff:ff:ff:ff:ff:ff,dl_type=0x806,arp_spa=10.100.0.1,arp_tpa=10.100.0.254,arp_sha=00:01:02:03:04:05,arp_tha=ff:ff:ff:ff:ff:ff,arp_op=1 -generate
# Step 2: Router Sends ARP Reply，可以通过观察p1端口的包得知
# Step 3: Host Sends IP Packet
ovs-appctl ofproto/trace br0 in_port=p1,dl_src=00:01:02:03:04:05,dl_dst=0e:00:00:00:00:01,udp,nw_src=10.100.0.1,nw_dst=10.200.0.1,nw_ttl=64 -generate
# Step 4: Router Broadcasts ARP Request，可以通过观察p4，p5端口的包得知
# Step 5: Host 2 Sends ARP Reply(手动模拟host2发包)
ovs-appctl ofproto/trace br0 in_port=p4,dl_src=00:10:20:30:40:50,dl_dst=0e:00:00:00:00:01,dl_type=0x806,arp_spa=10.200.0.1,arp_tpa=10.200.0.254,arp_sha=00:10:20:30:40:50,arp_tha=0e:00:00:00:00:01,arp_op=2 -generate
# Step 6: IP Packet Delivery。如果router有缓存功能，在获取完arp后，会将包从p4发出；否则，会等待host1重发包
ovs-appctl ofproto/trace br0 in_port=p1,dl_src=00:01:02:03:04:05,dl_dst=0e:00:00:00:00:01,udp,nw_src=10.100.0.1,nw_dst=10.200.0.1,nw_ttl=64 -generate
```

### ACLs

```yaml
dps:
    switch-1:
        dp_id: 0x1
        timeout: 3600
        arp_neighbor_timeout: 3600
        interfaces:
            1:
                native_vlan: 100
                acl_in: 1
            2:
                native_vlan: 100
            3:
                native_vlan: 100
            4:
                native_vlan: 200
            5:
                native_vlan: 200
vlans:
    100:
        faucet_vips: ["10.100.0.254/24"]
    200:
        faucet_vips: ["10.200.0.254/24"]
routers:
    router-1:
        vlans: [100, 200]
acls:
    1:
        - rule:
            dl_type: 0x800
            nw_proto: 6
            tcp_dst: 8080
            actions:
                allow: 0
        - rule:
            actions:
                allow: 1
```

运维命令

```sh
# 给80端口发包
ovs-appctl ofproto/trace br0 in_port=p1,dl_src=00:01:02:03:04:05,dl_dst=0e:00:00:00:00:01,tcp,nw_src=10.100.0.1,nw_dst=10.200.0.1,nw_ttl=64,tp_dst=80 -generate
```

### Faucet

```sh
# 检查配置文件语法
check_faucet_config /etc/faucet/faucet.yaml
# 用容器启动
docker run -d --name faucet --restart=always -v $(pwd)/inst/:/etc/faucet/ -v $(pwd)/inst/:/var/log/faucet/ -p 6653:6653 -p 9302:9302 faucet/faucet
# 重新加载配置
docker exec faucet pkill -HUP -f faucet.faucet
# 关闭容器
docker stop faucet
```

### Linux 网络

```sh
# Run command inside network namespace
as_ns () {
    NAME=$1
    NETNS=faucet-${NAME}
    shift
    sudo ip netns exec ${NETNS} $@
}
# Create network namespace
create_ns () {
    NAME=$1
    IP=$2
    NETNS=faucet-${NAME}
    sudo ip netns add ${NETNS}
    sudo ip link add dev veth-${NAME} type veth peer name veth0 netns ${NETNS}
    sudo ip link set dev veth-${NAME} up
    as_ns ${NAME} ip link set dev lo up
    [ -n "${IP}" ] && as_ns ${NAME} ip addr add dev veth0 ${IP}
    as_ns ${NAME} ip link set dev veth0 up
}

# 创建虚拟主机
create_ns host1 192.168.0.1/24
create_ns host2 192.168.0.2/24
# 在主机中执行ping
as_ns host1 ping 192.168.0.2
```

## namespace

```sh
# namespace
#添加 network namespace
ip netnas add <network namespace name>
#Example:
ip netns add nstest
#列表所有 netns
ip netns list
ip netns ls
#删除某 netns
ip netns delete <network namespace name>

#在 network namespace 中运行命令
ip netns exec <network namespace name> <command>
#Example using the namespace from above:
ip netns exec nstest ip addr

# ip link 通信管道
#添加 virtual interfaces 到 network namespace
#创建一对虚拟网卡veth-a 和 veth-b，两者由一根虚拟网线连接
ip link add veth-a type veth peer name veth-b 
# 默认命名是veth0 veth1
ip link add type veth
# 查看管道
ip link ls
# 删除管道(一删删一对)
ip link delete veth0
#将 veth-b 添加到 network namespace
ip link set [veth-name] netns [namespace-name]

#设置 VI 的 IP 地址
# 更改管道状态
ip netns exec [namespace-name] ip link set [veth-name] up
ip netns exec nstest ip link set dev veth-b up
# 添加ip地址
ip netns exec [namespace-name] ip addr add 10.0.0.1/24 dev [veth-name]
ip netns exec nstest ip addr add 10.0.0.2/24 dev veth-b
ip netns exec [namespace-name] ip route
# 在默认ns里添加
ip addr add 10.0.0.1/24 dev veth-a
ip link set dev veth-a up

# 进入namespace
ip netns exec [namespace-name] bash

#互通# ping 10.1.1.1
PING 10.1.1.1 (10.1.1.1) 56(84) bytes of data.
64 bytes from 10.1.1.1: icmp_seq=1 ttl=64 time=0.087 ms
  
# ip netns exec netns1 ping 10.1.1.2
PING 10.1.1.2 (10.1.1.2) 56(84) bytes of data.
64 bytes from 10.1.1.2: icmp_seq=1 ttl=64 time=0.054 ms

# 查看路由表和 iptbales
ip netns exec netns1 route
ip netns exec netns1 iptables -L
```

## OVS 基本操作

```sh
# ovs-vsctl获取或者更改ovs-vswitchd的配置信息，此工具操作的时候会更新ovsdb-server中的数据库
# 查看网桥
ovs-vsctl show
# 添加网桥
ovs-vsctl add-br br0
# 列出所有网桥
ovs-vsctl list-br

# 列出网桥中的所有端口
ovs-vsctl list-ports 交换机名 
# 列出所有挂接到网卡的网桥
ovs-vsctl port-to-br 端口名（网卡名） 
# 查看 Open vSwitch 中的端口信息（交换机对应的 dpid，以及每个端口的 OpenFlow 端口编号，端口名称，当前状态等等）
ovs-ofctl show 交换机名

# 创建port
ovs-vsctl add-port br0 port0
# 删除port
ovs-vsctl del-port br0 port0
# 删除网桥
ovs-vsctl del-br br0
# 网桥连接控制器
ovs-vsctl set-controller br0t tcp:127.0.0.1:6653

# ovs-ofctl操作交换机里的流表，包括对流表的增，删，改，查等命令
# 查看流表
ovs-ofctl dump-flows br0
# 下发流表
ovs-ofctl add-flow +匹配项和动作
# 匹配项
# 第一层 入端口in_port
# 第二层 MAC地址 dl_src，dl_dst
# 第三层 IP地址 dl_type、nw_src、nw_dst

# 删除流表
ovs-ofctl del-flows  + 网桥 + 匹配条件

# 网桥特性功能配置
# 查看网桥MAC学习地址表
ovs-appctl fdb/show s1

# fail_mode
# 控制器连接不上时fail_mode 故障模式有两种状态，一种是standalone，一种是secure状态。standalone(default)：清除所有控制器下发的流表，ovs自己接管。secure：按照原来流表继续转发
# 查看
ovs-vsctl get-fail-mode br0
# 配置
ovs-vsctl set-fail-mode br0 secure
# 删除
ovs-vsctl del-fail-mode br0

# 生成树协议STP
# 查看
ovs-vsctl get bridge s1 stp_enable
# 设置
ovs-vsctl set bridge br0 stp_enable=true
# 查看网桥配置信息
ovs-vsctl list bridge s1
# 查看端口配置信息
ovs-vsctl list port s1 s1-eth1


# 连接控制器
ovs-vsctl set-controller 交换机名 tcp:IP地址:端口号 
# 断开控制器
ovs-vsctl del-controller 交换机名 

# 修改dpid
ovs-vsctl set bridge 交换机名 other_config:datapath-id=新DPID 
# 修改端口号
ovs-vsctl set interface 端口名 ofport_request=新端口号
# 查看交换机中的所有 Table
ovs-ofctl dump-tables ovs-switch 
# 删除编号为 100 的端口上的所有流表项
ovs-ofctl del-flows ovs-switch "in_port=100"
# 添加流表项（以“添加新的 OpenFlow 条目，修改从端口 p0 收到的数据包的源地址为 9.181.137.1”为例）： 
ovs-ofctl add-flow ovs-switch "priority=1 idle_timeout=0,in_port=100,actions=mod_nw_src:9.181.137.1,normal"
# 查看 OVS 的版本信息：
ovs-appctl –version 
# 查看 OVS 支持的 OpenFlow 协议的版本
ovs-ofctl –version
```

## Neutron

参考资料(https://www.cnblogs.com/sammyliu/p/4622563.html)

## Overlay 网络

Overlay 技术是在现有的物理网络之上构建一个虚拟网络，上层应用只与虚拟网络相关。一个Overlay网络主要由三部分组成：

- 边缘设备：是指与虚拟机直接相连的设备
- 控制平面：主要负责虚拟隧道的建立维护以及主机可达性信息的通告
- 转发平面：承载 Overlay 报文的物理网络

![Overlay网络](images/ovs/Overlay网络.png)

当前主流的 Overlay 技术主要有VXLAN, GRE/NVGRE和 STT。

### GRE

GRE是一种协议封装的格式。它规定了如何用一种网络协议去封装另一种网络协议。说得直白点，就是 GRE 将普通的包（如ip包）封装了，又按照普通的ip包的路由方式进行路由，相当于ip包外面再封装一层 GRE 包。

其协议格式见[RFC2784](https://tools.ietf.org/html/rfc2784)。

GRE 使用 tunnel（隧道）技术，数据报在 tunnel 的两端封装，并在这个通路上传输，到另外一端的时候解封装。你可以认为 tunnel 是一个虚拟的点对点的连接。

![GRE网络](images/ovs/GRE网络.png)

例子：

办公网络 192.168.1.0/24； 网关ip 192.168.1.254； 公网ip 180.1.1.1

IDC 网络 10.1.1.0/24； 网关ip 10.1.1.1； 公网ip 110.2.2.2

2个网络想要互通。

办公网网关路由器配置

```sh
cat /usr/local/admin/gre.sh #并把改脚本加入开机启动

#!/bin/bash
modprobe ip_gre #加载gre模块
ip tunnel add office mode gre remote 110.2.2.2 local 180.1.1.1 ttl 255 #使用公网 IP 建立 tunnel 名字叫 ”office“ 的 device，使用gre mode。指定远端的ip是110.2.2.2，本地ip是180.1.1.1。这里为了提升安全性，你可以配置iptables，公网ip只接收来自110.2.2.2的包，其他的都drop掉。
ip link set office up #启动device office 
ip link set office up mtu 1500 #设置 mtu 为1500
ip addr add 192.192.192.2/24 dev office #为 office 添加ip 192.192.192.2
echo 1 > /proc/sys/net/ipv4/ip_forward #让服务器支持转发
ip route add 10.1.1.0/24 dev office #添加路由，含义是：到10.1.1.0/24的包，由office设备负责转发
iptables -t nat -A POSTROUTING -d 10.1.1.0/24 -j SNAT --to 192.192.192.2 #否则 192.168.1.x 等机器访问 10.1.1.x网段不通
```

IDC网络配置

```sh
cat /usr/local/admin/gre.sh #并把改脚本加入开机启动

#!/bin/bash
modprobe ip_gre
ip tunnel add office mode gre remote 180.1.1.1 local 110.2.2.2 ttl 255
ip link set office up
ip link set office up mtu 1500
ip addr add 192.192.192.1/24 dev office #为office添加ip192.192.192.1
echo 1 > /proc/sys/net/ipv4/ip_forward
ip route add 192.168.1.0/24 dev office
iptables -t nat -A POSTROUTING -s 192.192.192.2 -d 10.1.0.0/16 -j SNAT --to 10.1.1.1 #否则192.168.1.X等机器访问10.1.1.x网段不通
iptables -A FORWARD -s 192.192.192.2 -m state --state NEW -m tcp -p tcp --dport 3306 -j DROP #禁止直接访问线上的3306，防止内网被破
```

GRE 技术本身还是存在一些不足之处：

（1）Tunnel 的数量问题

    GRE 是一种点对点（point to point）标准。Neutron 中，所有计算和网络节点之间都会建立 GRE Tunnel。当节点不多的时候，这种组网方法没什么问题。但是，当你在你的很大的数据中心中有 40000 个节点的时候，又会是怎样一种情形呢？使用标准 GRE的话，将会有 780 millions 个 tunnels。

（2）扩大的广播域

   GRE 不支持组播，因此一个网络（同一个 GRE Tunnel ID）中的一个虚机发出一个广播帧后，GRE 会将其广播到所有与该节点有隧道连接的节点。

（3）GRE 封装的IP包的过滤和负载均衡问题

    目前还是有很多的防火墙和三层网络设备无法解析 GRE Header，因此它们无法对 GRE 封装包做合适的过滤和负载均衡。 

### VxLAN


## 参考内容

OVS官网指导：https://docs.openvswitch.org/en/latest/tutorials/faucet/

