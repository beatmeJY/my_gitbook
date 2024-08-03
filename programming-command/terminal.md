# Terminal

리눅스에서 포트 번호 레인지 확인하는 명령어.

```bash
cat /proc/sys/net/ipv4/ip_local_port_range
32768	60999
```



포트 레인지 튜닝 명령어

```bash
$ sysctl net.ipv4.ip_local_port_range
net.ipv4.ip_local_port_range = 32768	60999
```

```bash
$ sudo sysctl -w net.ipv4.ip_local_port_range="1024 65535"
```
