# AACネットワーク分科会 SDN勉強会第２回目（OpenFlowハンズオン）システム技術部：島内

前回の続きです。  
第一段階のゴールの復習です。『以下の構成をOpenFlowを用いて構築し、Node1/Node2間でのping疎通を通すこと』です。  


fig


まず、ここからやらなければいけないことを列挙します。  
1. 仮想ノードの作成(LinuxのNetwork NameSpaceを利用します)
1. 仮想スイッチの作成
1. インタフェースの作成
1. 仮想ノードにインタフェースをつける
1. 仮想スイッチにインタフェースをつける
1. インタフェースにIPアドレスを振る
1. スイッチにコントローラへの接続設定を行う
1. スイッチに、動作するOpenFlowのプロトコルバージョンを指定する


6までで、ネームスペース間の通信は可能になるはず。
7, 8を行うことで、DLUX上でも上記の構成が見えるはず(コントローラは見えませんが)

### 1. 仮想ノードの作成
ip netns add ネームスペース名 で追加できます。
```
[remote ~]$
[remote ~]$ sudo ip netns add node1
[remote ~]$ sudo ip netns add node2
[remote ~]$ sudo ip netns
node2
node1
[remote ~]$

```
これでnode1, node2ができました。

### 2. 仮想スイッチの作成
Open vSwitchに関するコマンドはovs-vsctl、ovs-ofctlなどのコマンドがあります。  
仮想スイッチを作成するなら、ovs-vsctl add-br 仮想スイッチ名 です。
```
[remote ~]$ sudo ovs-vsctl add-br vswitch00
[remote ~]$ sudo ovs-vsctl list-br
vswitch00
[remote ~]$
[remote ~]$

```
vswitch00ができました。

### 3. インタフェースの作成
インタフェースはveth peerというタイプでペアを作ります。  
で、一方をノード(ネームスペース)に、もう一方を仮想スイッチにつけます。  
まずは作成です。  
```
[remote ~]$
[remote ~]$ sudo ip link add name veth1s type veth peer name veth1
[remote ~]$ sudo ip link add name veth2s type veth peer name veth2
[remote ~]$ sudo ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000
    link/ether fa:16:3e:3b:82:40 brd ff:ff:ff:ff:ff:ff
3: ovs-system: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT
    link/ether ea:de:c8:c5:37:41 brd ff:ff:ff:ff:ff:ff
4: vswitch00: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT
    link/ether 4e:f1:8f:46:3c:45 brd ff:ff:ff:ff:ff:ff
5: veth1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT qlen 1000
    link/ether 36:0c:79:65:e3:cc brd ff:ff:ff:ff:ff:ff
6: veth1s: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT qlen 1000
    link/ether 4e:d5:d7:54:c7:7e brd ff:ff:ff:ff:ff:ff
7: veth2: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT qlen 1000
    link/ether fe:fd:4e:22:e1:49 brd ff:ff:ff:ff:ff:ff
8: veth2s: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT qlen 1000
    link/ether 2e:b1:12:e4:1b:81 brd ff:ff:ff:ff:ff:ff
[remote ~]$

```
できました。  
node1のインタフェースをveth1、node2のインタフェースをveth2、として仮想スイッチのインタフェースをveth1s、veth2sとしました。(1sはnode1と、2sはnode2とつながります)  

### 4. 仮想ノードにインタフェースをつける
1で作ったノード(ネームスペース)に仮想インタフェースを割り当てます。
```
[remote ~]$
[remote ~]$ sudo ip link set veth1 netns node1
[remote ~]$ sudo ip link set veth2 netns node2
[remote ~]$
[remote ~]$ sudo ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000
    link/ether fa:16:3e:3b:82:40 brd ff:ff:ff:ff:ff:ff
3: ovs-system: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT
    link/ether ea:de:c8:c5:37:41 brd ff:ff:ff:ff:ff:ff
4: vswitch00: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT
    link/ether 4e:f1:8f:46:3c:45 brd ff:ff:ff:ff:ff:ff
6: veth1s: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT qlen 1000
    link/ether 4e:d5:d7:54:c7:7e brd ff:ff:ff:ff:ff:ff
8: veth2s: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT qlen 1000
    link/ether 2e:b1:12:e4:1b:81 brd ff:ff:ff:ff:ff:ff
[remote ~]$
[remote ~]$
[remote ~]$ sudo ip netns exec node1 ip link
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN mode DEFAULT
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
5: veth1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT qlen 1000
    link/ether 36:0c:79:65:e3:cc brd ff:ff:ff:ff:ff:ff
[remote ~]$ sudo ip netns exec node2 ip link
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN mode DEFAULT
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
7: veth2: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT qlen 1000
    link/ether fe:fd:4e:22:e1:49 brd ff:ff:ff:ff:ff:ff
[remote ~]$

```
ちゃんとネームスペース内についていることが確認できます。


