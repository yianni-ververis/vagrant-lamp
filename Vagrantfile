# -*- mode: ruby -*-
# vi: set ft=ruby :

$script = <<-SCRIPT
# Docker
test -e /usr/local/bin/docker-compose || \\
curl -sSL https://github.com/docker/compose/releases/download/1.29.1/docker-compose-`uname -s`-`uname -m` \\
  | sudo tee /usr/local/bin/docker-compose > /dev/null
sudo chmod +x /usr/local/bin/docker-compose
test -e /etc/bash_completion.d/docker-compose || \\
curl -sSL https://raw.githubusercontent.com/docker/compose/$(docker-compose --version | awk 'NR==1{print $NF}')/contrib/completion/bash/docker-compose \\
  | sudo tee /etc/bash_completion.d/docker-compose > /dev/null

  cd ../../vagrant
  docker-compose up -d
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.provider "virtualbox" do |v|
    v.memory = 8192
    v.cpus = 2
    v.name = "vag-lamp"
  end

  # Disable GuestAdditions update.
  if Vagrant.has_plugin? "vagrant-vbguest"
    config.vbguest.auto_update = false
  end
  
  # Specify your hostname if you like
  config.vm.hostname = "vag-lamp"
  config.vm.network :forwarded_port, guest: 80, host: 80, id: "80", auto_correct: true
  config.vm.network :forwarded_port, guest: 3306, host: 3306, id: "mariadb"
  config.vm.network :forwarded_port, guest: 8080, host: 8080, id: "adminer"
  config.vm.box = "bento/ubuntu-20.10" 
  config.vm.provision :docker
  config.vm.synced_folder "./vagrant", "/vagrant",
    create: true,
    group:'vagrant',
    mount_options: ["dmode=777,fmode=777"]
  config.vm.provision :shell, inline: $script
end