# NTP Server

### 🖧 Topology
![Topology](Topology/Topology_interVLANRouting_NAT_Linux.png)

## NTP Server on Linux

**chrony пакетін орнату**
```shell
Debian/Ubuntu
$ sudo apt update 
$ sudo apt install chrony

RHEL/Rocky
$ sudo dnf install chrony
```

**Қазақстандық NTP серверлерге талдау жасау**
```shell
$ dig +short 2.kz.pool.ntp.org
185.116.194.200
82.200.233.67
38.180.36.242
$ ping -c2 185.116.194.200
64 bytes from 185.116.194.200: icmp_seq=1 ttl=54 time1.98 ms
64 bytes from 185.116.194.200: icmp_seq=1 ttl=54 time2.24 ms

$ dig +short ntp.nic.kz
80.241.0.72
$ ping -c2 80.241.0.72
64 bytes from 80.241.0.72: icmp_seq=1 ttl=53 time15.6 ms
64 bytes from 80.241.0.72: icmp_seq=1 ttl=53 time16.2 ms
```
> *NTP Pool Time Servers* [Link](https://www.ntppool.org/zone/kz)  

**NTP серверді конфигурациялау**
```shell
Уақыт белдеуін (Time Zone) өзгерту
$ sudo timedatectl set-timezone Asia/Almaty
```
> *Time Zones in Kazakhstan* [Link](https://www.timeanddate.com/time/zone/kazakhstan)

```shell
$ sudo nano /etc/chrony/chrony.conf

8-қатарды (Line 8) "#" comment-ге алып, төменгі қатарға Қазақстанға ең жақын NTP сервердің DNS атауын енгіземіз!
#pool 2.debian.pool.ntp.org iburst

# Kazakhstan NTP pool
server ntp.nic.kz iburst
pool 2.kz.pool.ntp.org iburst
pool 1.kz.pool.ntp.org iburst

# Global NTP pool
pool time.google.com iburst
pool time.cloudflare.com iburst

# Log settings
logdir /var/log/chrony
log measurements statistics tracking

# RTC синхрондау
rtcsync

# Уақыт дәлдігін тез реттеу
makestep 1.0 3
```

```shell
Daemon-ды қайта жүктеу
$ sudo systemctl restart chrony
```

**Нәтижені тексеру**
```shell
$ sudo chronyc sources -v
$ sudo chronyc tracking

$ sudo apt install ntpdate
$ sudo ntpdate -q 80.241.0.72
```

**Жергілікті желіге рұқсат беру (Allow)**
```shell
$ sudo nano /etc/chrony/chrony.conf
# Allow access for Local Networks
allow 172.16.11.0/24
allow 172.16.12.0/24

$ sudo systemctl restart chrony
```

**Firewall конфигурациялау**
```shell
nftables конфигурациясы

$ sudo nft add rule inet filter input udp dport 123 ip saddr 172.16.11.0/24 accept
$ sudo nft add rule inet filter input udp dport 123 ip saddr 172.16.12.0/24 accept

$ sudo nft list ruleset | sudo tee /etc/nftables.conf
$ sudo systemctl restart nftables

$ sudo nft list ruleset
```

```shell
iptables конфигурациясы

$ sudo iptables -A INPUT -p udp --dport 123 -s 172.16.11.0/24 -j ACCEPT
$ sudo iptables -A INPUT -p udp --dport 123 -s 172.16.12.0/24 -j ACCEPT
$ sudo iptables -A INPUT -p udp --dport 123 -s 127.0.0.1 -j ACCEPT

$ sudo netfilter-persistent save
$ sudo netfilter-persistent reload

$ sudo iptables -vnL
```

```shell
Firewalld конфигурациясы (RHEL/Rocky)

$ sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="172.16.11.0/24" port protocol="udp" port="123" accept'
$ sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="172.16.12.0/24" port protocol="udp" port="123" accept'

$ sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" port protocol="udp" port="123" drop'

$ sudo firewall-cmd --reload

$ sudo firewall-cmd --list-all --zone=public
```

```shell
UFW конфигурациясы (Debian/Ubuntu)

$ sudo ufw enable

$ sudo ufw allow from 172.16.11.0/24 to any port 123 proto udp
$ sudo ufw allow from 172.16.12.0/24 to any port 123 proto udp

$ sudo ufw deny proto udp from any to any port 123

$ sudo ufw status verbose
```
> UFW - Uncomplicated Firewall  

## NTP Client on Linux

```shell
Debian/Ubuntu
$ sudo apt install chrony

RHEL/Rocky
$ sudo dnf install chrony

$ sudo systemctl status chrony
```

```shell
$ sudo nano /etc/chrony/chrony.conf
server 172.16.11.1 iburst

$ sudo systemctl restart chrony
```

```shell
Нәтижені тексеру
$ sudo chronyc sources -v
$ sudo chronyc tracking
```

```shell
Уақытты қолмен синхрондау (тексеру үшін)
$ sudo chronyc makestep
200 OK
```

## NTP Server on Cisco IOS (Router, Switch)

```shell
configure terminal

clock timezone KZ +5                     // Уақыт белдеуін орнату
clock set 08:00:00 7 AUG 2025            // Жергілікті уақытты қолмен орнату

ntp master 3                             // NTP сервер болу, stratum 3
ntp source Loopback50                    // NTP клиенттің сұраныстарына жауап беретін интерфейсті көрсету
```

```shell
Нәтижені тексеру
show ntp status
show ntp associations
```

```shell
access-list 10 permit 172.16.11.0 0.0.0.255     // NTP клиенттерге рұқсат беру
ntp access-group peer 10
```

## NTP Client on Cisco IOS (Router, Switch)

```shell
configure terminal
ntp server 172.16.11.1 prefer        // Жергілікті желідегі (LAN) NTP сервердің IP адресі
clock timezone KZ +5                 // Уақыт белдеуін орнату
```

```shell
ntp source Loopback50                // NTP серверге сұраныс жіберетін интерфейсті көрсету
немесе
ntp source GigabitEthernet1/0/1
```

```shell
Нәтижені тексеру
show ntp associations
show ntp status
```

```shell
access-list 10 permit 172.16.11.1
ntp access-group peer 10
```

## NTP Server on Huawei VRP (Router, Switch)

```shell
system-view

clock timezone KZ add 5
clock datetime 08:00:00 2025-08-07          // Жергілікті уақытты қолмен орнату

ntp-service enable
ntp-service refclock-master 3              // NTP сервер болу, stratum 3
```

```shell
Нәтижені тексеру
display ntp-service status
```

```shell
acl number 2000
 rule permit ip source 172.16.11.0 0.0.0.255
ntp-service acl 2000
```

## NTP Client on Huawei VRP (Router, Switch)

```shell
system-view

clock timezone KZ add 5

ntp-service enable
ntp-service unicast-server 172.16.11.1
```

```shell
Нәтижені тексеру
display ntp-service status
display ntp-service sessions
```

```shell
acl number 2000
 rule permit ip source 172.16.11.1 0.0.0.0
ntp-service acl 2000
```
