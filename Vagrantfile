Vagrant.configure("2") do |config|
  config.vm.box = "bento/debian-10"
  config.vm.hostname = "debian10-vm"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "4096"  # 4GB of RAM
    vb.cpus = 4         # 4 CPUs
  end

  # NAT port forwarding	
  # config.vm.network "forwarded_port", guest: 22, host: 2200, id: "ssh"
  
  # Enable bridged networking
  config.vm.network "public_network"

end

