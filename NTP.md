# NTP Server on Linux

#### 🖧 Топология
![Topology](Topology/Topology_interVLANRouting_NAT_Linux.png)

# NTP Server on Debian

#### Configure NTP Server
```shell
$ sudo apt update 
$ sudo apt install chrony
```

```shell
Set timezone
$ sudo timedatectl set-timezone Asia/Almaty
```

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

```shell
8-қатарды (Line 8) "#" comment-ге алып, оның орнына Қазақстанға ең жақын NTP Pool серверлердің DNS атауын енгіземіз
$ sudo nano /etc/chrony/chrony.conf

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
$ sudo systemctl restart chrony
```

```shell
$ sudo chronyc sources -v

$ dig +short ntp.nic.kz
80.241.0.72

$ ping 80.241.0.72
64 bytes from 80.241.0.72: icmp_seq=1 ttl=53 time16.7 ms
64 bytes from 80.241.0.72: icmp_seq=1 ttl=53 time15.7 ms

$ sudo apt install ntpdate
$ sudo ntpdate -q 80.241.0.72

$ sudo chronyc tracking
```
![images](images/images.png)

```shell
$ sudo nano /etc/chrony/chrony.conf
# Allow access for Local Networks
allow 172.16.11.0/24
allow 172.16.12.0/24

$ sudo systemctl restart chrony
```

```shell
$ sudo nft add rule inet filter input udp dport 123 ip saddr 172.16.11.0/24 accept
$ sudo nft add rule inet filter input udp dport 123 ip saddr 172.16.12.0/24 accept

$ sudo nft list ruleset

$ sudo nft list ruleset | sudo tee /etc/nftables.conf
$ sudo systemctl restart nftables
```

#### Configure NTP Client on Linux

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

#### Configure NTP Client on Cisco IOS

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
Access Control List

access-list 10 permit 172.16.11.1
ntp access-group peer 10
```
