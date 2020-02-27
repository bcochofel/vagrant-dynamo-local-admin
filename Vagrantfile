# -*- mode: ruby -*-
# vi: set ft=ruby :

# Check for missing plugins
required_plugins = %w(vagrant-hostmanager)
plugin_installed = false
required_plugins.each do |plugin|
  unless Vagrant.has_plugin?(plugin)
    system "vagrant plugin install #{plugin}"
    plugin_installed = true
  end
end

# If new plugins installed, restart Vagrant process
if plugin_installed === true
  exec "vagrant #{ARGV.join' '}"
end

Vagrant.configure("2") do |config|
  # vagrant-hostmanager options
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true

  # Forward ssh agent to easily ssh into the different machines
  config.ssh.forward_agent = true

  config.vm.box = "bento/ubuntu-18.04"
  config.vm.hostname = "dynamodb.local.dev"

  config.vm.network :private_network, ip: "192.168.77.102"
  config.vm.network :forwarded_port, guest: 8000, host: 8000

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.name = "DynamoDB local Admin"
    vb.memory = "2048"
    vb.cpus = "2"
  end

  config.vm.provision "docker" do |d|
    d.pull_images "instructure/dynamo-local-admin"
    d.run "instructure/dynamo-local-admin",
      args: "-p 8000:8000 -it"
  end
end
