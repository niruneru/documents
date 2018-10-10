## AACネットワーク分科会 SDN勉強会第１回目（OpenFlowの紹介とハンズオン）システム技術部：島内 (2015/12/15)

### OpenFlowとは？
以下、JPNICのサイトからの引用
> 制御機能と転送機能が共存する既存のネットワーク機器とは異なり、OpenFlowでは、制御部と転送部を分離したアーキテクチャを採用しています。  
> ネットワーク管理者は、OpenFlowコントローラと呼ばれる制御部を、自ら設計・実装することで、必要な制御機能を自由に実現することができます。

詳しくはこちらをご参照
[引用元:]https://www.nic.ad.jp/ja/newsletter/No52/0800.html

### つくってみる
というOpenFlowを用いて、ミニマムなSDNを作成してブログ的にまとめてみましたので、それを元に時間の許す限りハンズオンしたいと思います。  
（社内勉強会なこともありユルい感じで進みますがご容赦ください。）

まず、今回のゴールは『以下の構成をOpenFlowを用いて構築し、Node1/Node2間でのping疎通を通すこと』です。  

![01_01](https://github.com/niruneru/documents/blob/media/01_01.gif)

使用するOS・OSSは以下の通りです。  
- CentOS 7.1
- Open vSwitch 2.4.0 (仮想スイッチ)
- OpenDayLight Lithium SR-2(OpenFlowコントローラ)

### 1. Open vSwitchのインストール
yum searchしても見つからず、調べたところ、、、
- ソースをダウンロードしてmake installする。
- RDOのリポジトリからインストールする。
のどちらかを選択する必要があるそうです。	
私は後者の方法で実施しました。RDOはRedHatのOpenstackユーザを支援するコミュニティだそうです。  
で、そのRDO公式サイトに行くと、トップページにQuickstartへの誘導があったので、そこをポチっとすると  

![01_02](https://github.com/niruneru/documents/blob/media/01_02.gif)

ということで、Step1まで実施してリポジトリを追加します。  
```
[remote ~]$
[remote ~]$ sudo yum -y update
読み込んだプラグイン:fastestmirror
base                                                                                                                                                                     | 3.6 kB  00:00:00
extras                                                                                                                                                                   | 3.4 kB  00:00:00
updates                                                                                                                                                                  | 3.4 kB  00:00:00
(1/4): extras/7/x86_64/primary_db                                                                                                                                        | 117 kB  00:00:00
(2/4): base/7/x86_64/group_gz                                                                                                                                            | 154 kB  00:00:00
(3/4): base/7/x86_64/primary_db                                                                                                                                          | 5.1 MB  00:00:00
(4/4): updates/7/x86_64/primary_db                                                                                                                                       | 4.7 MB  00:00:00
Determining fastest mirrors
 * base: www.ftp.ne.jp
 * extras: www.ftp.ne.jp
 * updates: www.ftp.ne.jp
依存性の解決をしています
--> トランザクションの確認を実行しています。
---> パッケージ NetworkManager.x86_64 1:1.0.0-14.git20150121.b4ea599c.el7 を 更新
---> パッケージ NetworkManager.x86_64 1:1.0.0-16.git20150121.b4ea599c.el7_1 を アップデート
---> パッケージ NetworkManager-libnm.x86_64 1:1.0.0-14.git20150121.b4ea599c.el7 を 更新
---> パッケージ NetworkManager-libnm.x86_64 1:1.0.0-16.git20150121.b4ea599c.el7_1 を アップデート
---> パッケージ NetworkManager-team.x86_64 1:1.0.0-14.git20150121.b4ea599c.el7 を 更新
---> パッケージ NetworkManager-team.x86_64 1:1.0.0-16.git20150121.b4ea599c.el7_1 を アップデート
---> パッケージ NetworkManager-tui.x86_64 1:1.0.0-14.git20150121.b4ea599c.el7 を 更新
---> パッケージ NetworkManager-tui.x86_64 1:1.0.0-16.git20150121.b4ea599c.el7_1 を アップデート
---> パッケージ bind-libs-lite.x86_64 32:9.9.4-18.el7 を 更新
---> パッケージ bind-libs-lite.x86_64 32:9.9.4-18.el7_1.5 を アップデート
---> パッケージ bind-license.noarch 32:9.9.4-18.el7 を 更新
---> パッケージ bind-license.noarch 32:9.9.4-18.el7_1.5 を アップデート
---> パッケージ binutils.x86_64 0:2.23.52.0.1-30.el7 を 更新
---> パッケージ binutils.x86_64 0:2.23.52.0.1-30.el7_1.2 を アップデート
---> パッケージ ca-certificates.noarch 0:2014.1.98-72.el7 を 更新
---> パッケージ ca-certificates.noarch 0:2015.2.4-70.0.el7_1 を アップデート
---> パッケージ centos-logos.noarch 0:70.0.6-1.el7.centos を 更新
---> パッケージ centos-logos.noarch 0:70.0.6-3.el7.centos を アップデート

略

  openssh-clients.x86_64 0:6.6.1p1-12.el7_1
  openssl.x86_64 1:1.0.1e-42.el7.9
  pam.x86_64 0:1.1.8-12.el7_1.1
  python-libs.x86_64 0:2.7.5-18.el7_1.1
  selinux-policy.noarch 0:3.13.1-23.el7_1.21
  sqlite.x86_64 0:3.7.17-6.el7_1.1
  systemd-libs.x86_64 0:208-20.el7_1.6
  trousers.x86_64 0:0.3.11.2-4.el7_1
  util-linux.x86_64 0:2.23.2-22.el7_1.1

完了しました!
[remote ~]$
[remote ~]$
[remote ~]$
[remote ~]$ sudo yum install -y https://rdoproject.org/repos/rdo-release.rpm
読み込んだプラグイン:fastestmirror
rdo-release.rpm                                                                                                                                                          | 5.1 kB  00:00:00
/var/tmp/yum-root-LhteEU/rdo-release.rpm を調べています: rdo-release-liberty-2.noarch
/var/tmp/yum-root-LhteEU/rdo-release.rpm をインストール済みとして設定しています
依存性の解決をしています
--> トランザクションの確認を実行しています。
---> パッケージ rdo-release.noarch 0:liberty-2 を インストール
--> 依存性解決を終了しました。

依存性を解決しました

================================================================================================================================================================================================
 Package                                        アーキテクチャー                          バージョン                                      リポジトリー                                     容量
================================================================================================================================================================================================
インストール中:
 rdo-release                                    noarch                                    liberty-2                                       /rdo-release                                    1.4 k

トランザクションの要約
================================================================================================================================================================================================
インストール  1 パッケージ

合計容量: 1.4 k
インストール容量: 1.4 k
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  インストール中          : rdo-release-liberty-2.noarch                                                                                                                                    1/1
  検証中                  : rdo-release-liberty-2.noarch                                                                                                                                    1/1

インストール:
  rdo-release.noarch 0:liberty-2

完了しました!
[remote ~]$
```

続いてOpen vSwitchをインストールします。
```
[remote ~]$
[remote ~]$
[remote ~]$ sudo yum -y install openvswitch
読み込んだプラグイン:fastestmirror
openstack-liberty                                                                                                                                                        | 2.9 kB  00:00:00
openstack-liberty/x86_64/primary_db                                                                                                                                      | 354 kB  00:00:03
Loading mirror speeds from cached hostfile
 * base: www.ftp.ne.jp
 * extras: www.ftp.ne.jp
 * updates: www.ftp.ne.jp
依存性の解決をしています
--> トランザクションの確認を実行しています。
---> パッケージ openvswitch.x86_64 0:2.4.0-1.el7 を インストール
--> 依存性解決を終了しました。

依存性を解決しました

================================================================================================================================================================================================
 Package                                       アーキテクチャー                         バージョン                                    リポジトリー                                         容量
================================================================================================================================================================================================
インストール中:
 openvswitch                                   x86_64                                   2.4.0-1.el7                                   openstack-liberty                                   2.2 M

トランザクションの要約
================================================================================================================================================================================================
インストール  1 パッケージ

総ダウンロード容量: 2.2 M
インストール容量: 8.9 M
Downloading packages:
警告: /var/cache/yum/x86_64/7/openstack-liberty/packages/openvswitch-2.4.0-1.el7.x86_64.rpm: ヘッダー V4 RSA/SHA1 Signature、鍵 ID 764429e6: NOKEY=========   ] 293 kB/s | 2.1 MB  00:00:00 ETA
openvswitch-2.4.0-1.el7.x86_64.rpm の公開鍵がインストールされていません
openvswitch-2.4.0-1.el7.x86_64.rpm                                                                                                                                       | 2.2 MB  00:00:07
file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-Cloud から鍵を取得中です。
Importing GPG key 0x764429E6:
 Userid     : "CentOS Cloud SIG (http://wiki.centos.org/SpecialInterestGroup/Cloud) <security@centos.org>"
 Fingerprint: 736a f511 6d9c 40e2 af6b 074b f9b9 fee7 7644 29e6
 Package    : rdo-release-liberty-2.noarch (@/rdo-release)
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-Cloud
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  インストール中          : openvswitch-2.4.0-1.el7.x86_64                                                                                                                                  1/1
  検証中                  : openvswitch-2.4.0-1.el7.x86_64                                                                                                                                  1/1

インストール:
  openvswitch.x86_64 0:2.4.0-1.el7

完了しました!
[remote ~]$
```

### 2. OpenDayLightのインストール
続いて、コントローラとなるOpenDayLightのインストールです。
OpenDayLightのインストールはOpenDayLightを実行するユーザで行う必要があるそうです。気をつけましょう。
OpenDayLightはjavaで作られているので、動かすためにまずJDKをインストールします。

```
[remote ~]$
[remote ~]$
[remote ~]$ sudo yum -y install java-1.8.0-openjdk
読み込んだプラグイン:fastestmirror
Loading mirror speeds from cached hostfile
 * base: www.ftp.ne.jp
 * extras: www.ftp.ne.jp
 * updates: www.ftp.ne.jp
依存性の解決をしています
--> トランザクションの確認を実行しています。
---> パッケージ java-1.8.0-openjdk.x86_64 1:1.8.0.65-2.b17.el7_1 を インストール
--> 依存性の処理をしています: java-1.8.0-openjdk-headless = 1:1.8.0.65-2.b17.el7_1 のパッケージ: 1:java-1.8.0-openjdk-1.8.0.65-2.b17.el7_1.x86_64
--> 依存性の処理をしています: xorg-x11-fonts-Type1 のパッケージ: 1:java-1.8.0-openjdk-1.8.0.65-2.b17.el7_1.x86_64
--> 依存性の処理をしています: libpng15.so.15(PNG15_0)(64bit) のパッケージ: 1:java-1.8.0-openjdk-1.8.0.65-2.b17.el7_1.x86_64
--> 依存性の処理をしています: libjvm.so(SUNWprivate_1.1)(64bit) のパッケージ: 1:java-1.8.0-openjdk-1.8.0.65-2.b17.el7_1.x86_64

略

  検証中                  : libXext-1.3.2-2.1.el7.x86_64                                                                                                                                  26/27
  検証中                  : fontconfig-2.10.95-7.el7.x86_64                                                                                                                               27/27

インストール:
  java-1.8.0-openjdk.x86_64 1:1.8.0.65-2.b17.el7_1

依存性関連をインストールしました:
  fontconfig.x86_64 0:2.10.95-7.el7             fontpackages-filesystem.noarch 0:1.44-8.el7    giflib.x86_64 0:4.1.6-9.el7       java-1.8.0-openjdk-headless.x86_64 1:1.8.0.65-2.b17.el7_1
  javapackages-tools.noarch 0:3.4.1-6.el7_0     libICE.x86_64 0:1.0.8-7.el7                    libSM.x86_64 0:1.2.1-7.el7        libX11.x86_64 0:1.6.0-2.1.el7
  libX11-common.noarch 0:1.6.0-2.1.el7          libXau.x86_64 0:1.0.8-2.1.el7                  libXext.x86_64 0:1.3.2-2.1.el7    libXfont.x86_64 0:1.4.7-3.el7_1
  libXi.x86_64 0:1.7.2-2.1.el7                  libXrender.x86_64 0:0.9.8-2.1.el7              libXtst.x86_64 0:1.2.2-2.1.el7    libfontenc.x86_64 0:1.1.1-5.el7
  libjpeg-turbo.x86_64 0:1.2.90-5.el7           libpng.x86_64 2:1.5.13-5.el7                   libxcb.x86_64 0:1.9-5.el7         libxslt.x86_64 0:1.1.28-5.el7
  python-javapackages.noarch 0:3.4.1-6.el7_0    python-lxml.x86_64 0:3.2.1-4.el7               ttmkfdir.x86_64 0:3.0.9-41.el7    tzdata-java.noarch 0:2015g-1.el7
  xorg-x11-font-utils.x86_64 1:7.5-18.1.el7     xorg-x11-fonts-Type1.noarch 0:7.5-9.el7

完了しました!
[remote ~]$
```
  
OpenDayLightは、環境変数JAVA_HOMEに記載されているJVMを使って起動するため、この設定をしておきます。
```
[remote ~]$
[remote ~]$ export JAVA_HOME=/etc/alternatives/jre_1.8.0_openjdk
[remote ~]$ echo 'export JAVA_HOME=/etc/alternatives/jre_1.8.0_openjdk' >> .bashrc
```
  
続いて、いよいよOpenDayLightをインストールします。  
  
公式サイトに行きます。


![01_03](https://github.com/niruneru/documents/blob/media/01_03.gif)

下の方にDOWNLOAD NOWがあるので、これをクリックすると


![01_04](https://github.com/niruneru/documents/blob/media/01_04.gif)



一覧が出ます。お目当てのLithium-SR2のPre-Built Tar FileのダウンロードURLをコピーして、curlかwgetあたりで取得します。

![fig5](https://github.com/niruneru/documents/blob/media/01_05.gif)

```
[remote ~]$
[remote ~]$
[remote ~]$ wget https://nexus.opendaylight.org/content/repositories/opendaylight.release/org/opendaylight/integration/distribution-karaf/0.3.2-Lithium-SR2/distribution-karaf-0.3.2-Lithium-SR2.tar.gz
--2015-11-17 03:57:27--  https://nexus.opendaylight.org/content/repositories/opendaylight.release/org/opendaylight/integration/distribution-karaf/0.3.2-Lithium-SR2/distribution-karaf-0.3.2-Lithium-SR2.tar.gz
nexus.opendaylight.org (nexus.opendaylight.org) をDNSに問いあわせています... 23.253.119.7
nexus.opendaylight.org (nexus.opendaylight.org)|23.253.119.7|:443 に接続しています... 接続しました。
HTTP による接続要求を送信しました、応答を待っています... 200 OK
長さ: 282951021 (270M) [application/x-gzip]
`distribution-karaf-0.3.2-Lithium-SR2.tar.gz' に保存中

100%[======================================================================================================================================================>] 282,951,021 1.22MB/s 時間 2m 9s

2015-11-17 03:59:37 (2.09 MB/s) - `distribution-karaf-0.3.2-Lithium-SR2.tar.gz' へ保存完了 [282951021/282951021]

[remote ~]$
```

ダウンロード完了。ホームディレクトリでもどこでもいいそうですが、解凍・展開します。
```
[remote ~]$
[remote ~]$ tar zxvf distribution-karaf-0.3.2-Lithium-SR2.tar.gz
distribution-karaf-0.3.2-Lithium-SR2/configuration/
distribution-karaf-0.3.2-Lithium-SR2/data/
distribution-karaf-0.3.2-Lithium-SR2/data/tmp/
distribution-karaf-0.3.2-Lithium-SR2/deploy/
distribution-karaf-0.3.2-Lithium-SR2/etc/
distribution-karaf-0.3.2-Lithium-SR2/externalapps/
distribution-karaf-0.3.2-Lithium-SR2/lib/

略

distribution-karaf-0.3.2-Lithium-SR2/bin/karaf.bat
distribution-karaf-0.3.2-Lithium-SR2/bin/setenv.bat
distribution-karaf-0.3.2-Lithium-SR2/bin/shell.bat
distribution-karaf-0.3.2-Lithium-SR2/bin/start.bat
distribution-karaf-0.3.2-Lithium-SR2/bin/status.bat
distribution-karaf-0.3.2-Lithium-SR2/bin/stop.bat
[remote ~]$
```

以上で、Open vSwitchとOpenDayLightのインストールが完了しました。  
次に、起動確認を行います。

### 3. Open vSwitchの起動確認
```
[remote ~]$
[remote ~]$
[remote ~]$
[remote ~]$ systemctl status openvswitch
openvswitch.service - Open vSwitch
   Loaded: loaded (/usr/lib/systemd/system/openvswitch.service; disabled)
   Active: inactive (dead)

[remote ~]$ sudo systemctl enable openvswitch
ln -s '/usr/lib/systemd/system/openvswitch.service' '/etc/systemd/system/multi-user.target.wants/openvswitch.service'
[remote ~]$ sudo systemctl start openvswitch
[remote ~]$ systemctl status openvswitch
openvswitch.service - Open vSwitch
   Loaded: loaded (/usr/lib/systemd/system/openvswitch.service; enabled)
   Active: active (exited) since 火 2015-11-17 04:15:40 JST; 9s ago
  Process: 28846 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
 Main PID: 28846 (code=exited, status=0/SUCCESS)

[remote ~]$
```
起動しました。以下のコマンドでバージョンの確認ができます。
```
[remote ~]$
[remote ~]$ ovs-vsctl -V
ovs-vsctl (Open vSwitch) 2.4.0
Compiled Oct  7 2015 18:01:06
DB Schema 7.12.1
[remote ~]$
```

### 4. OpenDayLightの起動確認
続いて、OpenDayLightの起動確認を行います。ダウンロードしたtarを解凍・展開したユーザで展開したディレクトリ内に入り、./bin/karafと打ちます。
※ついでに長ったらしいディレクトリ名を変更しています。
なお、binの下のコマンドですが
- ./start：バックグラウンド実行
- ./client：Karaf Consoleへ接続する。（バックグランド実行したあとなどに利用する）
- ./status：OpenDaylightの動作状況の確認  
などとなっています。  

```
[remote ~]$ mv distribution-karaf-0.3.2-Lithium-SR2 OpenDaylight-Lithium-SR2
[remote ~]$
[remote ~]$ cd OpenDaylight-lithium-SR2/
[remote OpenDaylight-Lithium-SR2]$ ./bin/karaf
OpenJDK 64-Bit Server VM warning: ignoring option MaxPermSize=512m; support was removed in 8.0

    ________                       ________                .__  .__       .__     __
    \_____  \ ______   ____   ____ \______ \ _____  ___.__.|  | |__| ____ |  |___/  |_
     /   |   \\____ \_/ __ \ /    \ |    |  \\__  \<   |  ||  | |  |/ ___\|  |  \   __\
    /    |    \  |_> >  ___/|   |  \|    `   \/ __ \\___  ||  |_|  / /_/  >   Y  \  |
    \_______  /   __/ \___  >___|  /_______  (____  / ____||____/__\___  /|___|  /__|
            \/|__|        \/     \/        \/     \/\/            /_____/      \/


Hit '<tab>' for a list of available commands
and '[cmd] --help' for help on a specific command.
Hit '<ctrl-d>' or type 'system:shutdown' or 'logout' to shutdown OpenDaylight.

opendaylight-user@root>
```

起動完了です。終了するときはlogoutと打ちます。  

### 5. GUIによる管理を提供するOpenDayLightのDLUX
DLUXを使用すると、WebブラウザのGUIを通して仮想ネットワークの管理を行うことができます。こんな画面です。↓(これは画像検索で拾ったもの)

![fig6](https://github.com/niruneru/documents/blob/media/01_06.gif)

まず、トップページにアクセスします。ブラウザから  
`http://OpenDayLightをインストールしたホストのアドレス:8181/dlux/index.html`  
へアクセスします。  

![fig7](https://github.com/niruneru/documents/blob/media/01_07.gif)

CentOS側のfirewallの設定を見直します。
```
[local ~]$
[local ~]$ sudo firewall-cmd --list-all
[sudo] password for kehukef:
public (default, active)
  interfaces: eth0
  sources:
  services: dhcpv6-client ssh
  ports:
  masquerade: no
  forward-ports:
  icmp-blocks:
  rich rules:

[local ~]$
[local ~]$ sudo firewall-cmd --list-all-zones
block
  interfaces:
  sources:
  services:
  ports:
  masquerade: no
  forward-ports:
  icmp-blocks:
  rich rules:

省略

public (default, active)
  interfaces: eth0
  sources:
  services: dhcpv6-client ssh
  ports:
  masquerade: no
  forward-ports:
  icmp-blocks:
  rich rules:

trusted
  interfaces:
  sources:
  services:
  ports:
  masquerade: no
  forward-ports:
  icmp-blocks:
  rich rules:

work
  interfaces:
  sources:
  services: dhcpv6-client ipp-client ssh
  ports:
  masquerade: no
  forward-ports:
  icmp-blocks:
  rich rules:

[local ~]$
[local ~]$
[local ~]$ sudo firewall-cmd --list-services --zone=public --permanent
dhcpv6-client ssh
[local ~]$
```

eth0にpublicが適用されています。
というわけで、publicに8181を追加します。一応、正攻法？で/etc/firewalld/services/にファイルを追加します。

```
[local ~]$
[local ~]$
[local ~]$ sudo cp -p /usr/lib/firewalld/services/http.xml /etc/firewalld/services/opendaylight.xml
[local ~]$ sudo vi /etc/firewalld/services/opendaylight.xml
[local ~]$
[local ~]$ sudo cat /etc/firewalld/services/opendaylight.xml
<?xml version="1.0" encoding="utf-8"?>
<service>
  <short>opendaylight</short>
  <description>HTTP is the protocol used to serve Web pages. If you plan to make your Web server publicly available, enable this option. This option is not required for viewing pages locally or developing Web pages.</description>
  <port protocol="tcp" port="8181"/>
</service>
[local ~]$
[local ~]$ sudo firewall-cmd --add-service=opendaylight --zone=public --permanent
success
[local ~]$
[local ~]$ sudo firewall-cmd --list-services --zone=public --permanent
dhcpv6-client opendaylight ssh
[local ~]$
```

※<description>タグの中は未修正です。  
  
で、sudo firewall-cmd --reloadして反映させますと、、、。



![fig8](https://github.com/niruneru/documents/blob/media/01_08.gif)



やっぱり駄目でした。調べたところ、そもそもインストールしただけではDLUXを使うための基本的なプラグインが入っていないとのことです。
というわけでプラグインを追加します。  
まず、今入っているプラグインを確認します。  
```
[remote openDayLight-Lithium-SR2]$ ./bin/karaf
OpenJDK 64-Bit Server VM warning: ignoring option MaxPermSize=512m; support was removed in 8.0

    ________                       ________                .__  .__       .__     __
    \_____  \ ______   ____   ____ \______ \ _____  ___.__.|  | |__| ____ |  |___/  |_
     /   |   \\____ \_/ __ \ /    \ |    |  \\__  \<   |  ||  | |  |/ ___\|  |  \   __\
    /    |    \  |_> >  ___/|   |  \|    `   \/ __ \\___  ||  |_|  / /_/  >   Y  \  |
    \_______  /   __/ \___  >___|  /_______  (____  / ____||____/__\___  /|___|  /__|
            \/|__|        \/     \/        \/     \/\/            /_____/      \/


Hit '<tab>' for a list of available commands
and '[cmd] --help' for help on a specific command.
Hit '<ctrl-d>' or type 'system:shutdown' or 'logout' to shutdown OpenDaylight.

opendaylight-user@root>
opendaylight-user@root>feature:list -i
Name       | Version | Installed | Repository     | Description
------------------------------------------------------------------------------------------------------
standard   | 3.0.3   | x         | standard-3.0.3 | Karaf standard feature
config     | 3.0.3   | x         | standard-3.0.3 | Provide OSGi ConfigAdmin support
region     | 3.0.3   | x         | standard-3.0.3 | Provide Region Support
package    | 3.0.3   | x         | standard-3.0.3 | Package commands and mbeans
kar        | 3.0.3   | x         | standard-3.0.3 | Provide KAR (KARaf archive) support
ssh        | 3.0.3   | x         | standard-3.0.3 | Provide a SSHd server on Karaf
management | 3.0.3   | x         | standard-3.0.3 | Provide a JMX MBeanServer and a set of MBeans in K
opendaylight-user@root>

```
実はこれだけではDLUXはおろか、コントローラとして機能しないとのこと。。。  
featureを追加します。コマンドはfeature:installです。打ってもしばらく応答がありませんが気長に待ちます。  

```
opendaylight-user@root>feature:install odl-dlux-core odl-restconf odl-mdsal-apidocs odl-l2switch-switch
opendaylight-user@root>

```
特に成功とも失敗とも出ませんが、一応feature:list -iで確認すると。。。  

```
opendaylight-user@root>feature:list -i
Name                                 | Version           | Installed | Repository                               | Description
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
odl-openflowjava-protocol            | 0.6.2-Lithium-SR2 | x         | odl-openflowjava-0.6.2-Lithium-SR2       | OpenDaylight :: Openflow Java :: Protocol
odl-netconf-api                      | 0.3.2-Lithium-SR2 | x         | odl-netconf-0.3.2-Lithium-SR2            | OpenDaylight :: Netconf :: API
odl-netconf-mapping-api              | 0.3.2-Lithium-SR2 | x         | odl-netconf-0.3.2-Lithium-SR2            | OpenDaylight :: Netconf :: Mapping API
odl-netconf-util                     | 0.3.2-Lithium-SR2 | x         | odl-netconf-0.3.2-Lithium-SR2            |
odl-netconf-impl                     | 0.3.2-Lithium-SR2 | x         | odl-netconf-0.3.2-Lithium-SR2            | OpenDaylight :: Netconf :: Impl
odl-config-netconf-connector         | 0.3.2-Lithium-SR2 | x         | odl-netconf-0.3.2-Lithium-SR2            | OpenDaylight :: Netconf :: Connector
odl-netconf-netty-util               | 0.3.2-Lithium-SR2 | x         | odl-netconf-0.3.2-Lithium-SR2            | OpenDaylight :: Netconf :: Netty Util
odl-netconf-monitoring               | 0.3.2-Lithium-SR2 | x         | odl-netconf-0.3.2-Lithium-SR2            | OpenDaylight :: Netconf :: Monitoring
odl-netconf-notifications-api        | 0.3.2-Lithium-SR2 | x         | odl-netconf-0.3.2-Lithium-SR2            | OpenDaylight :: Netconf :: Notification :: Api
odl-netconf-notifications-impl       | 0.3.2-Lithium-SR2 | x         | odl-netconf-0.3.2-Lithium-SR2            | OpenDaylight :: Netconf :: Monitoring :: Impl
standard                             | 3.0.3             | x         | standard-3.0.3                           | Karaf standard feature
config                               | 3.0.3             | x         | standard-3.0.3                           | Provide OSGi ConfigAdmin support
region                               | 3.0.3             | x         | standard-3.0.3                           | Provide Region Support
package                              | 3.0.3             | x         | standard-3.0.3                           | Package commands and mbeans
http                                 | 3.0.3             | x         | standard-3.0.3                           | Implementation of the OSGI HTTP Service
war                                  | 3.0.3             | x         | standard-3.0.3                           | Turn Karaf as a full WebContainer
kar                                  | 3.0.3             | x         | standard-3.0.3                           | Provide KAR (KARaf archive) support
ssh                                  | 3.0.3             | x         | standard-3.0.3                           | Provide a SSHd server on Karaf
management                           | 3.0.3             | x         | standard-3.0.3                           | Provide a JMX MBeanServer and a set of MBeans in K
odl-openflowplugin-southbound        | 0.1.2-Lithium-SR2 | x         | openflowplugin-0.1.2-Lithium-SR2         | OpenDaylight :: Openflow Plugin :: SouthBound
odl-openflowplugin-flow-services     | 0.1.2-Lithium-SR2 | x         | openflowplugin-0.1.2-Lithium-SR2         | OpenDaylight :: Openflow Plugin :: Flow Services
odl-openflowplugin-nsf-services      | 0.1.2-Lithium-SR2 | x         | openflowplugin-0.1.2-Lithium-SR2         | OpenDaylight :: OpenflowPlugin :: NSF :: Services
odl-openflowplugin-nsf-model         | 0.1.2-Lithium-SR2 | x         | openflowplugin-0.1.2-Lithium-SR2         | OpenDaylight :: OpenflowPlugin :: NSF :: Model
odl-openflowplugin-app-config-pusher | 0.1.2-Lithium-SR2 | x         | openflowplugin-0.1.2-Lithium-SR2         | OpenDaylight :: Openflow Plugin :: app - default c
odl-openflowplugin-app-lldp-speaker  | 0.1.2-Lithium-SR2 | x         | openflowplugin-0.1.2-Lithium-SR2         | OpenDaylight :: Openflow Plugin :: app lldp-speake
odl-config-persister                 | 0.3.2-Lithium-SR2 | x         | odl-config-persister-0.3.2-Lithium-SR2   | OpenDaylight :: Config Persister
odl-config-startup                   | 0.3.2-Lithium-SR2 | x         | odl-config-persister-0.3.2-Lithium-SR2   | OpenDaylight :: Config Persister:: Config Startup
odl-restconf                         | 1.2.2-Lithium-SR2 | x         | odl-controller-1.2.2-Lithium-SR2         | OpenDaylight :: Restconf
odl-restconf-noauth                  | 1.2.2-Lithium-SR2 | x         | odl-controller-1.2.2-Lithium-SR2         | OpenDaylight :: Restconf
odl-mdsal-apidocs                    | 1.2.2-Lithium-SR2 | x         | odl-controller-1.2.2-Lithium-SR2         | OpenDaylight :: MDSAL :: APIDOCS
odl-dlux-core                        | 0.2.2-Lithium-SR2 | x         | odl-dlux-0.2.2-Lithium-SR2               | Opendaylight dlux minimal feature
odl-protocol-framework               | 0.6.2-Lithium-SR2 | x         | odl-protocol-framework-0.6.2-Lithium-SR2 | OpenDaylight :: Protocol Framework
odl-aaa-authn                        | 0.2.2-Lithium-SR2 | x         | odl-aaa-0.2.2-Lithium-SR2                | OpenDaylight :: AAA :: Authentication :: MD-SAL
odl-config-api                       | 0.3.2-Lithium-SR2 | x         | odl-config-0.3.2-Lithium-SR2             | OpenDaylight :: Config :: API
odl-config-netty-config-api          | 0.3.2-Lithium-SR2 | x         | odl-config-0.3.2-Lithium-SR2             | OpenDaylight :: Config :: Netty Config API
odl-config-core                      | 0.3.2-Lithium-SR2 | x         | odl-config-0.3.2-Lithium-SR2             | OpenDaylight :: Config :: Core
odl-config-manager                   | 0.3.2-Lithium-SR2 | x         | odl-config-0.3.2-Lithium-SR2             | OpenDaylight :: Config :: Manager
odl-yangtools-models                 | 0.7.2-Lithium-SR2 | x         | odl-yangtools-0.7.2-Lithium-SR2          | OpenDaylight :: Yangtools :: Models
odl-yangtools-data-binding           | 0.7.2-Lithium-SR2 | x         | odl-yangtools-0.7.2-Lithium-SR2          | OpenDaylight :: Yangtools :: Data Binding
odl-yangtools-binding                | 0.7.2-Lithium-SR2 | x         | odl-yangtools-0.7.2-Lithium-SR2          | OpenDaylight :: Yangtools :: Binding
odl-yangtools-common                 | 0.7.2-Lithium-SR2 | x         | odl-yangtools-0.7.2-Lithium-SR2          | OpenDaylight :: Yangtools :: Common
odl-yangtools-binding-generator      | 0.7.2-Lithium-SR2 | x         | odl-yangtools-0.7.2-Lithium-SR2          | OpenDaylight :: Yangtools :: Binding Generator
pax-jetty                            | 8.1.15.v20140411  | x         | org.ops4j.pax.web-3.1.4                  | Provide Jetty engine support
pax-http                             | 3.1.4             | x         | org.ops4j.pax.web-3.1.4                  | Implementation of the OSGI HTTP Service
pax-http-whiteboard                  | 3.1.4             | x         | org.ops4j.pax.web-3.1.4                  | Provide HTTP Whiteboard pattern support
pax-war                              | 3.1.4             | x         | org.ops4j.pax.web-3.1.4                  | Provide support of a full WebContainer
odl-config-netty                     | 0.3.2-Lithium-SR2 | x         | odl-config-persister-0.3.2-Lithium-SR2   | OpenDaylight :: Config-Netty
odl-l2switch-switch                  | 0.2.2-Lithium-SR2 | x         | l2switch-0.2.2-Lithium-SR2               | OpenDaylight :: L2Switch :: Switch
odl-l2switch-hosttracker             | 0.2.2-Lithium-SR2 | x         | l2switch-0.2.2-Lithium-SR2               | OpenDaylight :: L2Switch :: HostTracker
odl-l2switch-addresstracker          | 0.2.2-Lithium-SR2 | x         | l2switch-0.2.2-Lithium-SR2               | OpenDaylight :: L2Switch :: AddressTracker
odl-l2switch-arphandler              | 0.2.2-Lithium-SR2 | x         | l2switch-0.2.2-Lithium-SR2               | OpenDaylight :: L2Switch :: ArpHandler
odl-l2switch-loopremover             | 0.2.2-Lithium-SR2 | x         | l2switch-0.2.2-Lithium-SR2               | OpenDaylight :: L2Switch :: LoopRemover
odl-l2switch-packethandler           | 0.2.2-Lithium-SR2 | x         | l2switch-0.2.2-Lithium-SR2               | OpenDaylight :: L2Switch :: PacketHandler
odl-akka-scala                       | 2.10              | x         | odl-controller-1.5.2-Lithium-SR2         | Scala Runtime for OpenDaylight
odl-akka-system                      | 2.3.10            | x         | odl-controller-1.5.2-Lithium-SR2         | Akka Actor Framework System Bundles
odl-akka-clustering                  | 2.3.10            | x         | odl-controller-1.5.2-Lithium-SR2         | Akka Clustering
odl-akka-leveldb                     | 0.7               | x         | odl-controller-1.5.2-Lithium-SR2         | LevelDB
odl-akka-persistence                 | 2.3.10            | x         | odl-controller-1.5.2-Lithium-SR2         | Akka Persistence
odl-mdsal-common                     | 1.2.2-Lithium-SR2 | x         | odl-mdsal-1.2.2-Lithium-SR2              | OpenDaylight :: Config :: All
odl-mdsal-broker-local               | 1.2.2-Lithium-SR2 | x         | odl-mdsal-1.2.2-Lithium-SR2              | OpenDaylight :: MDSAL :: Broker
odl-mdsal-clustering-commons         | 1.2.2-Lithium-SR2 | x         | odl-mdsal-1.2.2-Lithium-SR2              |
odl-mdsal-distributed-datastore      | 1.2.2-Lithium-SR2 | x         | odl-mdsal-1.2.2-Lithium-SR2              |
odl-mdsal-remoterpc-connector        | 1.2.2-Lithium-SR2 | x         | odl-mdsal-1.2.2-Lithium-SR2              |
odl-mdsal-broker                     | 1.2.2-Lithium-SR2 | x         | odl-mdsal-1.2.2-Lithium-SR2              |
opendaylight-user@root>

```

がっつり追加されました。  
では今度こそ、、、。


![fig9](https://github.com/niruneru/documents/blob/media/01_09.gif)


404に変わりました。一歩前進です。  
Lithiumからトップページへのパスが変わったようで、～/dlux/index.htmlではなく、～/index.htmlで行けました。  


![fig10](https://github.com/niruneru/documents/blob/media/01_10.gif)


やっと表示されました。ここでadmin/adminでログインします。(ただし、認証モジュールが立ち上がるのが遅いのか、少し時間が経ってからでないとログインできないです）


![fig11](https://github.com/niruneru/documents/blob/media/01_11.gif)


まだスイッチも何も作成していないため画面は真っ白です。  
  
長くなったので第１回目はここまでとします。  
仮想スイッチとコントローラのソフトウェアについて、インストールおよび起動確認までは完了したので、次回はいよいよ仮想スイッチを作成します。  