### 5. 仮想スイッチにインタフェースをつける
こちらはovs-vsctlコマンドで実行します。
```
[remote ~]$
[remote ~]$ sudo ovs-vsctl add-port vswitch00 veth1s
[remote ~]$ sudo ovs-vsctl add-port vswitch00 veth2s
[remote ~]$
[remote ~]$ sudo ovs-vsctl list-ports vswitch00
veth1s
veth2s
[remote ~]$

```
これでつながりました。

### 6. 仮想ノードにつけたインタフェースにIPアドレスを振る
IPアドレスを振って、各インタフェースをUPします。
```
[remote ~]$ sudo ip netns exec node1 ip addr add 10.0.0.1/24 dev veth1
[remote ~]$ sudo ip netns exec node2 ip addr add 10.0.0.2/24 dev veth2
[remote ~]$
[remote ~]$ sudo ip netns exec node1 ip link set veth1 up
[remote ~]$ sudo ip netns exec node2 ip link set veth2 up
[remote ~]$
[remote ~]$ sudo ip link set veth1s up
[remote ~]$ sudo ip link set veth2s up
[remote ~]$
[remote ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether fa:16:3e:3b:82:40 brd ff:ff:ff:ff:ff:ff
    inet xxx.xxx.xxx.xxx scope global dynamic eth0
       valid_lft 73698sec preferred_lft 73698sec
    inet6 fe80::f816:3eff:fe3b:8240/64 scope link
       valid_lft forever preferred_lft forever
3: ovs-system: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN
    link/ether ea:de:c8:c5:37:41 brd ff:ff:ff:ff:ff:ff
6: veth1s: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast master ovs-system state UP qlen 1000
    link/ether 4e:d5:d7:54:c7:7e brd ff:ff:ff:ff:ff:ff
    inet6 fe80::4cd5:d7ff:fe54:c77e/64 scope link
       valid_lft forever preferred_lft forever
8: veth2s: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast master ovs-system state UP qlen 1000
    link/ether 2e:b1:12:e4:1b:81 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::2cb1:12ff:fee4:1b81/64 scope link
       valid_lft forever preferred_lft forever
9: vswitch00: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN
    link/ether 1a:e0:ca:82:ae:46 brd ff:ff:ff:ff:ff:ff
[remote ~]$
[remote ~]$ sudo ip netns exec node1 ip a
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
5: veth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 36:0c:79:65:e3:cc brd ff:ff:ff:ff:ff:ff
    inet 10.0.0.1/24 scope global veth1
       valid_lft forever preferred_lft forever
    inet6 fe80::340c:79ff:fe65:e3cc/64 scope link
       valid_lft forever preferred_lft forever
[remote ~]$ sudo ip netns exec node2 ip a
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
7: veth2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether fe:fd:4e:22:e1:49 brd ff:ff:ff:ff:ff:ff
    inet 10.0.0.2/24 scope global veth2
       valid_lft forever preferred_lft forever
    inet6 fe80::fcfd:4eff:fe22:e149/64 scope link
       valid_lft forever preferred_lft forever
[remote ~]$
[remote ~]$

```

きちんとIPが振られてUPしています。  

