Vagrant.configure(2) do |config| 
 config.vm.box = "centos/7" 
 config.vm.provider "virtualbox" do |v| 
 v.memory = 256 
 v.cpus = 1 
 end 
 
 #inetRouter
 config.vm.define "inetRouter" do |inetrouter|
 inetrouter.vm.host_name = 'inetRouter'
 inetrouter.vm.network :private_network, ip: '192.168.255.1', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "router-net"
 inetrouter.vm.provision "shell", inline: <<-SHELL
    mkdir -p ~root/.ssh
    cp ~vagrant/.ssh/auth* ~root/.ssh
    SHELL
 inetrouter.vm.provision "ansible" do |ansible|
       ansible.playbook = "provision.yml"
       ansible.become = "true"
 end
 inetrouter.vm.provision "shell", run: "always", inline: <<-SHELL
    ip route add 192.168.0.0/28 via 192.168.255.2 dev eth1
    iptables-restore < /vagrant/knocking-rules
    SHELL
end

#inetRouter2
config.vm.define "inetRouter2" do |inetrouter2|
  inetrouter2.vm.host_name = 'inetRouter2'
  inetrouter2.vm.network :private_network, ip: '192.168.245.1', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "router2-net"
  inetrouter2.vm.network :private_network, ip: '192.168.56.121', adapter: 3, netmask: "255.255.255.0", virtualbox__intnet: false
  inetrouter2.vm.provision "shell", inline: <<-SHELL
    mkdir -p ~root/.ssh
    cp ~vagrant/.ssh/auth* ~root/.ssh
    SHELL
  inetrouter2.vm.provision "ansible" do |ansible|
      ansible.playbook = "provision.yml"
      ansible.become = "true"
 end
  inetrouter2.vm.provision "shell", run: "always", inline: <<-SHELL
    ip route add 192.168.0.0/28 via 192.168.245.2 dev eth1
    iptables-restore < /vagrant/rules
    SHELL
end

#centralRouter
config.vm.define "centralRouter" do |centralrouter|
  centralrouter.vm.host_name = 'centralRouter'
  centralrouter.vm.network :private_network, ip: '192.168.255.2', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "router-net"
  centralrouter.vm.network :private_network, ip: '192.168.245.2', adapter: 3, netmask: "255.255.255.252", virtualbox__intnet: "router2-net"
  centralrouter.vm.network :private_network, ip: '192.168.0.1', adapter: 4, netmask: "255.255.255.240", virtualbox__intnet: "central-net"
  centralrouter.vm.provision "shell", inline: <<-SHELL
    mkdir -p ~root/.ssh
    cp ~vagrant/.ssh/auth* ~root/.ssh
    SHELL
  centralrouter.vm.provision "ansible" do |ansible|
      ansible.playbook = "provision.yml"
      ansible.become = "true"
 end
  centralrouter.vm.provision "shell", run: "always", inline: <<-SHELL
    systemctl restart network
    ip route del default
    ip route add default via 192.168.255.1 dev eth1
    ip route add 192.168.1.0/24 via 192.168.245.1 dev eth2
    ip route add 192.168.11.0/24 via 192.168.245.1 dev eth2
    chmod +x /vagrant/knock.sh
    SHELL
end

#centralServer
config.vm.define "centralServer" do |centralserver|
  centralserver.vm.host_name = 'centralServer'
  centralserver.vm.network :private_network, ip: '192.168.0.2', adapter: 2, netmask: "255.255.255.240", virtualbox__intnet: "central-net"
  centralserver.vm.network :private_network, adapter: 3, auto_config: false, virtualbox__intnet: true
  centralserver.vm.network :private_network, adapter: 4, auto_config: false, virtualbox__intnet: true
  centralserver.vm.provision "shell", inline: <<-SHELL
    mkdir -p ~root/.ssh
    cp ~vagrant/.ssh/auth* ~root/.ssh
    SHELL
  centralserver.vm.provision "ansible" do |ansible|
      ansible.playbook = "provision.yml"
      ansible.become = "true"
 end
  centralserver.vm.provision "shell", run: "always", inline: <<-SHELL
    systemctl restart network
    ip route del default
    ip route add default via 192.168.0.1 dev eth1
    SHELL
end
end
