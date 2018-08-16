# -*- mode: ruby -*
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

$script = <<-SCRIPT
sudo route add -net 172.16.0.0 netmask 255.255.0.0 gw 172.16.1.1
tar -zxvf /home/vagrant/contrail-ansible-deployer-5.0.0-0.40.tgz
cp /home/vagrant/instances.yaml /home/vagrant/contrail-ansible-deployer/config/instances.yaml
cd /home/vagrant/contrail-ansible-deployer
ansible-playbook -i inventory/ -e orchestrator=openstack playbooks/configure_instances.yml
ansible-playbook -i inventory/ -e orchestrator=openstack playbooks/install_contrail.yml
SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

    re_name  = "vqfx"
    pfe_name = "vqfx-pfe"

    ##############################
    ## Packet Forwarding Engine ##
    ##############################
    config.vm.define pfe_name do |vqfxpfe|
        vqfxpfe.ssh.insert_key = false
        vqfxpfe.vm.box = 'juniper/vqfx10k-pfe'

        # DO NOT REMOVE / NO VMtools installed
        vqfxpfe.vm.synced_folder '.', '/vagrant', disabled: true
        vqfxpfe.vm.network 'private_network', auto_config: false, nic_type: '82540EM', virtualbox__intnet: "vqfx_internal"
    end

    ##########################
    ## Routing Engine  #######
    ##########################
    config.vm.define re_name do |vqfx|
        vqfx.vm.hostname = "vqfx"
        vqfx.vm.box = 'juniper/vqfx10k-re'
        vqfx.ssh.insert_key = false

        # DO NOT REMOVE / NO VMtools installed
        vqfx.vm.synced_folder '.', '/vagrant', disabled: true

        # Management Ports
        vqfx.vm.network 'private_network', auto_config: false, nic_type: '82540EM', virtualbox__intnet: "vqfx_internal"
        vqfx.vm.network 'private_network', auto_config: false, nic_type: '82540EM', virtualbox__intnet: "reserved-bridge"

        ## Dataplane ports
        (1..8).each do |seg_id|
           vqfx.vm.network 'private_network', auto_config: false, nic_type: '82540EM', virtualbox__intnet: "seg#{seg_id}"
        end
    end

    ##########################
    ## Servers         #######
    ##########################
# Defaults for config options defined in CONFIG
controller_name = "srv1"
$subnet_mgmt = "192.168.100"
$subnet_ctrl_data= "172.16.1"
    (1..3).each do |id|
       srv_name = ( "srv" + id.to_s ).to_sym
        config.vm.define srv_name do |srv|
            srv.vm.box = "qarham/CentOS7.5-350GB"
            srv.vm.hostname = "srv#{id}"
            srv.vm.network "public_network", bridge: "br1", ip:"#{$subnet_mgmt}.#{id+10}"
            srv.vm.network 'private_network', ip: "#{$subnet_ctrl_data}.#{id+100}", nic_type: '82540EM', virtualbox__intnet: "seg#{id}"
            srv.ssh.insert_key = true
            srv.vm.provision "shell", path: "scripts/ntp.sh"
            srv.vm.provision "shell",
               inline: "sudo route add -net 172.16.0.0 netmask 255.255.0.0 gw 172.16.1.1"
        config.vm.provider :virtualbox do |vb|
            vb.customize ["modifyvm", :id, "--memory", "32768", "--cpus", "4"]
          end
        end
    end



    config.vm.define "provision" do |p|
            p.vm.box = "qarham/CentOS7.5-350GB"
            p.vm.hostname = "provision"
            p.vm.network "public_network", bridge: "br1", ip:"#{$subnet_mgmt}.16"
            p.vm.network 'private_network', ip: "#{$subnet_ctrl_data}.106", nic_type: '82540EM', virtualbox__intnet: "seg6"
            p.ssh.insert_key = true
            p.vm.provision "shell", path: "scripts/ntp.sh"
            p.vm.provision "file", source: "instances.yaml", destination: "/home/vagrant/instances.yaml"
            p.vm.provision "file", source: "contrail-ansible-deployer-5.0.0-0.40.tgz", destination: "/home/vagrant/contrail-ansible-deployer-5.0.0-0.40.tgz"
            p.vm.provision "shell",
               inline: $script
        config.vm.provider :virtualbox do |vb|
            vb.customize ["modifyvm", :id, "--memory", "2048", "--cpus", "1"]
        end 
    end



    ##############################
    ## Box provisioning        ###
    ## exclude Windows host    ###
    ##############################
    if !Vagrant::Util::Platform.windows?
        config.vm.provision "ansible" do |ansible|
            ansible.groups = {
                "vqfx10k" => ["vqfx"],
                "all:children" => ["vqfx10k"]
            }
            ansible.playbook = "provisioning/deploy-config.p.yaml"
	end
    end
end
