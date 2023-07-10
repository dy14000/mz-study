# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"

  config.vm.define "sshfs" do |sshfs|
    sshfs.vm.hostname = "sshfs-server"
    sshfs.vm.provider "virtualbox" do |vb|
      vb.name = "sshfs-server"
      vb.cpus = 1
      vb.memory = 2048
    end
    sshfs.vm.network "private_network", ip: "192.168.33.250"
    sshfs.vm.provision "shell", inline: <<-SCRIPT
     sudo useradd -m -s /bin/bash sshfs -d name /home/sshfs
     echo sshfs:qwerasdfzxcv | sudo chpasswd
     git clone https://github.com/dy14000/static_web_template.git
     sudo cp home/static_web_temlpate/* /home/sshfs


    SCRIPT
  end
   
  config.vm.define "ubuntu" do |ubuntu|
    ubuntu.vm.hostname = "mz-server"
    ubuntu.vm.provider "virtualbox" do |vb|
      vb.name = "docker-server"
      vb.cpus = 4
      vb.memory = 8192
    end





    ubuntu.vm.network "forwarded_port", guest: 8080, host: 80 
    ubuntu.vm.network "private_network", ip: "192.168.33.200"
    ubuntu.vm.provision "shell", inline: <<-SCRIPT
      sudo apt-get update -y
      sudo apt-get install -y ca-certificates curl gnupg
      sudo install -m 0755 -d /etc/apt/keyrings
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
      sudo chmod a+r /etc/apt/keyrings/docker.gpg
      echo \
        "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
        "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
        sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
      sudo apt-get update -y
      sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
      sudo usermod -a -G docker vagrant
      docker login -u dy14000 --password-stdin < /vagrant/env/docker_token
      docker volume create 
    SCRIPT
  end
end