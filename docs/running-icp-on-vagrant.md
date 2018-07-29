# Running IBM Cloud Private locally using Vagrant

## Installation Notes
* refer to [deploy-vagrant.md](./deploy-vagrant.md) for detailed instructions
  * took much longer than the 20-30 minutes suggested for the install on my Mac
  * I recommend using a utility _like_ [caffeinate](https://discussions.apple.com/thread/7858428) on the Mac to prevent the computer from going to sleep during the long install process
  * worked more reliably using an alternate network `base_segment` parameter
* I found the install and resume processes to be unreliable so I went with the cache server option
  * prepare yourself to run `vagrant destroy` and `vagrant up` more than once for the core ICP installation and after failed resume attempts
  * start-up and resume of the **LXC** `cfc-manager` instance is the least reliable - when and how it's used is also not clear to me


### Changed Vagrantfile parameters
```
license = "accept"
base_segment = '192.168.56'
use_cache = 'true'
cache_host = '192.168.56.99'
etcd_version = '3.2.14'
```

### Changed Cachefile parameters
```
base_segment = '192.168.56'
icp_cache.vm.box_version = "201806.08.0"
```

# Vagrant Commands
**install**: `vagrant up`  
**stop**: `vagrant halt` __(DO NOT USE! USE `vagrant suspend` TO HALT VM)__    
**start**: `vagrant up`  
**uninstall**: `vagrant destroy`  
**login to master node**: `vagrant ssh`  
**suspend**: `vagrant suspend`  
**resume**: `vagrant resume`

## Startup / Shutdown


### IBM Cloud Private Server - stop first / start last

To shutdown the ICP server use **suspend**:
```bash
vagrant suspend
```
NOTE:  `export VAGRANT_VAGRANTFILE=Vagrantfile` not required as Vagrantfile is the default configuration file

To **resume** the ICP server up again after **suspend**:
```bash
vagrant resume
```

To reset the ICP server after failed installation or failed resume attempt:
```bash
vagrant destroy
vagrant up
```

### Cache Server - stop last / start first

To shutdown the cache server use **halt**:
```bash
export VAGRANT_VAGRANTFILE=Cachefile
vagrant halt
```

To start the cache server after **halt**:  
```bash
export VAGRANT_VAGRANTFILE=Cachefile
vagrant up
```

## Check LXC instances on Virtual Box server
* from terminal window, `cd` to location where `Vagrantfile` is located
* run the following commands
```bash
vagrant ssh
lxc list
```
### Sample Output
* one key question is whether the `manager` instance is requried after installation
* compare this to sample `lxc list` output shown in [deploy-vagrant.md](./deploy-vagrant.md)
```
vagrant@master:~$ lxc list
+---------+---------+-----------------------+------+------------+-----------+
|  NAME   |  STATE  |         IPV4          | IPV6 |    TYPE    | SNAPSHOTS |
+---------+---------+-----------------------+------+------------+-----------+
| worker1 | RUNNING | 192.168.56.101 (eth0) |      | PERSISTENT | 0         |
|         |         | 172.17.0.1 (docker0)  |      |            |           |
|         |         | 10.1.235.128 (tunl0)  |      |            |           |
+---------+---------+-----------------------+------+------------+-----------+
| worker2 | RUNNING | 192.168.56.102 (eth0) |      | PERSISTENT | 0         |
|         |         | 172.17.0.1 (docker0)  |      |            |           |
|         |         | 10.1.189.64 (tunl0)   |      |            |           |
+---------+---------+-----------------------+------+------------+-----------+
```


# IBM Cloud Private web console
* URL will vary based on `base_segment` parameter:  [https://192.168.56.100:8443](https://192.168.56.100:8443)
* default username/password:  admin/admin



See [Accessing IBM Cloud Private](/README.md#accessing-ibm-cloud-private) for next steps.
