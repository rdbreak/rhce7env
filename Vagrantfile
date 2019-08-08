VAGRANTFILE_API_VERSION = "2"
VAGRANT_DISABLE_VBOXSYMLINKCREATE = "1"
file_to_disk1 = './disk-0-1.vdi'
file_to_disk2 = './disk-0-2.vdi'
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
# Use same SSH key for each machine
config.ssh.insert_key = false
config.vm.box_check_update = false
config.vm.define "repo" do |machine|
  machine.vm.box = "centos/7"
  machine.vm.hostname = "repo.test.example.com"
  machine.vm.network "private_network", ip: "192.168.55.19"
  machine.vm.network "private_network", ip: "192.168.55.101"
  machine.vm.network "private_network", ip: "192.168.55.102"
  machine.vm.provision :shell, :inline => " sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config;  systemctl restart sshd; echo vagrant |  passwd vagrant --stdin;", run: "always"
  machine.vm.provision :shell, :inline => "sudo yum install -y createrepo wget httpd sshpass| grep -v 'warning\|Error'; sudo cd /etc/pki/rpm-gpg;  sudo wget http://yum.theforeman.org/releases/1.8/RPM-GPG-KEY-foreman; sudo rpm --import RPM-GPG-KEY-foreman; sudo rpm -qa gpg* ; sudo rpm -qi gpg-pubkey-225c9b71-54fda121;", run: "always"
  machine.vm.provision :shell, :inline => "sudo curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py ; python get-pip.py ;", run: "always"
  machine.vm.provision :shell, :inline => "sudo mkdir -p /var/www/html/rpms;", run: "always"
#  machine.vm.provision :shell, :inline => "for i in \"Development Tools\" \"Server with GUI\" \"File and Print Server\" \"Web Server\" httpd-manual selinux\* sssd\* bash-completion ipa-client man-pages httpd; do yum group install \"$i\" --downloadonly --downloaddir=/var/www/html/rpms;done;", run: "always"
#  machine.vm.provision :shell, :inline => "createrepo /var/www/html/rpms", run: "always"
  machine.vm.provision :shell, :inline => " sudo systemctl stop packagekit; sudo systemctl mask packagekit", run: "always"
#  machine.vm.provision :shell, :inline => " mkdir -p /var/www/html/rpms;", run: "always"
  machine.vm.provider "virtualbox" do |machine|
    machine.memory = "1024"
  end
  machine.vm.provision :shell, :inline => "sudo yum group install -y \"Development Tools\" ; sudo yum install -y python-devel curl ;sudo curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py ; python get-pip.py ; sudo pip install -U pip ; sudo pip install pexpect;", run: "always"
  machine.vm.provision :shell, :inline => "pip install ansible", run: "always"
#  machine.vm.provision :ansible_local do |ansible|
#    ansible.playbook       = "playbooks/repo.yml"
#    ansible.verbose        = true
#  end
end
config.vm.define "system1" do |machine|
  machine.vm.box = "puppetlabs/centos-7.0-64-nocm"
  machine.vm.hostname = "system1.test.example.com"
  machine.vm.network "private_network", ip: "192.168.55.21"
  machine.vm.network "private_network", ip: "192.168.55.110"
  machine.vm.network "private_network", ip: "192.168.55.111"
  machine.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config; sudo systemctl restart sshd; echo vagrant | sudo passwd vagrant --stdin", run: "always"
  machine.vm.provider "virtualbox" do |machine|
    machine.memory = "1024"

    if not File.exist?(file_to_disk1)
      machine.customize ['createhd', '--filename', file_to_disk1, '--variant', 'Fixed', '--size', 10 * 1024]
    end
    machine.customize ['storagectl', :id, '--name', 'SATA Controller', '--add', 'sata', '--portcount', 2]
    machine.customize ['storageattach', :id,  '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', file_to_disk1]
  end
  
    machine.vm.provision "shell", inline: <<-SHELL
    yes| sudo mkfs.ext4 /dev/sdb
    SHELL
    machine.vm.provision :shell, :inline => "sudo yum group install -y \"Development Tools\" ; sudo yum install -y python-devel curl ;sudo curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py ; python get-pip.py ; sudo pip install -U pip ; sudo pip install pexpect;", run: "always"
    machine.vm.provision :shell, :inline => "sudo yum install -y httpd sshpass", run: "always"
    machine.vm.provision :shell, :inline => "pip install ansible", run: "always"
#    machine.vm.provision :ansible_local do |ansible|
#      ansible.playbook       = "playbooks/system1.yml"
#      ansible.verbose        = true
#    end
end
config.vm.define "system2" do |machine|
  machine.vm.box = "puppetlabs/centos-7.0-64-nocm"
  machine.vm.hostname = "system2.test.example.com"
  machine.vm.network "private_network", ip: "192.168.55.22"
  machine.vm.network "private_network", ip: "192.168.55.112"
  machine.vm.network "private_network", ip: "192.168.55.113"
  machine.vm.provision :shell, :inline => "sudo yum install -y httpd sshpass", run: "always"
  machine.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config; sudo systemctl restart sshd; echo vagrant | sudo passwd vagrant --stdin", run: "always"
  machine.vm.provision :shell, :inline => "sudo yum group install -y \"Development Tools\" ; sudo yum install -y python-devel curl ;sudo curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py ; python get-pip.py ; sudo pip install -U pip ; sudo pip install pexpect;", run: "always"
  machine.vm.provision :shell, :inline => "pip install ansible", run: "always"
  machine.vm.synced_folder ".", "/vagrant"
  machine.vm.provider "virtualbox" do |machine|
    machine.memory = "1024"

    if not File.exist?(file_to_disk2)
      machine.customize ['createhd', '--filename', file_to_disk2, '--variant', 'Fixed', '--size', 10 * 1024]
    end
    machine.customize ['storagectl', :id, '--name', 'SATA Controller', '--add', 'sata', '--portcount', 1]
    machine.customize ['storageattach', :id,  '--storagectl', 'SATA Controller', '--port', 2, '--device', 0, '--type', 'hdd', '--medium', file_to_disk2]
  end
  
    machine.vm.provision "shell", inline: <<-SHELL
    yes| sudo mkfs.ext4 /dev/sdb
    SHELL
  end


config.vm.define "ipa" do |machine|
  machine.vm.box = "centos/7"
  machine.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config; sudo systemctl restart sshd;", run: "always"
  machine.vm.provision :shell, :inline => "sudo yum install -y httpd sshpass", run: "always"
  machine.vm.hostname = "ipa.test.example.com"
  machine.vm.network "private_network", ip: "192.168.55.20"
  machine.vm.provider :virtualbox do |machine|
    machine.customize ['modifyvm', :id,'--memory', '2048']
  end

machine.vm.provision :shell, :inline => "sudo yum group install -y \"Development Tools\" ; sudo yum install -y python-devel curl ;sudo curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py ; python get-pip.py ; sudo pip install -U pip ; sudo pip install pexpect;", run: "always"
machine.vm.provision :shell, :inline => "pip install ansible", run: "always"
machine.vm.provision :ansible_local do |ansible|
  ansible.playbook       = "playbooks/master.yml"
#  ansible.verbose        = true
  ansible.inventory_path = "/vagrant/inventory"
  ansible.limit = "all"
end
end
end