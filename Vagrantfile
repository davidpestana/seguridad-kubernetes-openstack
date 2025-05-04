Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"

  def configure_ssh_key(vm)
    vm.vm.provision "file", source: "~/.vagrant.d/insecure_private_key", destination: "/home/vagrant/.ssh/id_rsa"
    vm.vm.provision "shell", inline: <<-SHELL
      chown vagrant:vagrant /home/vagrant/.ssh/id_rsa
      chmod 600 /home/vagrant/.ssh/id_rsa

      PUB_KEY=$(ssh-keygen -y -f /home/vagrant/.ssh/id_rsa)
      mkdir -p /home/vagrant/.ssh
      echo "$PUB_KEY" >> /home/vagrant/.ssh/authorized_keys
      chown vagrant:vagrant /home/vagrant/.ssh/authorized_keys
      chmod 600 /home/vagrant/.ssh/authorized_keys
    SHELL
  end

  config.vm.define "tower" do |tower|
    tower.vm.hostname = "tower"
    tower.vm.network "private_network", type: "dhcp"
    tower.vm.provider "virtualbox" do |vb|
      vb.name = "tower"
      vb.memory = 4096
      vb.cpus = 2
    end
    configure_ssh_key(tower)
  end

  config.vm.define "openstack" do |openstack|
    openstack.vm.box = "ubuntu/jammy64"
    openstack.vm.hostname = "openstack"
    openstack.vm.network "private_network", type: "dhcp"
    openstack.vm.provider "virtualbox" do |vb|
      vb.name = "openstack"
      vb.memory = 8192
      vb.cpus = 4
    end
    configure_ssh_key(openstack)

    # openstack.vm.provision "shell", inline: <<-SHELL
    #   apt-get update
    #   apt-get install -y python3 python3-pip python3-apt sudo git net-tools
    # SHELL
  end

  config.vm.define "openstack2" do |openstack|
    openstack.vm.box = "ubuntu/jammy64"
    openstack.vm.hostname = "openstack"
    openstack.vm.network "private_network", type: "dhcp"
    openstack.vm.provider "virtualbox" do |vb|
      vb.name = "openstack2"
      vb.memory = 8192
      vb.cpus = 4
    end
    configure_ssh_key(openstack)

    # openstack.vm.provision "shell", inline: <<-SHELL
    #   apt-get update
    #   apt-get install -y python3 python3-pip python3-apt sudo git net-tools
    # SHELL
  end


  config.vm.define "k8s" do |k8s|
    k8s.vm.hostname = "k8s"
    k8s.vm.network "private_network", type: "dhcp"
    # Red pública NAT con reenvío de puerto 6443 para exponer la API de Kubernetes
    k8s.vm.network "forwarded_port", guest: 6443, host: 6443, auto_correct: true
    k8s.vm.provider "virtualbox" do |vb|
      vb.name = "k8s"
      vb.memory = 4096
      vb.cpus = 2
    end
    configure_ssh_key(k8s)
  end
end
