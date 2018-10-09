# AAC�l�b�g���[�N���ȉ� SDN�׋����Q��ځiOpenFlow�n���Y�I���j�V�X�e���Z�p���F����

�O��̑����ł��B  
���i�K�̃S�[���̕��K�ł��B�w�ȉ��̍\����OpenFlow��p���č\�z���ANode1/Node2�Ԃł�ping�a�ʂ�ʂ����Ɓx�ł��B  


fig


�܂��A����������Ȃ���΂����Ȃ����Ƃ�񋓂��܂��B  
1. ���z�m�[�h�̍쐬(Linux��Network NameSpace�𗘗p���܂�)
1. ���z�X�C�b�`�̍쐬
1. �C���^�t�F�[�X�̍쐬
1. ���z�m�[�h�ɃC���^�t�F�[�X������
1. ���z�X�C�b�`�ɃC���^�t�F�[�X������
1. �C���^�t�F�[�X��IP�A�h���X��U��
1. �X�C�b�`�ɃR���g���[���ւ̐ڑ��ݒ���s��
1. �X�C�b�`�ɁA���삷��OpenFlow�̃v���g�R���o�[�W�������w�肷��


6�܂łŁA�l�[���X�y�[�X�Ԃ̒ʐM�͉\�ɂȂ�͂��B
7, 8���s�����ƂŁADLUX��ł���L�̍\����������͂�(�R���g���[���͌����܂���)

### 1. ���z�m�[�h�̍쐬
ip netns add �l�[���X�y�[�X�� �Œǉ��ł��܂��B
```
[remote ~]$
[remote ~]$ sudo ip netns add node1
[remote ~]$ sudo ip netns add node2
[remote ~]$ sudo ip netns
node2
node1
[remote ~]$

```
�����node1, node2���ł��܂����B

### 2. ���z�X�C�b�`�̍쐬
Open vSwitch�Ɋւ���R�}���h��ovs-vsctl�Aovs-ofctl�Ȃǂ̃R�}���h������܂��B  
���z�X�C�b�`���쐬����Ȃ�Aovs-vsctl add-br ���z�X�C�b�`�� �ł��B
```
[remote ~]$ sudo ovs-vsctl add-br vswitch00
[remote ~]$ sudo ovs-vsctl list-br
vswitch00
[remote ~]$
[remote ~]$

```
vswitch00���ł��܂����B

### 3. �C���^�t�F�[�X�̍쐬
�C���^�t�F�[�X��veth peer�Ƃ����^�C�v�Ńy�A�����܂��B  
�ŁA������m�[�h(�l�[���X�y�[�X)�ɁA������������z�X�C�b�`�ɂ��܂��B  
�܂��͍쐬�ł��B  
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
�ł��܂����B  
node1�̃C���^�t�F�[�X��veth1�Anode2�̃C���^�t�F�[�X��veth2�A�Ƃ��ĉ��z�X�C�b�`�̃C���^�t�F�[�X��veth1s�Aveth2s�Ƃ��܂����B(1s��node1�ƁA2s��node2�ƂȂ���܂�)  

### 4. ���z�m�[�h�ɃC���^�t�F�[�X������
1�ō�����m�[�h(�l�[���X�y�[�X)�ɉ��z�C���^�t�F�[�X�����蓖�Ă܂��B
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
�����ƃl�[���X�y�[�X���ɂ��Ă��邱�Ƃ��m�F�ł��܂��B


### 5. ���z�X�C�b�`�ɃC���^�t�F�[�X������
�������ovs-vsctl�R�}���h�Ŏ��s���܂��B
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
����łȂ���܂����B

### 6. ���z�m�[�h�ɂ����C���^�t�F�[�X��IP�A�h���X��U��
IP�A�h���X��U���āA�e�C���^�t�F�[�X��UP���܂��B
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

�������IP���U����UP���Ă��܂��B  

�����܂łŁAping�͒ʂ�͂��Ȃ̂ł�����Ǝ����Ă݂܂��B
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
�ʂ�܂����ˁB

### 7. �X�C�b�`�ɃR���g���[���ւ̐ڑ��ݒ���s��
### 8. �X�C�b�`�ɓ��삷��OpenFlow�̃v���g�R���o�[�W�������w�肷��
�����āA���z�X�C�b�`�ƃR���g���[����ڑ����AOpenFlow�̃v���g�R���o�[�W�������w�肵�܂��B  
�܂��A�f�[�^�p�XID�Ƃ������̂��w�肵�Ă����܂��B�����OpenFlow�R���g���[�������z�X�C�b�`�����ʂ��邽�߂̎���ID�ɂȂ�܂��B�w�肵�Ȃ����MAC�A�h���X�����ƂɌ��肳��܂��B
```
[remote ~]$ sudo ovs-vsctl set-controller vswitch00 tcp:OpenDayLight���C���X�g�[�������z�X�g��IP�A�h���X
[remote ~]$ sudo ovs-vsctl set bridge vswitch00 protocols=OpenFlow13
[remote ~]$ sudo ovs-vsctl set bridge vswitch00 other-config:datapath-id=10
[remote ~]$

```
�����DLUX�Ō�����悤�ɂȂ����͂��A�A�A�B


fig


����܂����B���z�m�[�h���Ȃ��̂́A�܂��X�C�b�`���F�����ĂȂ����߂ł��B  
�F�������邽�߂�ping��ł��܂��B
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
���������Œʂ�Ȃ��ꍇ�́A��x
`ovs-ofctl dump-flows vswitch00 --protocol=OpenFlow13`
��ł��Ă���ēx�m�F���Ă݂Ă��������B

�ēxDLUX���m�F���Ă݂܂��B


fig


���z�m�[�h���\������܂����B  

�Ƃ肠�����A�ŏ��\���ł̓���m�F����ꂽ�̂ő�Q��͂����Ŋ����ł��B�����ꂳ�܂ł����B  
�ȍ~��OpenFlow��p�����t���[����Ɏ��g�݂����Ǝv���܂��B  

���̃S�[���̓X�C�b�`�̒ǉ��ƃt���[����ɂ��ʐM�ۂ̐؂�ւ��B�ł��B  
�\���}�͈ȉ��ł��B


fig