ここまでで、pingは通るはずなのでちょっと試してみます。
```
[remote ~]$
[remote ~]$
[remote ~]$ sudo ip netns exec node1 ping 10.0.0.2
PING 10.0.0.2 (10.0.0.2) 56(84) bytes of data.
64 bytes from 10.0.0.2: icmp_seq=1 ttl=64 time=0.546 ms
64 bytes from 10.0.0.2: icmp_seq=2 ttl=64 time=0.092 ms
64 bytes from 10.0.0.2: icmp_seq=3 ttl=64 time=0.088 ms
^C
--- 10.0.0.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2000ms
rtt min/avg/max/mdev = 0.088/0.242/0.546/0.214 ms
[remote ~]$ sudo ip netns exec node2 ping 10.0.0.1
PING 10.0.0.1 (10.0.0.1) 56(84) bytes of data.
64 bytes from 10.0.0.1: icmp_seq=1 ttl=64 time=0.268 ms
64 bytes from 10.0.0.1: icmp_seq=2 ttl=64 time=0.083 ms
64 bytes from 10.0.0.1: icmp_seq=3 ttl=64 time=0.082 ms
64 bytes from 10.0.0.1: icmp_seq=4 ttl=64 time=0.081 ms
^C
--- 10.0.0.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 2999ms
rtt min/avg/max/mdev = 0.081/0.128/0.268/0.081 ms
[remote ~]$

```
通りましたね。

### 7. スイッチにコントローラへの接続設定を行う
### 8. スイッチに動作するOpenFlowのプロトコルバージョンを指定する
続いて、仮想スイッチとコントローラを接続し、OpenFlowのプロトコルバージョンを指定します。  
また、データパスIDというものも指定しておきます。これはOpenFlowコントローラが仮想スイッチを識別するための識別IDになります。指定しなければMACアドレスをもとに決定されます。
```
[remote ~]$ sudo ovs-vsctl set-controller vswitch00 tcp:OpenDayLightをインストールしたホストのIPアドレス
[remote ~]$ sudo ovs-vsctl set bridge vswitch00 protocols=OpenFlow13
[remote ~]$ sudo ovs-vsctl set bridge vswitch00 other-config:datapath-id=10
[remote ~]$

```
これでDLUXで見えるようになったはず、、、。


fig


見れました。仮想ノードがないのは、まだスイッチが認識してないためです。  
認識させるためにpingを打ちます。
```
[remote ~]$
[remote ~]$
[remote ~]$ sudo ip netns exec node1 ping 10.0.0.2
PING 10.0.0.2 (10.0.0.2) 56(84) bytes of data.
64 bytes from 10.0.0.2: icmp_seq=1 ttl=64 time=0.595 ms
64 bytes from 10.0.0.2: icmp_seq=2 ttl=64 time=0.331 ms
64 bytes from 10.0.0.2: icmp_seq=3 ttl=64 time=0.388 ms
64 bytes from 10.0.0.2: icmp_seq=4 ttl=64 time=0.399 ms
64 bytes from 10.0.0.2: icmp_seq=5 ttl=64 time=0.351 ms
^C
--- 10.0.0.2 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 3999ms
rtt min/avg/max/mdev = 0.331/0.412/0.595/0.097 ms
[remote ~]$ sudo ip netns exec node2 ping 10.0.0.1
PING 10.0.0.1 (10.0.0.1) 56(84) bytes of data.
64 bytes from 10.0.0.1: icmp_seq=1 ttl=64 time=0.619 ms
64 bytes from 10.0.0.1: icmp_seq=2 ttl=64 time=0.544 ms
64 bytes from 10.0.0.1: icmp_seq=3 ttl=64 time=0.344 ms
64 bytes from 10.0.0.1: icmp_seq=4 ttl=64 time=0.349 ms
64 bytes from 10.0.0.1: icmp_seq=5 ttl=64 time=0.392 ms
^C
--- 10.0.0.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4000ms
rtt min/avg/max/mdev = 0.344/0.449/0.619/0.113 ms
[remote ~]$

```
もしここで通らない場合は、一度
`ovs-ofctl dump-flows vswitch00 --protocol=OpenFlow13`
を打ってから再度確認してみてください。

再度DLUXを確認してみます。


fig


仮想ノードも表示されました。  

とりあえず、最小構成での動作確認が取れたので第２回はここで完了です。おつかれさまでした。  
以降はOpenFlowを用いたフロー制御に取り組みたいと思います。  

次のゴールはスイッチの追加とフロー制御による通信可否の切り替え。です。  
構成図は以下です。


fig


