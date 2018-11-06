# centOS 7 - Network Configuration

Table of Contents

- [1. Identifying the network interfaces](#1-identifying-the-network-interfaces)
- [2. Configuring the network interface](#2-configuring-the-network-interface)
  - [2.1 Configuring the network interface as STATIC](#21-configuring-the-network-interface-as-static)
  - [2.2 Configuring the network interface as DHCP](#22-configuring-the-network-interface-as-dhcp)
  - [2.3 Network file variables description](#23-network-file-variables-description)
- [3. Gateway and Networking](#3-gateway-and-networking)
- [4. Restarting networking service](#4-restarting-networking-service)

## Network interfaces

### 1. Identifying the network interfaces

```bash
localhost:~$ ip addr list
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 94:c6:91:1c:94:a2 brd ff:ff:ff:ff:ff:ff
    inet 10.219.128.54/8 brd 10.255.255.255 scope global eno1
       valid_lft forever preferred_lft forever
    inet6 fe80::96c6:91ff:fe1c:94a2/64 scope link 
       valid_lft forever preferred_lft forever
localhost:~$ 
```

In the above case your network interface will be `eno1`

### 2. Configuring the network interface

Here you have to locate the configuration file of the interface that you are going to modify

```bash
localhost:~$ ls -l /etc/sysconfig/network-scripts/ifcfg-*
-rw-r--r--  1 root root 299 May  4 10:46 /etc/sysconfig/network-scripts/ifcfg-eno1
-rw-r--r--. 1 root root 114 May  4 10:24 /etc/sysconfig/network-scripts/ifcfg-lo
```

Your file must be `ifcfg-eno1` and it should look like as the following example:

```bash
localhost:~$ cat /etc/sysconfig/network-scripts/ifcfg-eno1 
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=eno1
UUID=755b5bb4-7012-4795-af0a-1c7dbfbfb927
DEVICE=eno1
ONBOOT=no
```

#### 2.1 Configuring the network interface as STATIC

Please edit the network configuration file as *root* and add the missing variables (if any)

```text
BOOTPROTO=static
IPV6INIT=no
IPV6_AUTOCONF=no
ONBOOT=yes
IPADDR=<your_ip>
```


#### 2.2 Configuring the network interface as DHCP

Please edit the network configuration file as *root* and add the missing variables (if any)

```text
BOOTPROTO=dhcp
IPV6INIT=no
IPV6_AUTOCONF=no
ONBOOT=yes
```

#### 2.3 Network file variables description 

| Variable Name | Description                                                                                                                                                                                                                                                                                                                                                                                                    |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ONBOOT        | If the network interface you are configuring should be automatically raised when the  network service starts, then you must configure this option as "yes" otherwise the  server will boot and the interface will remain disabled until you manually activate it. Remember that CentOS 7 always configures this option as "no", so you will not have a  default network connection in the network interfaces.  |
| IPV6INIT      | If you want to activate the IP version 6 protocol and it is autoconfigured, in the  example we select that we will not use it and that it will not be configured.                                                                                                                                                                                                                                              |
| IPV6_AUTOCONF | If you want to activate the IP version 6 protocol and it is autoconfigured, in the                                                                                                                                                                                                                                                                                                                             |
| BOOTPROTO     | Determines the type of configuration that the interface has, it can be none (none),  static (static) or dhcp (dynamic ip allocation by dhcp) usually on a  server it should  always be configured as static.                                                                                                                                                                                                   |


### 3. Gateway and Networking

Finally you must add the following parameters to `/etc/sysconfig/network` file. 

```bash
localhost:~# vi /etc/sysconfig/network
NETWORKING=yes
GATEWAY=10.219.<VLAN>.1
HOSTNAME=<your_hotname>
```

### 4. Restarting networking service

Once you have setup the network file you must restart the networking service in order to take
effect the applied changes on it.

```bash
localhost:~# systemctl restart network.service
```

In this moment you should have active the network interface with the ip you have setup previously.
In order to verify the network status please execute the following ip command like this:

```bash
localhost:~$ ip add show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 94:c6:91:1c:94:a2 brd ff:ff:ff:ff:ff:ff
    inet 10.219.128.54/8 brd 10.255.255.255 scope global eno1
       valid_lft forever preferred_lft forever
    inet6 fe80::96c6:91ff:fe1c:94a2/64 scope link 
       valid_lft forever preferred_lft forever
```

:notebook: In `ip add show` command output you should note that unlike the command at the beginning of this post,
the network interface is now assigned an IP and shows the status as "state UP" which indicates that it is working.  

There is something important that I want to mention about the `ONBOOT` parameter because it does not
only apply when the server starts up, but also when the network service is started.<br>
Therefore, if the parameter were to be configured as `ONBOOT=no` when executing the command
`systemctl restart network.service` the interface will not assign an IP and it will appear that this
command does not work.<br>
If you want to raise the interface with `ONBOOT=no` you must do it manually with the command `ifup eno1`
or you must configure the `ONBOOT` parameter to yes and execute `systemctl restart network.service` again.
