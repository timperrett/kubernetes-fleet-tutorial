# -*- mode: ruby -*-
# # vi: set ft=ruby :

require 'fileutils'

Vagrant.require_version ">= 1.6.0"

# CLOUD_CONFIG_PATH = File.join(File.dirname(__FILE__), "user-data")

ETCD_NODE_CONFIG_PATH = File.join(File.dirname(__FILE__) + "/configs/", "etcd.yml")

CONFIG = File.join(File.dirname(__FILE__), "config.rb")

# Defaults for config options defined in CONFIG
$num_instances = 1
$update_channel = "alpha"
$enable_serial_logging = false
$vb_gui = false
$vb_memory = 1024
$vb_cpus = 1

# Attempt to apply the deprecated environment variable NUM_INSTANCES to
# $num_instances while allowing config.rb to override it
if ENV["NUM_INSTANCES"].to_i > 0 && ENV["NUM_INSTANCES"]
  $num_instances = ENV["NUM_INSTANCES"].to_i
end

if File.exist?(CONFIG)
  require CONFIG
end

Vagrant.configure("2") do |config|
  config.vm.box = "coreos-%s" % $update_channel
  config.vm.box_version = ">= 308.0.1"
  config.vm.box_url = "http://%s.release.core-os.net/amd64-usr/current/coreos_production_vagrant.json" % $update_channel

  config.vm.provider :virtualbox do |v|
    # On VirtualBox, we don't have guest additions or a functional vboxsf
    # in CoreOS, so tell Vagrant that so it can be smarter.
    v.check_guest_additions = false
    v.functional_vboxsf     = false
  end

  # plugin conflict
  if Vagrant.has_plugin?("vagrant-vbguest") then
    config.vbguest.auto_update = false
  end

  # (1..$num_instances).each do |i|
  #   config.vm.define vm_name = "coreos-etcd-%02d" % i do |config|
  #     config.vm.hostname = vm_name

  #     if $expose_docker_tcp
  #       config.vm.network "forwarded_port", guest: 2375, host: ($expose_docker_tcp + i - 1), auto_correct: true
  #     end

  #     config.vm.provider :virtualbox do |vb|
  #       vb.gui = $vb_gui
  #       vb.memory = $vb_memory
  #       vb.cpus = $vb_cpus
  #     end

  #     ip = "172.17.8.#{i+100}"
  #     config.vm.network :private_network, ip: ip

  #     # Uncomment below to enable NFS for sharing the host machine into the coreos-vagrant VM.
  #     #config.vm.synced_folder ".", "/home/core/share", id: "core", :nfs => true, :mount_options => ['nolock,vers=3,udp']

  #     if File.exist?(CLOUD_CONFIG_PATH)
  #       config.vm.provision :file, :source => "#{CLOUD_CONFIG_PATH}", :destination => "/tmp/vagrantfile-user-data"
  #       config.vm.provision :shell, :inline => "mv /tmp/vagrantfile-user-data /var/lib/coreos-vagrant/", :privileged => true
  #     end

  #   end
  # end

  config.vm.define "etcd" do |etcd|
    config.vm.provider :virtualbox do |vb|
      vb.gui = $vb_gui
      vb.memory = $vb_memory
      vb.cpus = $vb_cpus
    end

    config.vm.network :private_network, :ip => "172.17.8.100"

    if File.exist?(ETCD_NODE_CONFIG_PATH)
      config.vm.provision :file,
        :source => "#{ETCD_NODE_CONFIG_PATH}",
        :destination => "/tmp/vagrantfile-user-data"

      config.vm.provision :shell,
        :inline => "mv /tmp/vagrantfile-user-data /var/lib/coreos-vagrant/",
        :privileged => true
    end
  end

end