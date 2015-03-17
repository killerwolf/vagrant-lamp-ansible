#!/usr/bin/env ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'yaml'
servers = YAML.load_file('servers.yml')

Vagrant.configure("2") do |config|

  config.vm.box = "wheezy64nocm"
  config.vm.box_url = "http://puppet-vagrant-boxes.puppetlabs.com/debian-73-x64-virtualbox-nocm.box"

  $ansible_groups = {
    "all_groups:children" => []
  }
  config.ssh.insert_key = false  
  servers.each_with_index do |server, index|
    config.vm.define server["name"].to_sym do |serv|
      serv.vm.provider :virtualbox do |v|
        v.customize ["modifyvm", :id, "--memory", server["memory"]]
        v.customize ["modifyvm", :id, "--name", server["name"]]
      end
      serv.vm.hostname = server["name"]
      serv.vm.network "private_network", ip: server["ip"]
      
      if $ansible_groups[server['group']].nil?
        $ansible_groups[server['group']] = []
        $ansible_groups["all_groups:children"] << server['group']
      end
      $ansible_groups[server['group']] << server["name"]
      
      if index == servers.size - 1
        serv.vm.provision "ansible" do |ansible|
          ansible.groups = $ansible_groups
          ansible.limit= "all"
          ansible.playbook = "ansible/playbook.main.yml"
        end
      end
    end 
  end

end
