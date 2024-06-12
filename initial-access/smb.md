# SMB

### List Shares with Anonymous Login

```sh
smbclient -N -L //$IP
```

```
nmap -p139,445 --script=smb-enum-shares 192.168.240.11
```

***

### Utilizing rpcclient for Additional Information

```sh
rpcclient -U "" $IP
```

Once logged in, you can execute various commands to explore further:

* `enumdomusers` : Lists the users.
* `queryuser id`: Queries detailed information about a user by their ID.
* `querydominfo`: Enumerates domains.
* `srvinfo`: Provides server information.
* `querydomain`: Queries domain information.

Script for Filtering `rpcclient` Results\*\*: To efficiently filter through user information in a range of IDs, you can use a shell script loop. The following script utilizes `rpcclient` to query user IDs from 500 to 1100, extracting key information such as the User Name, user\_rid, and group\_rid. Make sure to replace `10.129.14.128` with your target IP address.

{% code overflow="wrap" %}
```bash
for i in $(seq 500 1100); do
        rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo ""
    done
```
{% endcode %}

This command allows for a targeted approach in enumerating user details, streamlining the investigation process in identifying relevant accounts.

Ref: [https://book.hacktricks.xyz/network-services-pentesting/pentesting-smb/rpcclient-enumeration](https://book.hacktricks.xyz/network-services-pentesting/pentesting-smb/rpcclient-enumeration)

***

### samrdump.py

An alternative method for gathering user information and domain enumeration is through the use of Impacket's `samrdump.py`. This Python script can connect to a target machine and dump SAM database contents without the need for manual enumeration through `rpcclient`.

```bash
python samrdump.py DOMAIN/username:password@10.129.14.128
```

Replace `DOMAIN/username:password` with the actual domain, username, and password for the target system, and `10.129.14.128` with the target IP address.

***

### SMBMAP

For enumerating shared drives and permissions on a target system, SMBMap can be an invaluable tool. It enables users to quickly identify the shares available and the level of access to them.

```bash
smbmap -H 10.129.14.128
```

Replace `10.129.14.128` with the IP address of your target system.

### CrackMapExec

CrackMapExec (CME) is a Swiss army knife for pentesting networks. It is especially useful for assessing SMB services on Windows machines. CME can be used to find shares, check permissions, and even attempt logins.

```
nxc smb 192.168.240.11 --shares -u "" -p ""
```

Replace `10.129.14.128` with the IP address of the target system. Update `-u` (username) and `-p` (password) with valid credentials if available.

***

### enum4linux-ng

After using tools like `samrdump.py`, `smbmap`, and `CrackMapExec`, another powerful tool in the enumeration arsenal is `enum4linux-ng`. It is a rewrite of the classic `enum4linux` tool and aims to improve functionality and performance. It is designed for enumerating information from Windows and Samba systems.

```bash
enum4linux-ng -A 10.129.14.128
```

This command with the `-A` option will perform all simple enumerations on the target IP `10.129.14.128`. Replace `10.129.14.128` with your target system's IP address.
