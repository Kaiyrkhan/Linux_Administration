# DHCP Server on Debian12

### Тақырыбы: Debian Linux дистрибутивінде DHCP-ды конфигурациялау
### Жұмыстың орындалу қадамы: 
  1) DHCP-ды орнату;
  2) DHCP-ды конфигурациялау;
  3) Нәтижені тексеру.

### Корпоративті желінің топологиясы
<img width="1205" height="886" alt="Topology_Enterprise_Compus_Network_v1" src="https://github.com/user-attachments/assets/98df986a-2f8e-4002-bfcc-ce0e9e8732d4" />

### DHCP-ды орнату
```shell
$ sudo apt update
$ sudo apt upgrade -y
```
```shell
$ sudo apt install -y isc-dhcp-server
```

### DHCP-ды конфигурациялау
```shell
$ ip address
```
```shell
$ sudo nano /etc/default/isc-dhcp-server
INTERFACESv4="ens3"
INTERFACESv6=""
```
