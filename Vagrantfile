# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

servers = [
  {
    :name => "master-server",
    :box => "bento/ubuntu-18.04",
    :enp0s8 => "192.168.33.77",
    :mem => "2048",
    :cpu => "2"
  },
  {
    :name => "device01-server",
    :box => "bento/ubuntu-18.04",
    :enp0s8 => "192.168.33.150",
    :mem => "1024",
    :cpu => "1"
  },
  {
    :name => "device02-server",
    :box => "bento/ubuntu-18.04",
    :enp0s8 => "192.168.33.151",
    :mem => "1024",
    :cpu => "1"
  }
]

# Docker provisioning
$configureBox = <<-SCRIPT
  # install docker v17.03
  apt-get update
  echo "Uninstall old versions"
  apt-get remove -y docker docker-engine docker.io containerd runc
  export DEBIAN_FRONTEND=noninteractive
  apt-get install -y apt-transport-https ca-certificates curl software-properties-common gnupg-agent
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
  add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
  apt-get update
  echo "Docker Install"
  apt-get install -y docker-ce docker-ce-cli containerd.io
  # run docker commands as vagrant user (sudo not required)
  sudo gpasswd -a vagrant docker
  sudo chmod 666 /var/run/docker.sock
  # install docker-compose
  sudo curl -L "https://github.com/docker/compose/releases/download/1.25.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  chmod +x /usr/local/bin/docker-compose
SCRIPT

Vagrant.configure("2") do |config|

  servers.each do |opts|
    config.vm.define opts[:name] do |config|

      config.vm.box = opts[:box]
      config.vm.hostname = opts[:name]
      config.vm.network "private_network", ip: opts[:enp0s8]

      # Share an additional folder to the guest VM. The first argument is
      # the path on the host to the actual folder. The second argument is
      # the path on the guest to mount the folder. And the optional third
      # argument is a set of non-required options.
      # config.vm.synced_folder "../data", "/vagrant_data"

      # Provider-specific configuration so you can fine-tune various
      # backing providers for Vagrant. These expose provider-specific options.
      # Example for VirtualBox:
      #
      config.vm.provider "virtualbox" do |vb|
        vb.name = opts[:name]
        vb.customize ["modifyvm", :id, "--memory", opts[:mem]]
        vb.customize ["modifyvm", :id, "--cpus", opts[:cpu]]
      end

      config.vm.provision "shell", inline: $configureBox

      # Enable provisioning with a shell script. Additional provisioners such as
      # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
      # documentation for more information about their specific syntax and use.
      # config.vm.provision "shell", inline: <<-SHELL
      #   apt-get update
      #   apt-get install -y apache2
      # SHELL
    end
  end
end
