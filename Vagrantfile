# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

# cluster configure
cluster = {
  "master-1" => { :ip => "10.245.6.2", :cpus => 2, :memory => 4096 },
  "slave-1"  => { :ip => "10.245.6.3", :cpus => 1, :memory => 2048 },
  "slave-2"  => { :ip => "10.245.6.4", :cpus => 1, :memory => 2048 },
}

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
###############################################################################
# Global plugin settings                                                      #
###############################################################################
  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
    config.cache.auto_detect = true
    config.cache.synced_folder_opts = {
      type: :nfs,
      mount_options: ['rw', 'vers=3', 'tcp', 'nolock']
    }
  end
  if Vagrant.has_plugin?("vagrant-vbguest")
    config.vbguest.auto_update = false
    config.vbguest.no_remote = true
  end
  if Vagrant.has_plugin?("vagrant-hostmanager")
    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true
    config.hostmanager.ignore_private_ip = false
  end
  # ssh
  config.ssh.username         = 'vagrant'
  config.ssh.insert_key       = false
  config.ssh.forward_agent    = true
  config.ssh.shell            = "bash -c 'BASH_ENV=/etc/profile exec bash'"
  config.ssh.private_key_path = ["#{ENV['HOME']}/.ssh/id_rsa", "#{ENV['HOME']}/.vagrant.d/insecure_private_key"]


###############################################################################
# Base box                                                                    #
###############################################################################
  config.vm.box              = "ubuntu/trusty64"
  config.vm.box_check_update = false

  cluster.each_with_index do |(hostname, info), index|
    config.vm.define hostname do |cfg|
      cfg.vm.hostname = hostname

      # virtualbox
      cfg.vm.provider :virtualbox do |vb|
        vb.name   = "#{hostname}"
        vb.cpus   = info[:cpus]
        vb.memory = info[:memory]
        vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      end

      # network
      cfg.vm.network :private_network, ip: "#{info[:ip]}", nictype: "virtio"
      if index == 0
        cfg.vm.network "forwarded_port", guest: 8080, host: 8080
        cfg.vm.network "forwarded_port", guest: 8081, host: 8081
      end

      # provision
      cfg.vm.provision "shell", inline: <<-SCRIPT
        printf "%s\n" "#{File.read("#{ENV['HOME']}/.ssh/id_rsa")}" > /root/.ssh/id_rsa
        printf "%s\n" "#{File.read("#{ENV['HOME']}/.ssh/id_rsa.pub")}" > /root/.ssh/id_rsa.pub
        chmod 400 /root/.ssh/id_rsa
        chmod 600 /root/.ssh/id_rsa.pub
      SCRIPT
      if index == cluster.size - 1
        cfg.vm.provision "ansible" do |ansible|
          ansible.limit          = "all"
          ansible.playbook       = "provision/development.yml"
          ansible.inventory_path = "provision/development"
        end
      end
    end
  end
end
