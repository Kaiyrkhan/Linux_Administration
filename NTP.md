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
$ ping -c2 185.116.194.200

$ dig +short time.google.com
216.239.35.12
$ ping -c2 216.239.35.12
```

```shell
8-қатарды (Line 8) "#" comment-ге алып, оның орнына Қазақстанға ең жақын NTP Pool серверлердің DNS атауын енгіземіз
$ sudo nano /etc/chrony/chrony.conf

#pool 2.debian.pool.ntp.org iburst

# Kazakhstan NTP pool
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

#### Configure NTP Client

```shell
```
