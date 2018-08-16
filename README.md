# ![alt text](/images/cfm-top.png) Bare Metal Integration Testbed setup (1vQFX and 6 VMs)


![Web Console](/images/cfm-1vqfx-5srv-topology.png)


The main code of this repository is taken from [Juniper/vqfx10k-vagrant](https://github.com/Juniper/vqfx10k-vagrant) to create a Testbed for TSN testing.

* 1 vQFX 10K
* 4 VMs CentOS 7.5 
  * 1 Provision VM
  * 1 Contrail Controller
  * 2 Compute Nodes
 
**Prerequisites**: A host machine with Ubuntu/CentOS OS preinstalled with Vagrant & VirtualBox SW.


```bash
host> git@gitlab.com:sohaibazed/contrail-gatewayless-forwarding.git
host> cd contrail-vagrant-deployer/tor-integration-5srv/
host> vagrant status
host> vagrantup
```

Download contrail-ansible-deployer.tar.gz file and place it inside the folder. By default without making any change in "Vagrantfile" above topology will be created. You can change MGMT and Ctrl+Data Subnet in Vagrantfile as needed.

```bash
$subnet_mgmt = "192.168.100"
$subnet_ctrl_data= "172.16.1"
```

### How to use Foxy Proxy for GUI access

Follow these steps for GUI access via FoxyProxy.
1- Open FireFox and open https://addons.mozilla.org/en-US/firefox/ URL.
2- Search for FoxyProxy and select "FoxyProxy Standard"
3- Click on "Add to Firefox"


![Web Console](/images/FoxyProxy-Install.png)

Now open ssh port forwading session to physical server using port 1080. please change IP as per your host config

```bash
your-laptop> ssh root@<< physical server ip>> -D 1080
```

Configure FireFox FoxyProxy add-on by configuring "127.0.0.1" & port 1080 as Scoks4 as captured in screenshot. 

![Web Console](/images/FoxyProxy-Configure.png)

Now enable FoxyProxy add-on by selecting the profile created earlier and open Contrail GUI using IP address of Vagrant VM https://192.168.100.11:8143

![Web Console](/images/FoxyProxy-Contrail-GUI-k8s.png)
.

### References

* <https://github.com/Juniper/contrail-ansible-deployer/wiki>
* <https://github.com/Juniper/vqfx10k-vagrant>

