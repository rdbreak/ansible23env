VAGRANTFILE_API_VERSION = "2"
VAGRANT_DISABLE_VBOXSYMLINKCREATE = "1"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
# Use same SSH key for each machine
config.ssh.insert_key = false
config.vm.box_check_update = true

# Node 1 configuration
config.vm.define "node1" do |node1|
  node1.vm.box = "rdbreak/ansible23node"
#  node1.vm.hostname = "node1.ansi.example.com"
  node1.vm.network "private_network", ip: "192.168.55.61"
  node1.vm.provider "virtualbox" do |node1|
    node1.memory = "512"
  end
end

# Node 2 configuration
config.vm.define "node2" do |node2|
  node2.vm.box = "rdbreak/ansible23node"
#  node2.vm.hostname = "node2.ansi.example.com"
  node2.vm.network "private_network", ip: "192.168.55.62"
  node2.vm.provider "virtualbox" do |node2|
    node2.memory = "512"
  end
end

# Repo node configuration
config.vm.define "repo" do |repo|
  repo.vm.box = "rdbreak/ansible23repo"
#  repo.vm.hostname = "repo.ansi.example.com"
  repo.vm.network "private_network", ip: "192.168.55.59"
  repo.vm.provider "virtualbox" do |repo|
    repo.memory = "1024"
  end
end

# Control node configuration
config.vm.define "control" do |control|
  control.vm.box = "centos/7"
  control.vm.box_version = "1705.02"
  control.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config; sudo systemctl restart sshd;", run: "always"
  control.vm.provision :shell, :inline => "yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm -y; sudo yum install -y sshpass libselinux-python python-pip python-devel httpd sshpass vsftpd createrepo pki-ca", run: "always"
  control.vm.provision :shell, :inline => "yum groupinstall 'Development Tools' -y ;  python -m pip install -U pip ; python -m pip install pexpect; python -m pip install ansible", run: "always"
#  control.vm.hostname = "control.ansi.example.com"
control.vm.network "private_network", ip: "192.168.55.60"
  control.vm.provider :virtualbox do |control|
    control.customize ['modifyvm', :id,'--memory', '2048']
  end
#  control.vm.synced_folder ".", "/vagrant", type: "rsync", rsync__exclude: ".git/"

# Running Master playbook that begins configuration
  control.vm.provision :ansible_local do |ansible|
    ansible.playbook = '/vagrant/playbooks/envplaybooks/master.yml'
    ansible.install = false
    ansible.compatibility_mode = "2.0"
    ansible.inventory_path = "/vagrant/inventory"
    ansible.config_file = "/vagrant/ansible.cfg"
    ansible.limit = "all"
    control.vm.provision :shell, :inline => "python -m pip install 'ansible==2.3.1.0'", run: "always"
  end

# Creating welcome message
  control.vm.provision :ansible_local do |ansible|
    ansible.playbook = '/vagrant/playbooks/envplaybooks/welcome.yml'
    ansible.install = false
    ansible.compatibility_mode = "2.0"
    ansible.inventory_path = "/vagrant/inventory"
    ansible.config_file = "/vagrant/ansible.cfg"
    ansible.limit = "all"
  end
end
end
