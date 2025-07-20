# DHCP Server on Debian12

### Тақырыбы: Debian Linux дистрибутивінде DHCP-ды конфигурациялау
### Жұмыстың орындалу қадамы: 
  1) DHCP-ды орнату;
  2) DHCP-ды конфигурациялау;
  3) Нәтижені тексеру.

### Корпоративті желінің топологиясы
![Topology Enterprise Campus Network](Topology_Enterprise_Compus_Network_v1.png)

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

---
<details>
<summary>Толығырақ көру</summary>
Мұнда толық ақпарат жазылады. Мысалы: Cisco құрылғылары тізімі, конфигурация т.б.
</details>

> Бұл – дәйексөз.

### Development

To start developing with AdminLTE:

1. **Install dependencies:** `npm install`
2. **Start development server:** `npm start` *(opens browser at http://localhost:3000)*
3. **Start coding!** Files auto-compile and refresh on changes

### Production Build

To build for production:

1. **Full production build:** `npm run production` *(includes linting and optimization)*
2. **Quick build:** `npm run build` *(faster for development/testing)*

### Available Scripts

- `npm start` - Start development server with file watching
- `npm run build` - Build all assets for development
- `npm run production` - Full production build with linting and bundlewatch
- `npm run lint` - Run all linters (JS, CSS, docs, lockfile)
- `npm run css` - Build CSS only
- `npm run js` - Build JavaScript only
