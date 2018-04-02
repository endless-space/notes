# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
	(1..3).each do |i|
		config.vm.define "node#{i}" do |node|
			node.vm.box = "centos7"
			node.vm.hostname = "node#{i}"
			node.vm.network "private_network", ip: "192.168.66.1#{i}"
			node.vm.synced_folder "e:/", "/vagrant_data"
			node.vm.provider "virtualbox" do |v|
				v.name = "node#{i}"
				v.gui = false
				v.cpus = 1
				v.memory = 2048
			end
		end
	end
end
