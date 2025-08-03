# NTP Server on Linux

#### 🖧 Топология
![Logical Topology](Topology/Topology_interVLANRouting_NAT_Linux.png)

# NTP Server on Debian

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
pool 2.kz.pool.ntp.org iburst
pool 1.kz.pool.ntp.org iburst
pool time.google.com iburst
pool time.cloudflare.com iburst
```

```shell
```

```shell
```

```shell
```
