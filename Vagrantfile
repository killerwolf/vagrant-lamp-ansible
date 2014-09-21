#!/usr/bin/env ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'yaml'
servers = YAML.load_file('servers.yml')

Vagrant.configure("2") do |config|

  config.vm.box = "wheezy64nocm"
  config.vm.box_url = "http://puppet-vagrant-boxes.puppetlabs.com/debian-73-x64-virtualbox-nocm.box"

  servers.each.with_index do |servers, index|
    config.vm.define servers["name"].to_sym do |serv|
      serv.vm.provider :virtualbox do |v|
        v.customize ["modifyvm", :id, "--memory", servers["memory"]]
        v.customize ["modifyvm", :id, "--name", servers["name"]]
      end
      serv.vm.hostname = servers["name"]
      serv.vm.network "private_network", ip: servers["ip"]
      if index == 3
        serv.vm.provision "ansible" do |ansible|
          ansible.limit= "all"
          ansible.playbook = "ansible/playbook.main.yml"
          #TODO ansible inventory should be generated from servers.yml
          ansible.groups = {
            "db" => ["dbserver"],
            "lb" => ["lbserver"],
            "web" => ["webserver01", "webserver02"],
            "all_groups:children" => ["web", "lb", "db"]
          }
        end
      end
    end 

  end

end