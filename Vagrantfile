# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :router1 => {
        :box_name => "generic/ubuntu2204",
        :vm_name => "router1",
        #:public => {:ip => "10.10.10.1", :adapter => 1},
        :net => [   
                    #ip, adpter, netmask, virtualbox__intnet
                    ["192.168.10.1", 2, "255.255.255.0", "net1"], 
                    ["10.0.12.1", 3, "255.255.255.252", "r1-r3"], 
                    ["10.0.10.1", 4, "255.255.255.252", "r1-r2"], 
                    ["192.168.56.10", 8, "255.255.255.0"],
                ]
  },

  :router2 => {
        :box_name => "generic/ubuntu2204",
        :vm_name => "router2",
        #:public => {:ip => "10.10.10.1", :adapter => 1},
        :net => [   
                    #ip, adpter, netmask, virtualbox__intnet
                    ["192.168.20.1", 2, "255.255.255.0", "net2"], 
                    ["10.0.11.2", 3, "255.255.255.252", "r2-r3"], 
                    ["10.0.10.2", 4, "255.255.255.252", "r1-r2"], 
                    ["192.168.56.20", 8, "255.255.255.0"],
                ]
  },

  :router3 => {
        :box_name => "generic/ubuntu2204",
        :vm_name => "router3",
        :net => [
                    ["192.168.30.1", 2, "255.255.255.0", "net3"], 
                    ["10.0.12.2", 3, "255.255.255.252", "r1-r3"], 
                    ["10.0.11.1", 4, "255.255.255.252", "r2-r3"], 
                    ["192.168.56.30", 8, "255.255.255.0"],
                ]
  }
}

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|

    config.vm.define boxname do |box|
   
      box.vm.box = boxconfig[:box_name]
      box.vm.host_name = boxconfig[:vm_name]

      box.vm.provider "virtualbox" do |v|
        v.memory = 768
        v.cpus = 1
       end

       if boxconfig[:vm_name] == "router3"
        box.vm.provision "ansible" do |ansible|
         ansible.playbook = "ansible/playbook.yml"
         ansible.inventory_path = "ansible/hosts"
         ansible.host_key_checking = "false"
         ansible.limit = "all"
        end
       end

      boxconfig[:net].each do |ipconf|
        box.vm.network("private_network", ip: ipconf[0], adapter: ipconf[1], netmask: ipconf[2], virtualbox__intnet: ipconf[3])
      end

      if boxconfig.key?(:public)
        box.vm.network "public_network", boxconfig[:public]
      end

      box.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh
        cp ~vagrant/.ssh/auth* ~root/.ssh
      SHELL
    end
  end
end