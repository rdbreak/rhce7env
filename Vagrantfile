VAGRANTFILE_API_VERSION = "2"
VAGRANT_DISABLE_VBOXSYMLINKCREATE = "1"
file_to_disk1 = './disk-0-1.vdi'
file_to_disk2 = './disk-0-2.vdi'
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
# Use same SSH key for each machine
config.ssh.insert_key = false
config.vm.box_check_update = false
config.vm.define "repo" do |repo|
  repo.vm.box = "rdbreak/pracrepo"
#  repo.vm.hostname = "repo.test.example.com"
  repo.vm.network "private_network", ip: "192.168.55.19"
  repo.vm.provider "virtualbox" do |repo|
    repo.memory = "1024"
  end
end
config.vm.define "system1" do |system1|
  system1.vm.box = "rdbreak/pracenvs"
#  system1.vm.hostname = "system1.test.example.com"
  system1.vm.network "private_network", ip: "192.168.55.21"
  system1.vm.network "private_network", ip: "192.168.55.110"
  system1.vm.network "private_network", ip: "192.168.55.111"
  system1.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config; sudo systemctl restart sshd;", run: "always"
  system1.vm.provider "virtualbox" do |system1|
    system1.memory = "512"

    if not File.exist?(file_to_disk1)
      system1.customize ['createhd', '--filename', file_to_disk1, '--variant', 'Fixed', '--size', 10 * 1024]
    end
    system1.customize ['storagectl', :id, '--name', 'SATA Controller', '--add', 'sata', '--portcount', 2]
    system1.customize ['storageattach', :id,  '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', file_to_disk1]
  end
    system1.vm.provision "shell", inline: <<-SHELL
    yes| sudo mkfs.ext4 /dev/sdb
    SHELL
  end
config.vm.define "system2" do |system2|
  system2.vm.box = "rdbreak/pracenvs"
#  system2.vm.hostname = "system2.test.example.com"
  system2.vm.network "private_network", ip: "192.168.55.22"
  system2.vm.network "private_network", ip: "192.168.55.112"
  system2.vm.network "private_network", ip: "192.168.55.113"
  system2.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config; sudo systemctl restart sshd;", run: "always"
  system2.vm.provider "virtualbox" do |system2|
    system2.memory = "512"

    if not File.exist?(file_to_disk2)
      system2.customize ['createhd', '--filename', file_to_disk2, '--variant', 'Fixed', '--size', 10 * 1024]
    end
    system2.customize ['storagectl', :id, '--name', 'SATA Controller', '--add', 'sata', '--portcount', 1]
    system2.customize ['storageattach', :id,  '--storagectl', 'SATA Controller', '--port', 2, '--device', 0, '--type', 'hdd', '--medium', file_to_disk2]
  end
  
    system2.vm.provision "shell", inline: <<-SHELL
    yes| sudo mkfs.ext4 /dev/sdb
    SHELL
end

config.vm.define "ipa" do |ipa|
  ipa.vm.box = "centos/7"
  ipa.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config; sudo systemctl restart sshd;", run: "always"
  ipa.vm.provision :shell, :inline => "yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm -y; sudo yum install -y nss sshpass libselinux-python python-pip python-devel httpd sshpass vsftpd createrepo pki-ca", run: "always"
  ipa.vm.provision :shell, :inline => "python -m pip install -U pip ; python -m pip install pexpect; python -m pip install ansible", run: "always"
  #  ipa.vm.hostname = "ipa.test.example.com"
  ipa.vm.network "private_network", ip: "192.168.55.20"
  ipa.vm.provider :virtualbox do |ipa|
    ipa.customize ['modifyvm', :id,'--memory', '2048']
  end
  ipa.vm.synced_folder ".", "/vagrant"
  ipa.vm.provision :ansible_local do |ansible|
    ansible.playbook = "/vagrant/playbooks/master.yml"
    ansible.install = false
    ansible.compatibility_mode = "2.0"
    ansible.inventory_path = "/vagrant/inventory"
    ansible.config_file = "/vagrant/ansible.cfg"
    ansible.limit = "all"
   end
  end
end
