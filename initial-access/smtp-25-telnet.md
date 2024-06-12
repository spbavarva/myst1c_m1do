# SMTP - 25 - telnet

To connect, use the following command:

```bash
telnet $IP 25

nc -nv $IP 25
```

After connecting to the SMTP server via telnet as shown above, you can explore various commands to interact with the server

*   **EHLO/HELO**: These commands are used to identify the client to the server. It's often the first command sent after establishing a connection. Example:

    ```
    EHLO example.com
    ```
*   **VRFY**: This command is used to verify if a user exists on the server. Example:

    ```
    VRFY user@example.com

    VRFY root
    ```

![[spaces_ROk2K3zZId4eElllNbe7_uploads_kipbIJE3zXrxCXK37wUS_image.webp]]


* we can check with nmap that which command we can use with -sCV

***

* we can further assess the server's configuration for potential open relay vulnerabilities. Use the command below to check if the server is an open relay, which might allow the unauthorized sending of emails:

```
sudo nmap 10.129.14.128 -p25 --script smtp-open-relay
```

This Nmap script attempts to relay an email through the target SMTP server. An open relay server can be used to send unsolicited emails, contributing to spam or other malicious activities.

***

#### Checking for User Enumeration Vulnerability

```shell
smtp-user-enum -M VRFY -U list.txt -t 10.129.108.68 -w 100 -v
```

* `-M VRFY` tells the tool to use the VRFY method.
* `-U list.txt` specifies the user list file.
* `-t 10.129.108.68` is the target server's IP.
* `-w 100` sets the wait time between queries.
* `-v` enables verbose mode for detailed output.

This step is essential for identifying potential accounts that could be exploited by malicious actors to gain unauthorized access to the server.
