Linux ttl=61

rustscan and nmap showed only ssh but with full `-p-` I get redis port open `6379`

![[Pasted image 20240618012431.png]]

https://github.com/n0b0dyCN/redis-rogue-server

This worked so smooth

![[Pasted image 20240618012615.png]]

of course I tried to read that but some binary things there, so checked whether strings is installed or not and get a key from it

![[Pasted image 20240618012733.png]]

just escaped from it and got shell!!

another way is **PwnKit!!**