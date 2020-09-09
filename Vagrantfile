# -*- mode: ruby -*-
# vi: set ft=ruby :
$domain = "mshome.net"
$domain_ip_address = "192.168.56.2"
$ubuntu_ip_address = "192.168.56.14"

unless Vagrant.has_plugin?("vagrant-vbguest")
  puts 'Installing vagrant-vbguest Plugin...'
  system('vagrant plugin install vagrant-vbguest')
end

unless Vagrant.has_plugin?("vagrant-reload")
  puts 'Installing vagrant-reload Plugin...'
  system('vagrant plugin install vagrant-reload')
end

Vagrant.configure("2") do |config|

  config.vm.define "dc" do |config|
    #config.vm.box = "danielmenezesbr/WindowsServerDC"
    #config.vm.box_version = "0"
    config.vm.box = "cdaf/WindowsServerDC"
    config.vm.box_version = "2020.05.14"
	  

    config.winrm.username = "vagrant"
    config.winrm.password = "vagrant"
    config.vm.guest = :windows
    config.vm.communicator = "winrm"
    config.winrm.timeout = 2800 # 30 minutes
    config.winrm.max_tries = 40
    config.winrm.retry_limit = 200
    config.winrm.retry_delay = 10
    config.vm.graceful_halt_timeout = 600

    config.vm.hostname = "winserver"
    config.vm.network "private_network", ip: "192.168.56.2"
    #config.vm.provision "shell", path: "provision/uninstall-windefeder.ps1"
    #config.vm.provision "shell", reboot: true
    #config.vm.provision "shell", path: "provision/ad.ps1"
    #config.vm.provision "ie", type: "shell", path: "provision/ie.ps1"

    config.vm.provider :virtualbox do |v, override|
      v.memory = 2024
	  v.cpus = 1
	  v.gui = false #TODO
	  #v.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
	  #v.customize ["modifyvm", :id, "--nictype1", "Am79C973"]
          #v.customize ['modifyvm', :id, '--cableconnected1', 'on']
    end

	config.vbguest.auto_update = false
  end

  config.vm.define "client" do |config|
    config.vm.box = "bento/ubuntu-18.04"
    config.vm.box_version = "202005.21.0"
	#config.vm.box = "ubuntu/bionic64"
    #config.vm.box_version = "20200610.1.0"

    config.vm.provider :virtualbox do |v, override|
      v.memory = 3000
	  v.cpus = 2
    end

    config.vm.hostname = "clientlinux.#{$domain}"
    config.vm.network "private_network", ip: $ubuntu_ip_address, libvirt__forward_mode: "route", libvirt__dhcp_enabled: false
    config.vm.provision "shell", path: "provision/clientlinux/provision/provision-base.sh"
    config.vm.provision "shell", path: "provision/clientlinux/provision/add-to-domain.sh", args: [$domain, $domain_ip_address]
    config.vm.provision :reload
    config.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "provision/clientlinux/provision/playbook.yml"
    end
  end

  config.vm.box_check_update = false
  config.vm.boot_timeout = 2800

end
