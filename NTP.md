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

**Нәтижені тексеру / Verification**
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
$ sudo nft add rule inet filter input udp dport 123 ip saddr 172.16.11.0/24 accept
$ sudo nft add rule inet filter input udp dport 123 ip saddr 172.16.12.0/24 accept

$ sudo nft list ruleset

$ sudo nft list ruleset | sudo tee /etc/nftables.conf
$ sudo systemctl restart nftables
```

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
Verification

$ sudo chronyc sources -v
$ sudo chronyc tracking
```

```shell
Уақытты қолмен синхрондау (тексеру үшін)
$ sudo chronyc makestep
200 OK
```

## NTP Client on Cisco IOS (Router, Switch)

```shell
configure terminal
ntp server 172.16.11.1 prefer        # NTP сервер IP адрес
clock timezone KZ +5                 # Уақыт белдеуін көрсету
```

```shell
NTP сұранысы болатын, нақты интерфейсті көрсету
ntp source GigabitEthernet0/0
```

```shell
Verification

show ntp associations
show ntp status
```

```shell
Қосымша қауіпсіздік (Access Control List)

access-list 10 permit 172.16.11.1
ntp access-group peer 10
```

## NTP Client on Huawei VRP (Router, Switch)

```shell
system-view
ntp-service enable
ntp-service unicast-server 172.16.11.1

clock timezone KZ add 5
```

```shell
Verification

display ntp-service status
display ntp-service sessions
```

```shell
Қосымша қауіпсіздік (Access Control List)

acl number 2000
 rule permit ip source 172.16.11.1 0.0.0.0
ntp-service acl 2000
```
