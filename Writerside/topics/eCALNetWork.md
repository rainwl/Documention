# eCAL NetWork

[eCAL office website](https://eclipse-ecal.github.io/ecal/getting_started/cloud.html#fa-windows-multicast-configuration-on-windows)

## Config

`C:\ProgramData\eCAL\ecal.ini`

```
[network]
network_enabled           = true
```

Open the cmd with administrator

`ipconfig`

```
 IPv4 Address. . . . . . . . . . . : 192.168.1.58
 ```

 ` route -p add 239.0.0.0 mask 255.255.255.0 192.168.1.58`

 ` route print`
 