# -*- mode: ruby -*-
# vi: set ft=ruby :

Node = Struct.new(:hostname, :private_addr)
postgres_nodes = [
  Node.new("pg01", "192.168.77.10"),
  # Node.new("pg02", 192.168.77.11),
  # Node.new("pg03", 192.168.77.12),
]

etcd_nodes = [
  Node.new("etcd01", "192.168.77.13")
]

backup_nodes = [
  Node.new("backup01", "192.168.77.14")
]

# postgres_nodes = ["pg01", "pg02", "pg03"]
# etcd_nodes = ["etcd01"]
# backup_nodes = ["backup01"]

base_ip_net = "192.168.77."
base_ip = 20
saved_ips = {}
Vagrant.configure("2") do |config|
  config.vm.box = "generic/ubuntu2004"
  config.vm.provider "virtualbox" do |v|
    v.cpus = 1
    v.memory = 512
  end

  config.ssh.insert_key = false
  config.ssh.private_key_path = ["~/.vagrant.d/insecure_private_key", "./keys/id_rsa"]
  config.vm.provision "file", source: "./keys/id_rsa.pub", destination: "~/.ssh/authorized_keys"
  config.vm.provision "file", source: "./keys/id_rsa", destination: "~/.ssh/id_rsa"
  config.vm.provision "shell", inline: <<-EOC
  sudo sed -i -e "\\#PasswordAuthentication yes# s#PasswordAuthentication yes#PasswordAuthentication no#g" /etc/ssh/sshd_config
    sudo systemctl restart sshd.service
  echo "finished"
  EOC

  # nodes = (postgres_nodes+etcd_nodes+backup_nodes)
  # nodes.each_with_index do |node, index|
  #   ip = base_ip_net + (base_ip += index).to_s
  #   saved_ips[node] = ip
  # end

  (postgres_nodes+etcd_nodes+backup_nodes).each do |node|
    config.vm.define node.hostname do |conf|
      conf.vm.hostname = node.hostname
      conf.vm.network "private_network", ip: node.private_addr
    end
  end

  # (etcd_nodes).each do |node|
  #   config.vm.define node do |conf|
  #     conf.vm.hostname = node
  #     conf.vm.network "private_network", ip: saved_ips[node]
  #   end
  # end

  # (postgres_nodes).each do |node|
  #   config.vm.define node do |conf|
  #     conf.vm.hostname = node
  #     conf.vm.network "private_network", ip: saved_ips[node]
  #   end
  # end

  # (backup_nodes).each do |node|
  #   config.vm.define node do |conf|
  #     conf.vm.hostname = node
  #     conf.vm.network "private_network", ip: saved_ips[node]
  #   end
  # end

  # config.vm.provision "ansible" do |a|
  #   a.playbook = "provisioning/playbook.yaml"
  #   a.become = true
  #   a.groups = {
  #     "etcd" => etcd_nodes,
  #     "etcd_master" => etcd_nodes[0],
  #     "pg" => postgres_nodes,
  #     "pg:vars" => {"etcd_server" => saved_ips["etcd01"], "backup_server" => saved_ips["backup01"]},
  #     "backup" => backup_nodes,
  #     "backup:vars" => {"pg_servers" => saved_ips.select {|key,value| key.include?("pg")}.map {|k, v| v}.to_a}
  #   }
  # end

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
