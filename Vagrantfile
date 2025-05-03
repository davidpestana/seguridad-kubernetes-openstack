Vagrant.configure("2") do |config|
    config.vm.box = "ubuntu/jammy64"
  
    # VM 1: Ansible Tower/AWX
    config.vm.define "tower" do |tower|
      tower.vm.hostname = "tower"
      tower.vm.network "private_network", type: "dhcp"
      tower.vm.provider "virtualbox" do |vb|
        vb.name = "tower"
        vb.memory = 4096
        vb.cpus = 2
      end
    end
  
    # VM 2: OpenStack (DevStack)
    config.vm.define "openstack" do |openstack|
      openstack.vm.hostname = "openstack"
      openstack.vm.network "private_network", type: "dhcp"
      openstack.vm.provider "virtualbox" do |vb|
        vb.name = "openstack"
        vb.memory = 8192
        vb.cpus = 4
      end
    end
  
    # VM 3: Kubernetes (Kind)
    config.vm.define "k8s" do |k8s|
      k8s.vm.hostname = "k8s"
      k8s.vm.network "private_network", type: "dhcp"
      k8s.vm.provider "virtualbox" do |vb|
        vb.name = "k8s"
        vb.memory = 4096
        vb.cpus = 2
      end
    end
  end
  