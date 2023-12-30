# What have containers done for you lately?
https://www.youtube.com/watch?v=MHv6cWjvQjM


- Basically processes separated using namespace and capabilities
- Isolation using namespaces
- namespaces
	- PID
	- MOUNT
	- NETWORK
	- USER
- CGROUP
	- Restrict the resource usage of process
		- RAM
		- DISK
		- CPU
- 
  ### See containers
```
crictl ps
```

### configuration
```
cat /etc/crictl.yaml

runtime-endpoint: unix:///run/containerd/containerd.sock
```

This is basically telling `crictl` to communicate with `containerd`

`ip net-ns`

       A network namespace is logically another copy of the network stack, with its own routes, firewall rules, and network devices.


see more using: `man ip net-ns`

#### Add a new network namespace

`ip netns add netns0`

`ip netns`

##### Get shell into network namespace

`nsenter`

run program with namespaces of other processes.  Enters  the  namespaces  of one or more other processes and then executes the specified program. If program is not given, then ``${SHELL}'' is run
       (default: /bin/sh).



`sudo nsenter --net=/var/run/netns/netns0 bash`

#### Creating virtual ethernet

`man veth`

create

`ip link add veth0 type veth peer name ceth0`


Root namespace -> veth0 <-> ceth0 


###### move ceth0 to netns0

`ip link set ceth0 netns netns0`

Root namespace -> veth0 <-> ceth0 -> netns0 namespace


#### Assign ip address to each interface

`ip link set veth0 up`
`ip addr add 172.18.0.11/16 dev veth0`

`ip link set ceth0 up`
`ip addr add 172.18.0.10/16 dev ceth0`