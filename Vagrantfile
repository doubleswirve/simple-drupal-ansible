# -*- mode: ruby -*-
# vi: set ft=ruby :

# Load YAML lib to parse user configuration
require "yaml"

# Parse user config
current_file_path = File.expand_path __FILE__
current_dir       = File.dirname current_file_path
user_config_file  = "#{current_dir}/config.yml"

unless File.exists? user_config_file
  puts "Please create config.yml file to provision your development environment."
  abort
end

user_config = YAML::load_file user_config_file

unless user_config.has_key? "weburl"
  puts "Please set a weburl in config.yml."
  abort
end

unless user_config.has_key? "sitename"
  puts "Please set a sitename in config.yml."
  abort
end

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "trusty64"

  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  config.vm.box_url = "https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "10.86.73.82"

  # Bump up memory and speed up http requests
  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--memory", "1024"]
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
  end

  # Provision
  config.vm.provision "ansible" do |ansible|
    ansible.extra_vars = {
      is_development: true,
      hostname: user_config["weburl"],
      mysql_root_password: "password",
      sitename: user_config["sitename"],
      weburl: user_config["weburl"]
    }
    ansible.playbook = "provision/web-development.yml"
    ansible.sudo = true
  end
end
