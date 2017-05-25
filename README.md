# go-pssh
pssh CLI written in golang 

## Example

```
D:\sandbox\src\golang>type tmp.txt
192.168.141.68
192.168.141.67

D:\sandbox\src\golang>type tmp.txt|pssh.exe -P testpasswd -a "date;ip a | grep 192;sleep 5;date"
Async Mode running ...
Thu May 25 14:23:19 CST 2017
    inet 192.168.141.68/24 brd 127.255.255.255 scope global eth0
Thu May 25 14:23:24 CST 2017

Thu May 25 14:23:18 CST 2017
    inet 192.168.141.67/24 brd 192.168.141.255 scope global eth1
Thu May 25 14:23:24 CST 2017
```
## Usage

```
$ ./pssh --help
Usage of pssh:
  -P password
        Plain Unix account password.  (default "xxx")
  -T global_timeout
        Whole process  global_timeout. (default 300)
  -a async
        Enable async Mode instead of Default Sync Mode.
  -b onetime
        Set onetime Wait Group counter to control goroutines number. Note: only needed in Sync Mode.  (default 10)
  -d DEADLINE
        Remote CMD in ssh session will be closed after DEADLINE seconds.  (default 200)
  -i sshkey
        Private sshkey file path.  (default "C:\\Users\\simon\\.ssh\\id_rsa")
  -n username
        Unix username. (default "root")
  -p default_port
        SSH TCP default_port number.  (default 22)
  -t conn_timeout
        SSH connection conn_timeout.  (default 5)

```

### Sync Mode example


```
D:\sandbox\src\golang\src\lab>type tmp.txt
192.168.141.68
192.168.141.67
192.168.141.68
192.168.141.67
192.168.141.68:23
192.168.141.67
192.168.141.68
192.168.141.67
192.168.141.68:22
192.168.141.67
192.168.141.68
192.168.141.67
```

>  **Note**: 
- onetime=4, every 4x calls started & ended at same time; 
- SSH port listed in ip list will override default port 22 and -p flag specified; (in this example, port 23 was timeout due to default 5s conn_timeout)


```
graph LR
-b -->4
```
```
D:\sandbox\src\golang\src\lab>type tmp.txt|demo.exe -P testpasswd -b 4  "date;ip a | grep 192;sleep 5;date"
Sync Mode running ...
Thu May 25 14:53:14 CST 2017
    inet 192.168.141.67/24 brd 192.168.141.255 scope global eth1
Thu May 25 14:53:19 CST 2017

Thu May 25 14:53:14 CST 2017
    inet 192.168.141.67/24 brd 192.168.141.255 scope global eth1
Thu May 25 14:53:19 CST 2017

Thu May 25 14:53:14 CST 2017
    inet 192.168.141.68/24 brd 127.255.255.255 scope global eth0
Thu May 25 14:53:19 CST 2017

Thu May 25 14:53:14 CST 2017
    inet 192.168.141.68/24 brd 127.255.255.255 scope global eth0
Thu May 25 14:53:19 CST 2017

192.168.141.68:23: dial tcp 192.168.141.68:23: i/o timeout
Thu May 25 14:53:19 CST 2017
    inet 192.168.141.67/24 brd 192.168.141.255 scope global eth1
Thu May 25 14:53:24 CST 2017

Thu May 25 14:53:19 CST 2017
    inet 192.168.141.67/24 brd 192.168.141.255 scope global eth1
Thu May 25 14:53:24 CST 2017

Thu May 25 14:53:20 CST 2017
    inet 192.168.141.68/24 brd 127.255.255.255 scope global eth0
Thu May 25 14:53:25 CST 2017

Thu May 25 14:53:25 CST 2017
    inet 192.168.141.67/24 brd 192.168.141.255 scope global eth1
Thu May 25 14:53:30 CST 2017

Thu May 25 14:53:25 CST 2017
    inet 192.168.141.67/24 brd 192.168.141.255 scope global eth1
Thu May 25 14:53:30 CST 2017

Thu May 25 14:53:25 CST 2017
    inet 192.168.141.68/24 brd 127.255.255.255 scope global eth0
Thu May 25 14:53:30 CST 2017

Thu May 25 14:53:25 CST 2017
    inet 192.168.141.68/24 brd 127.255.255.255 scope global eth0
Thu May 25 14:53:30 CST 2017
```

### Deadline Sample

- deadline=4s, remote call sleeps 5s, that's why 2nd date info had no output in each call.

```
graph LR
-d-->4
```

```
D:\sandbox\src\golang\src\lab>type tmp.txt
192.168.141.68
192.168.141.67
192.168.141.68
192.168.141.67

D:\sandbox\src\golang\src\lab>type tmp.txt|demo.exe -P testpasswd -d 4  "date;ip a | grep 192;sleep 5;date"
Sync Mode running ...
Thu May 25 15:06:18 CST 2017
    inet 192.168.141.67/24 brd 192.168.141.255 scope global eth1

Thu May 25 15:06:19 CST 2017
    inet 192.168.141.68/24 brd 127.255.255.255 scope global eth0

Thu May 25 15:06:18 CST 2017
    inet 192.168.141.67/24 brd 192.168.141.255 scope global eth1

Thu May 25 15:06:19 CST 2017
    inet 192.168.141.68/24 brd 127.255.255.255 scope global eth0
```

### Private key file

- 192.168.141.68 has public key installed;
- Changed listed hosts passwd from testpasswd to 123;
- old passwd failed on 141.67, but 141.68 sucessed due to the key file 


```
graph LR
-i --> c:\Users\simon\.ssh\id_rsa_test
```


```
D:\sandbox\src\golang\src\lab>type tmp.txt
192.168.141.68
192.168.141.67

D:\sandbox\src\golang\src\lab>type tmp.txt|demo.exe -P testpasswd  "echo 123|passwd root --stdin"
Sync Mode running ...
Changing password for user root.
passwd: all authentication tokens updated successfully.

Changing password for user root.
passwd: all authentication tokens updated successfully.


D:\sandbox\src\golang\src\lab>type tmp.txt|demo.exe -P testpasswd -i c:\Users\simon\.ssh\id_rsa_test "ip a |grep 192"
Sync Mode running ...
    inet 192.168.141.68/24 brd 127.255.255.255 scope global eth0

192.168.141.67:22: ssh: handshake failed: ssh: unable to authenticate, attempted methods [publickey password none], no supported methods remain


```

### TODO

- [ ] Tracing  [this issue](https://github.com/golang/go/issues/18692) till x/crypto/ssh: Support Encrypted Private Keys