# AAC�l�b�g���[�N���ȉ� SDN�׋����P��ځiOpenFlow�̏Љ�ƃn���Y�I���j�V�X�e���Z�p���F����

## OpenFlow�Ƃ́H



## �����Ă݂�
�Ƃ���OpenFlow��p���āA�~�j�}����SDN���쐬���ău���O�I�ɂ܂Ƃ߂Ă݂܂����̂ŁA��������Ɏ��Ԃ̋�������n���Y�I���������Ǝv���܂��B  
�i�Г��׋���Ȃ��Ƃ����胆���������Ői�݂܂������e�͂��������B�j

�܂��A����̃S�[���́w�ȉ��̍\����OpenFlow��p���č\�z���ANode1/Node2�Ԃł�ping�a�ʂ�ʂ����Ɓx�ł��B  

fig

�g�p����OS�EOSS�͈ȉ��̒ʂ�ł��B  
- CentOS 7.1
- Open vSwitch 2.4.0 (���z�X�C�b�`)
- OpenDayLight Lithium SR-2(OpenFlow�R���g���[��)

###1. Open vSwitch�̃C���X�g�[��
yum search���Ă������炸�A���ׂ��Ƃ���A�A�A
- �@�\�[�X���_�E�����[�h����make install����B
- �ARDO�̃��|�W�g������C���X�g�[������B
�̂ǂ��炩��I������K�v�����邻���ł��B	
���͇A�̕��@�Ŏ��{���܂����BRDO��RedHat��Openstack���[�U���x������R�~���j�e�B�������ł��B  
�ŁA����RDO�����T�C�g�ɍs���ƁA�g�b�v�y�[�W��Quickstart�ւ̗U�����������̂ŁA�������|�`���Ƃ����  

fig

�Ƃ������ƂŁAStep1�܂Ŏ��{���ă��|�W�g����ǉ����܂��B  
```
[remote ~]$
[remote ~]$ sudo yum -y update
�ǂݍ��񂾃v���O�C��:fastestmirror
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
�ˑ����̉��������Ă��܂�
--> �g�����U�N�V�����̊m�F�����s���Ă��܂��B
---> �p�b�P�[�W NetworkManager.x86_64 1:1.0.0-14.git20150121.b4ea599c.el7 �� �X�V
---> �p�b�P�[�W NetworkManager.x86_64 1:1.0.0-16.git20150121.b4ea599c.el7_1 �� �A�b�v�f�[�g
---> �p�b�P�[�W NetworkManager-libnm.x86_64 1:1.0.0-14.git20150121.b4ea599c.el7 �� �X�V
---> �p�b�P�[�W NetworkManager-libnm.x86_64 1:1.0.0-16.git20150121.b4ea599c.el7_1 �� �A�b�v�f�[�g
---> �p�b�P�[�W NetworkManager-team.x86_64 1:1.0.0-14.git20150121.b4ea599c.el7 �� �X�V
---> �p�b�P�[�W NetworkManager-team.x86_64 1:1.0.0-16.git20150121.b4ea599c.el7_1 �� �A�b�v�f�[�g
---> �p�b�P�[�W NetworkManager-tui.x86_64 1:1.0.0-14.git20150121.b4ea599c.el7 �� �X�V
---> �p�b�P�[�W NetworkManager-tui.x86_64 1:1.0.0-16.git20150121.b4ea599c.el7_1 �� �A�b�v�f�[�g
---> �p�b�P�[�W bind-libs-lite.x86_64 32:9.9.4-18.el7 �� �X�V
---> �p�b�P�[�W bind-libs-lite.x86_64 32:9.9.4-18.el7_1.5 �� �A�b�v�f�[�g
---> �p�b�P�[�W bind-license.noarch 32:9.9.4-18.el7 �� �X�V
---> �p�b�P�[�W bind-license.noarch 32:9.9.4-18.el7_1.5 �� �A�b�v�f�[�g
---> �p�b�P�[�W binutils.x86_64 0:2.23.52.0.1-30.el7 �� �X�V
---> �p�b�P�[�W binutils.x86_64 0:2.23.52.0.1-30.el7_1.2 �� �A�b�v�f�[�g
---> �p�b�P�[�W ca-certificates.noarch 0:2014.1.98-72.el7 �� �X�V
---> �p�b�P�[�W ca-certificates.noarch 0:2015.2.4-70.0.el7_1 �� �A�b�v�f�[�g
---> �p�b�P�[�W centos-logos.noarch 0:70.0.6-1.el7.centos �� �X�V
---> �p�b�P�[�W centos-logos.noarch 0:70.0.6-3.el7.centos �� �A�b�v�f�[�g

��

  openssh-clients.x86_64 0:6.6.1p1-12.el7_1
  openssl.x86_64 1:1.0.1e-42.el7.9
  pam.x86_64 0:1.1.8-12.el7_1.1
  python-libs.x86_64 0:2.7.5-18.el7_1.1
  selinux-policy.noarch 0:3.13.1-23.el7_1.21
  sqlite.x86_64 0:3.7.17-6.el7_1.1
  systemd-libs.x86_64 0:208-20.el7_1.6
  trousers.x86_64 0:0.3.11.2-4.el7_1
  util-linux.x86_64 0:2.23.2-22.el7_1.1

�������܂���!
[remote ~]$
[remote ~]$
[remote ~]$
[remote ~]$ sudo yum install -y https://rdoproject.org/repos/rdo-release.rpm
�ǂݍ��񂾃v���O�C��:fastestmirror
rdo-release.rpm                                                                                                                                                          | 5.1 kB  00:00:00
/var/tmp/yum-root-LhteEU/rdo-release.rpm �𒲂ׂĂ��܂�: rdo-release-liberty-2.noarch
/var/tmp/yum-root-LhteEU/rdo-release.rpm ���C���X�g�[���ς݂Ƃ��Đݒ肵�Ă��܂�
�ˑ����̉��������Ă��܂�
--> �g�����U�N�V�����̊m�F�����s���Ă��܂��B
---> �p�b�P�[�W rdo-release.noarch 0:liberty-2 �� �C���X�g�[��
--> �ˑ����������I�����܂����B

�ˑ������������܂���

================================================================================================================================================================================================
 Package                                        �A�[�L�e�N�`���[                          �o�[�W����                                      ���|�W�g���[                                     �e��
================================================================================================================================================================================================
�C���X�g�[����:
 rdo-release                                    noarch                                    liberty-2                                       /rdo-release                                    1.4 k

�g�����U�N�V�����̗v��
================================================================================================================================================================================================
�C���X�g�[��  1 �p�b�P�[�W

���v�e��: 1.4 k
�C���X�g�[���e��: 1.4 k
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  �C���X�g�[����          : rdo-release-liberty-2.noarch                                                                                                                                    1/1
  ���ؒ�                  : rdo-release-liberty-2.noarch                                                                                                                                    1/1

�C���X�g�[��:
  rdo-release.noarch 0:liberty-2

�������܂���!
[remote ~]$
```

������Open vSwitch���C���X�g�[�����܂��B
```
[remote ~]$
[remote ~]$
[remote ~]$ sudo yum -y install openvswitch
�ǂݍ��񂾃v���O�C��:fastestmirror
openstack-liberty                                                                                                                                                        | 2.9 kB  00:00:00
openstack-liberty/x86_64/primary_db                                                                                                                                      | 354 kB  00:00:03
Loading mirror speeds from cached hostfile
 * base: www.ftp.ne.jp
 * extras: www.ftp.ne.jp
 * updates: www.ftp.ne.jp
�ˑ����̉��������Ă��܂�
--> �g�����U�N�V�����̊m�F�����s���Ă��܂��B
---> �p�b�P�[�W openvswitch.x86_64 0:2.4.0-1.el7 �� �C���X�g�[��
--> �ˑ����������I�����܂����B

�ˑ������������܂���

================================================================================================================================================================================================
 Package                                       �A�[�L�e�N�`���[                         �o�[�W����                                    ���|�W�g���[                                         �e��
================================================================================================================================================================================================
�C���X�g�[����:
 openvswitch                                   x86_64                                   2.4.0-1.el7                                   openstack-liberty                                   2.2 M

�g�����U�N�V�����̗v��
================================================================================================================================================================================================
�C���X�g�[��  1 �p�b�P�[�W

���_�E�����[�h�e��: 2.2 M
�C���X�g�[���e��: 8.9 M
Downloading packages:
�x��: /var/cache/yum/x86_64/7/openstack-liberty/packages/openvswitch-2.4.0-1.el7.x86_64.rpm: �w�b�_�[ V4 RSA/SHA1 Signature�A�� ID 764429e6: NOKEY=========   ] 293 kB/s | 2.1 MB  00:00:00 ETA
openvswitch-2.4.0-1.el7.x86_64.rpm �̌��J�����C���X�g�[������Ă��܂���
openvswitch-2.4.0-1.el7.x86_64.rpm                                                                                                                                       | 2.2 MB  00:00:07
file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-Cloud ���献���擾���ł��B
Importing GPG key 0x764429E6:
 Userid     : "CentOS Cloud SIG (http://wiki.centos.org/SpecialInterestGroup/Cloud) <security@centos.org>"
 Fingerprint: 736a f511 6d9c 40e2 af6b 074b f9b9 fee7 7644 29e6
 Package    : rdo-release-liberty-2.noarch (@/rdo-release)
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-SIG-Cloud
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  �C���X�g�[����          : openvswitch-2.4.0-1.el7.x86_64                                                                                                                                  1/1
  ���ؒ�                  : openvswitch-2.4.0-1.el7.x86_64                                                                                                                                  1/1

�C���X�g�[��:
  openvswitch.x86_64 0:2.4.0-1.el7

�������܂���!
[remote ~]$
```

### 2. OpenDayLight�̃C���X�g�[��
�����āA�R���g���[���ƂȂ�OpenDayLight�̃C���X�g�[���ł��B
OpenDayLight�̃C���X�g�[����OpenDayLight�����s���郆�[�U�ōs���K�v�����邻���ł��B�C�����܂��傤�B
OpenDayLight��java�ō���Ă���̂ŁA���������߂ɂ܂�JDK���C���X�g�[�����܂��B

```
[remote ~]$
[remote ~]$
[remote ~]$ sudo yum -y install java-1.8.0-openjdk
�ǂݍ��񂾃v���O�C��:fastestmirror
Loading mirror speeds from cached hostfile
 * base: www.ftp.ne.jp
 * extras: www.ftp.ne.jp
 * updates: www.ftp.ne.jp
�ˑ����̉��������Ă��܂�
--> �g�����U�N�V�����̊m�F�����s���Ă��܂��B
---> �p�b�P�[�W java-1.8.0-openjdk.x86_64 1:1.8.0.65-2.b17.el7_1 �� �C���X�g�[��
--> �ˑ����̏��������Ă��܂�: java-1.8.0-openjdk-headless = 1:1.8.0.65-2.b17.el7_1 �̃p�b�P�[�W: 1:java-1.8.0-openjdk-1.8.0.65-2.b17.el7_1.x86_64
--> �ˑ����̏��������Ă��܂�: xorg-x11-fonts-Type1 �̃p�b�P�[�W: 1:java-1.8.0-openjdk-1.8.0.65-2.b17.el7_1.x86_64
--> �ˑ����̏��������Ă��܂�: libpng15.so.15(PNG15_0)(64bit) �̃p�b�P�[�W: 1:java-1.8.0-openjdk-1.8.0.65-2.b17.el7_1.x86_64
--> �ˑ����̏��������Ă��܂�: libjvm.so(SUNWprivate_1.1)(64bit) �̃p�b�P�[�W: 1:java-1.8.0-openjdk-1.8.0.65-2.b17.el7_1.x86_64

��

  ���ؒ�                  : libXext-1.3.2-2.1.el7.x86_64                                                                                                                                  26/27
  ���ؒ�                  : fontconfig-2.10.95-7.el7.x86_64                                                                                                                               27/27

�C���X�g�[��:
  java-1.8.0-openjdk.x86_64 1:1.8.0.65-2.b17.el7_1

�ˑ����֘A���C���X�g�[�����܂���:
  fontconfig.x86_64 0:2.10.95-7.el7             fontpackages-filesystem.noarch 0:1.44-8.el7    giflib.x86_64 0:4.1.6-9.el7       java-1.8.0-openjdk-headless.x86_64 1:1.8.0.65-2.b17.el7_1
  javapackages-tools.noarch 0:3.4.1-6.el7_0     libICE.x86_64 0:1.0.8-7.el7                    libSM.x86_64 0:1.2.1-7.el7        libX11.x86_64 0:1.6.0-2.1.el7
  libX11-common.noarch 0:1.6.0-2.1.el7          libXau.x86_64 0:1.0.8-2.1.el7                  libXext.x86_64 0:1.3.2-2.1.el7    libXfont.x86_64 0:1.4.7-3.el7_1
  libXi.x86_64 0:1.7.2-2.1.el7                  libXrender.x86_64 0:0.9.8-2.1.el7              libXtst.x86_64 0:1.2.2-2.1.el7    libfontenc.x86_64 0:1.1.1-5.el7
  libjpeg-turbo.x86_64 0:1.2.90-5.el7           libpng.x86_64 2:1.5.13-5.el7                   libxcb.x86_64 0:1.9-5.el7         libxslt.x86_64 0:1.1.28-5.el7
  python-javapackages.noarch 0:3.4.1-6.el7_0    python-lxml.x86_64 0:3.2.1-4.el7               ttmkfdir.x86_64 0:3.0.9-41.el7    tzdata-java.noarch 0:2015g-1.el7
  xorg-x11-font-utils.x86_64 1:7.5-18.1.el7     xorg-x11-fonts-Type1.noarch 0:7.5-9.el7

�������܂���!
[remote ~]$
```
  
OpenDayLight�́A���ϐ�JAVA_HOME�ɋL�ڂ���Ă���JVM���g���ċN�����邽�߁A���̐ݒ�����Ă����܂��B
```
[remote ~]$
[remote ~]$ export JAVA_HOME=/etc/alternatives/jre_1.8.0_openjdk
[remote ~]$ echo 'export JAVA_HOME=/etc/alternatives/jre_1.8.0_openjdk' >> .bashrc
```
  
�����āA���悢��OpenDayLight���C���X�g�[�����܂��B  
  
�����T�C�g�ɍs���܂��B


fig

���̕���DOWNLOAD NOW������̂ŁA������N���b�N�����


fig



�ꗗ���o�܂��B���ړ��Ă�Lithium-SR2��Pre-Built Tar File�̃_�E�����[�hURL���R�s�[���āAcurl��wget������Ŏ擾���܂��B

fig

```
[remote ~]$
[remote ~]$
[remote ~]$ wget https://nexus.opendaylight.org/content/repositories/opendaylight.release/org/opendaylight/integration/distribution-karaf/0.3.2-Lithium-SR2/distribution-karaf-0.3.2-Lithium-SR2.tar.gz
--2015-11-17 03:57:27--  https://nexus.opendaylight.org/content/repositories/opendaylight.release/org/opendaylight/integration/distribution-karaf/0.3.2-Lithium-SR2/distribution-karaf-0.3.2-Lithium-SR2.tar.gz
nexus.opendaylight.org (nexus.opendaylight.org) ��DNS�ɖ₢���킹�Ă��܂�... 23.253.119.7
nexus.opendaylight.org (nexus.opendaylight.org)|23.253.119.7|:443 �ɐڑ����Ă��܂�... �ڑ����܂����B
HTTP �ɂ��ڑ��v���𑗐M���܂����A������҂��Ă��܂�... 200 OK
����: 282951021 (270M) [application/x-gzip]
`distribution-karaf-0.3.2-Lithium-SR2.tar.gz' �ɕۑ���

100%[======================================================================================================================================================>] 282,951,021 1.22MB/s ���� 2m 9s

2015-11-17 03:59:37 (2.09 MB/s) - `distribution-karaf-0.3.2-Lithium-SR2.tar.gz' �֕ۑ����� [282951021/282951021]

[remote ~]$
```

�_�E�����[�h�����B�z�[���f�B���N�g���ł��ǂ��ł����������ł����A�𓀁E�W�J���܂��B
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

��

distribution-karaf-0.3.2-Lithium-SR2/bin/karaf.bat
distribution-karaf-0.3.2-Lithium-SR2/bin/setenv.bat
distribution-karaf-0.3.2-Lithium-SR2/bin/shell.bat
distribution-karaf-0.3.2-Lithium-SR2/bin/start.bat
distribution-karaf-0.3.2-Lithium-SR2/bin/status.bat
distribution-karaf-0.3.2-Lithium-SR2/bin/stop.bat
[remote ~]$
```

�ȏ�ŁAOpen vSwitch��OpenDayLight�̃C���X�g�[�����������܂����B  
���ɁA�N���m�F���s���܂��B

### 3. Open vSwitch�̋N���m�F
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
   Active: active (exited) since �� 2015-11-17 04:15:40 JST; 9s ago
  Process: 28846 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
 Main PID: 28846 (code=exited, status=0/SUCCESS)

[remote ~]$
```
�N�����܂����B�ȉ��̃R�}���h�Ńo�[�W�����̊m�F���ł��܂��B
```
[remote ~]$
[remote ~]$ ovs-vsctl -V
ovs-vsctl (Open vSwitch) 2.4.0
Compiled Oct  7 2015 18:01:06
DB Schema 7.12.1
[remote ~]$
```

### 4. OpenDayLight�̋N���m�F
�����āAOpenDayLight�̋N���m�F���s���܂��B�_�E�����[�h����tar���𓀁E�W�J�������[�U�œW�J�����f�B���N�g�����ɓ���A./bin/karaf�Ƒł��܂��B
�����łɒ������炵���f�B���N�g������ύX���Ă��܂��B
�Ȃ��Abin�̉��̃R�}���h�ł���
./start�F�o�b�N�O���E���h���s
./client�FKaraf Console�֐ڑ�����B�i�o�b�N�O�����h���s�������ƂȂǂɗ��p����j
./status�FOpenDaylight�̓���󋵂̊m�F�B

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

�N�������ł��B�I������Ƃ���logout�Ƒł��܂��B  

### 5. GUI�ɂ��Ǘ���񋟂���OpenDayLight��DLUX
DLUX���g�p����ƁAWeb�u���E�U��GUI��ʂ��ĉ��z�l�b�g���[�N�̊Ǘ����s�����Ƃ��ł��܂��B����ȉ�ʂł��B��(����͉摜�����ŏE��������)

fig

�܂��A�g�b�v�y�[�W�ɃA�N�Z�X���܂��B�u���E�U����  
`http://OpenDayLight���C���X�g�[�������z�X�g�̃A�h���X:8181/dlux/index.html`  
�փA�N�Z�X���܂��B  

fig

CentOS����firewall�̐ݒ���������܂��B
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

�ȗ�

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

eth0��public���K�p����Ă��܂��B
�Ƃ����킯�ŁApublic��8181��ǉ����܂��B�ꉞ�A���U�@�H��/etc/firewalld/services/�Ƀt�@�C����ǉ����܂��B

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

��<description>�^�O�̒��͖��C���ł��B  
  
�ŁAsudo firewall-cmd --reload���Ĕ��f�����܂��ƁA�A�A�B



fig



����ς�ʖڂł����B���������C���X�g�[�����������ł�DLUX���g�����߂̊�{�I�ȃv���O�C���������Ă��Ȃ��Ƃ̂��Ƃł��B
�Ƃ����킯�Ńv���O�C����ǉ����܂��B
�܂��A�������Ă���v���O�C�����m�F���܂��B
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
�m���ɂȂ񂩏��Ȃ��C�����܂��B���͂��ꂾ���ł�DLUX�͂��납�A�R���g���[���Ƃ��ċ@�\���Ȃ��Ƃ̂��ƁB�B�B  
feature��ǉ����܂��B�R�}���h��feature:install�ł��B�ł��Ă����΂炭����������܂��񂪋C���ɑ҂��܂��B  
```
opendaylight-user@root>feature:install odl-dlux-core odl-restconf odl-mdsal-apidocs odl-l2switch-switch
opendaylight-user@root>

```
���ɐ����Ƃ����s�Ƃ��o�܂��񂪁A�ꉞfeature:list -i�Ŋm�F����ƁB�B�B
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

������ǉ�����܂����B  
�ł͍��x�����A�A�A�B


fig


404�ɕς��܂����B  
Lithium����g�b�v�y�[�W�ւ̃p�X���ς�����悤�ŁA�`/dlux/index.html�ł͂Ȃ��A�`/index.html�ōs���܂����B  


fig


����ƕ\������܂����B������admin/admin�Ń��O�C�����܂��B(�������A�F�؃��W���[���������オ��̂��x���̂��A�������Ԃ��o���Ă���łȂ��ƃ��O�C���ł��Ȃ��ł��j


fig


�܂��X�C�b�`�������쐬���Ă��Ȃ����߉�ʂ͐^�����ł��B  
  
�����Ȃ����̂łP��ڂ͂����܂łƂ��܂��B  
���z�X�C�b�`�ƃR���g���[���̃\�t�g�E�F�A�ɂ��āA�C���X�g�[������ыN���m�F�܂ł͊��������̂ŁA����͂��悢�扼�z�X�C�b�`���쐬���܂��B  
