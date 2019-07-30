Vagrant.configure(2) do |config|
  server_configs = [
    {"hostname" => "ci", "ip" => "192.168.33.22", "port" => 2220, "memory_size" => "512", "execute_script" => true},
    {"hostname" => "web", "ip" => "192.168.33.23", "port" => 2221, "memory_size" => "512", "execute_script" => false},
    {"hostname" => "db", "ip" => "192.168.33.24", "port" => 2223, "memory_size" => "512", "execute_script" => false},
  ]

  $script = "
   sudo apt update
   sudo apt install software-properties-common
   sudo apt-add-repository ppa:ansible/ansible
   sudo apt update
   sudo apt-get install -y git-core
   sudo apt install -y ansible
   cd ansible-playbook
   cp vagrant/insecure_private_key /home/vagrant/.ssh/id_rsa
   cp vagrant/ssh_config /home/vagrant/.ssh/config
   chmod -R og-rwx /home/vagrant/.ssh
   chown -R vagrant.vagrant /home/vagrant/.ssh
   sudo cp vagrant/ansible.conf /etc/ansible/ansible.cfg

   # sudo -u vagrant ansible-galaxy --ignore-errors install -p roles -r requirements.yml
"

  server_configs.each do |server_config|
    config.vm.define "#{server_config['hostname']}" do |server|
      server.vm.hostname = server_config['hostname']
      server.vm.box = "bento/ubuntu-16.04"
      server.vm.network :private_network, ip: server_config['ip']
      server.vm.network :forwarded_port, guest: 22, host: server_config['port'], id: "ssh"
      server.vm.provider "virtualbox" do |v|
        v.customize ["modifyvm", :id, "--memory", server_config['memory_size']]
        v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      end
      server.vm.synced_folder ".", "/home/vagrant/ansible-playbook", owner: "root", group: "root", :create => true, :mount_options => ["fmode=777", "dmode=777"]
      if server_config['execute_script'] then
        server.vm.provision :shell, inline: $script
      end
      server.ssh.private_key_path = "./vagrant/insecure_private_key"
      server.ssh.insert_key = false
    end
  end
end

