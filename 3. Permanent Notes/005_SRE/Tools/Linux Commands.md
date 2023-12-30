
- [[#find|find]]
- [[#sed|sed]]
- [[#awk|awk]]
- [[#curl|curl]]
- [[#ps|ps]]
- [[#netstat|netstat]]
- [[#ss|ss]]
- [[#lsof|lsof]]
- [[#proc|proc]]
- [[#nc|nc]]
- [[#telnet|telnet]]
- [[#dig|dig]]
- [[#xargs|xargs]]
- [[#cut|cut]]
- [[#tee|tee]]
- [[#grep|grep]]
- [[#uniq|uniq]]
- [[#sort|sort]]
- [[#tr|tr]]
- [[#grep / egrep / pgrep|grep / egrep / pgrep]]
- [[#head|head]]
- [[#wc|wc]]
- [[#tail|tail]]
- [[#wc|wc]]
- [[#rsync|rsync]]
- [[#ssh|ssh]]
- [[#zip|zip]]
- [[#du|du]]
- [[#free|free]]
- [[#df|df]]
- [[#strace|strace]]
- watch
- pbcopy
- ping
- nohup
- pv
- tshark
- tmux
- ethtool
- pushd
- popd
- Process management
	- pstree
	- top
	- atop
	- htop
- File transfer
	- scp
	- sftp
	- rsync
	- ftp
	- curl
	- wget
- Network config
	- ifconfig
	- ip addr
	- ip link
	- ip route
	- iwconfig
- Network Connection
	- netstat
	- ss
	- lsof
- DNS
	- /etc/resolve.conf
	- /etc/nssswitch.conf
	- nslookup
	- dig
	- host
- Network monitoring tool
	- tcpdump
	- wireshark
	- iftop
	- [[#perf]]
	- iptraf
	- ntop
	- nload
- Firewall
	- iptables
	- firewalld
	- ufw
- Network troubleshooting
	- ping
	- traceroute
	- mtr
	- nc
	- telnet
	- ip neigh
	- arp
	- netperf
	- nuttcp
	- iperf
- Bandwidth monitoring
	- bwm-ng
	- iftop
	- nload
	- vnstat
- Memory management
	- free
	- top
	- htop
	- /proc/meminfo
	- vmstat
	- sar
	- smem
	- atop
	- nmon
- Process Management
	- ps
	- pmap
- Cache and swapping
	- sync
	- echo 1 > /proc/sys/vm/drop_caches
	- swapon/swapoff
	- /proc/meminfo
- OOM handling
	- /var/log/messages
	- /var/log/syslog
	- dmesg
- Disk usage
	- [[#du|du]]
	- df
- JSON parsing
	- jq
- SELINUX
	- getfacl
	- setfacl
- File System Permission
	- chmod
	- chown
	- chroot
- Archiving and compression
	- tar
	- zip
	- gzip
- File system check
	- fsck
- Disk management and partitioning
	- fdisk
	- gdisk
	- mkfs
	- parted
	- dumpe2fs
	- mount/umount
- LVM
	-  pvcreate
	- vgcreate
	- lvcreate
	- resize2fs
	- lvresize
- RAID
	- mdadm
- FS encryption
	- cryptsetup
	- luksFormat
	- luksOpen
	- crypttab
- Network File System
	- /etc/exports
	- showmount
- File system monitoring
	- iostat

#### perf
```
perf // make sure its running

// tracing event of net category
sudo perf trace --no-syscalls --event 'net:*' ping 172.17.0.2 -c1 > /dev/null

sudo perf trace --no-syscalls           \
  --event 'net:net_dev_queue'           \
  --event 'net:netif_receive_skb_entry' \
  --event 'net:netif_rx'                \
  --event 'net:napi_gro_receive_entry'  \
  ping 172.17.0.2 -c1 > /dev/null

sudo perf list 'net:**'
```

#### find
- used to search files and directories in a location based on different criteria

use cases
```
find /local -type d 
find /local -type f
find . -maxdepth 1 -type f
find . -type f -exec cat {} +;
find . -type f -exec cat {} \;
find . -type f -mtime +7
find . -type f -name *.xml *-mtime +7
```
- in find `+` and `;`  are used to specify the argument passed to `-exec`
- `+` tells find that specified command on `exec` with as many argument as we can find. Good when dealing with large number of files. Basically if find gets 2 files then cat for those ten file will be like this `cat 1.txt 2.txt 3.txt`
- Remember when using `+`, we need to make sure that command supports multiple arguments
- `\;` tells find to run specified command on individual file.
- for files: we use `-exec`
- for directories: we use `-execdir`

#### sed
- stream editor, find and replace
- 

#### awk
- awk itself is a programming language, very powerful

#### curl 
- https://www.oodlestechnologies.com/blogs/some-advanced-uses-of-curl/
- https://reqbin.com/curl
- curl -o: output
- curl -v: verbose
- curl -L: Follow redirect
- curl -u : authentication
- curl --interface: follow specific interface
- curl --max-redirs: control redirection
- curl -z: Checksum
- curl --tls-max: Use specific TLS version
- curl exit codes: 1-7
- curl -v -s -o /dev/null: suppress body
- curl -XPOST: do HTTP request, default GET etc
- curl -u admin:admin https://demo.com: Authentication
- curl -H "Accept: application/json": Add headers
- curl -H "Authorization: Bearer {token}"

#### ps
- ps aux: x shows processes that are not attached to the terminal
- ps xuwww: shows complete width of command
- ps -o pid,ppid,command:  only see certain fields

#### netstat
- Get network statistic information
- netstat -tulpn: tcp, udp, actively listening connection, port, 
- netstat -s: show network statistic
- netstat -st: TCP stats
- netstat -ut: UDP stats
- netstat -i: show send/receive stats by interface
#### ss
- ss : faster and more readable 
- ss: lists all network connection regardless of their status
- ss | awk '{print $2}' | sort | uniq -c: shows all established connection and their state
- ss -tuln: scans for TCP and UDP port for listening sockets and displaying them in a list
- ss -i: see stats about every tcp connection
- sudo ss-t 'sport = :22':  see all clients connection to ssh port
- ss -t -o state established


### sar
- sar -n SOCK

#### lsof
- list open file but can do more than that
- lsof -nP -iTCP -sTCP:LISTEN: list all tcp port in use
- lsof -nP -i:9090: Check if port is in use

#### nmap
- port scan and more using script like NSE

#### proc

#### nc
- Can do almost anything related to TCP and UDP

#### telnet

#### dig
- +nostats +nocomments +nocmd +noquestion +recurse
- dig DOMAIN +short: get IP
- dig DOMAIN ns: get NS records
- dig DOMAIN @8.8.8.8
- dig -x IP: reverse dns lookup
- https://knowledgeacademy.io/digging-deep-into-dig-command/

#### xargs
- find /tmp -name core -type f -print | xargs /bin/rm -f
- xargs -L 1: tells to use one line as input per command, by default it sends all as a single argument
#### cut
- cut -c 1: character
- cut -b 1: byte
- cut -d " " -f 1: delimeter
- cut -d: -f1 < /etc/passwd | sort | xargs echo

#### tee
- Send to STDOUT and STDIN
- echo "hello" | tee hello.txt: Shows output in screen via STDOUT and also writes to file

#### grep

#### uniq
- finds unique line in given input
- uniq -c: counts the repeated line
- uniq -d: only prints line that are repeating
- uniq -u: prints only uniq lines
- uniq -i: ignores case and shows all unique lines
- uniq -s 2: Avoid comparing the first 2 lines

#### sort
- sort -r: sort in reverse order
- sort -u: sort and uniq

#### tr

#### grep / egrep / pgrep

#### head
- prints first part of the line
- head -n 10: print first 10 line

#### wc
- wc -l: count lines
- wc: counts words

#### tail
- shows last part of files
- tail -n 20: show last 20 lines

#### wc

#### rsync

#### ssh

#### zip

#### du

#### free

#### df

#### strace
- stack trace for debugging
- strace ls: show all system call
- strace -p PID: show stack trace for PID
- strace -f command: follow forks
- strace -e trace=write,open,read command: trace only particular system call

#### vmstat

#### mpstat

#### lsblk


