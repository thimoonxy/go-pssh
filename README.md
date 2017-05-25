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