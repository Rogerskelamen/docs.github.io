# Centos 7配置网络

> 我们通常以为配置网络就是能ping百度了，**大错特错**，有时候我们的虚拟机是能自动ping上百度的，==但实际上网络并没有配置好==（可能是我们的虚拟机直接用了宿主机的网络ping的百度）

- ## 先进入/etc/sysconfig/network-scripts/ifcfg-ens33

  ```bash
  vi /etc/sysconfig/network-scripts/ifcfg-ens33
  ```

  我们会看到这样的配置

  ```
  TYPE="Ethernet"
  PROXY_METHOD="none"
  BROWSER_ONLY="no"
  BOOTPROTO=dhcp
  DEFROUTE="yes"
  IPV4_FAILURE_FATAL="no"
  IPV6INIT="yes"
  IPV6_AUTOCONF="yes"
  IPV6_DEFROUTE="yes"
  IPV6_FAILURE_FATAL="no"
  IPV6_ADDR_GEN_MODE="stable-privacy"
  NAME="ens33"
  UUID="e18281bf-cfc2-45b0-8413-6ebd72048ff7"
  DEVICE="ens33"
  ONBOOT="yes"
  ```

  我们要更改的东西就很多了，如下

  ```
  BOOTPROTO=static 	// 直接改成static
  IPADDR=192.168.152.101	// IP的最后位不要用什么0，1，2之类的
  GATEWAY=192.168.152.2
  NETMASK=255.255.255.0
  DNS1=8.8.4.4
  DNS2=8.8.8.8
  HDADDR=00:0c:29:68:a8:f1	// 这个一般不一样，得查
  ```

  *除了注释的那几个，基本上一样，或者根据英文就能配出来了*

  

- ## 那我们就去查物理地址（HDADDR）

  使用命令：

  ```bash
  ip addr
  ```

  显示如下两个网卡信息*（一般两个）*：

  ```
  1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
      link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
      inet 127.0.0.1/8 scope host lo
         valid_lft forever preferred_lft forever
      inet6 ::1/128 scope host 
         valid_lft forever preferred_lft forever
  2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
      link/ether 00:0c:29:68:a8:f1 brd ff:ff:ff:ff:ff:ff
      inet 192.168.152.101/24 brd 192.168.152.255 scope global noprefixroute ens33
         valid_lft forever preferred_lft forever
      inet6 fe80::484e:a23e:82ac:d890/64 scope link noprefixroute 
         valid_lft forever preferred_lft forever
  ```

  **第二个是我们的实际上用的网卡**，即ens33，然后我们看到`00:0c:29:68:a8:f1`这一串，就是==我的==物理地址

  咱们记下来之后，把HDADDR配置了：

  ```
  HDADDR=00:0c:29:68:a8:f1
  ```

- ## 重启网络服务以生效

  > 很多时候我们都忘了重启这个服务了
  >
  > 指令如下：

  ```bash
  service network restart
  ```

  **等待一下后会显示<font color=green>[OK]</font>**

  

- ## 最后ping一下，以免最后结果炸了

  ```bash
  ping www.baidu.com
  ```

  