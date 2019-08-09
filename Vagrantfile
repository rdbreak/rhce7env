VAGRANTFILE_API_VERSION = "2"
VAGRANT_DISABLE_VBOXSYMLINKCREATE = "1"
file_to_disk1 = './disk-0-1.vdi'
file_to_disk2 = './disk-0-2.vdi'
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
# Use same SSH key for each machine
config.ssh.insert_key = false
config.vm.box_check_update = false
config.vm.define "repo" do |repo|
  repo.vm.box = "centos/7"
#  repo.vm.hostname = "repo.test.example.com"
  repo.vm.network "private_network", ip: "192.168.55.19"
  repo.vm.network "private_network", ip: "192.168.55.101"
  repo.vm.network "private_network", ip: "192.168.55.102"
  repo.vm.provision :shell, :inline => " sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config;  systemctl restart sshd; echo vagrant |  passwd vagrant --stdin;", run: "always"
  repo.vm.provision :shell, :inline => "sudo yum install -y createrepo wget httpd sshpass| grep -v 'warning\|Error'; sudo cd /etc/pki/rpm-gpg;  sudo wget http://yum.theforeman.org/releases/1.8/RPM-GPG-KEY-foreman; sudo rpm --import RPM-GPG-KEY-foreman; sudo rpm -qa gpg* ; sudo rpm -qi gpg-pubkey-225c9b71-54fda121;", run: "always"
  repo.vm.provision :shell, :inline => "sudo curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py ; python get-pip.py ;", run: "always"
  repo.vm.provision :shell, :inline => "sudo mkdir -p /var/www/html/rpms;", run: "always"
  repo.vm.provision :shell, :inline => " sudo systemctl stop packagekit; sudo systemctl mask packagekit", run: "always"
  repo.vm.provider "virtualbox" do |repo|
    repo.memory = "1024"
  end
  repo.vm.provision :shell, :inline => "sudo yum group install -y \"Development Tools\" ; sudo yum install -y python-devel curl ;sudo curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py ; python get-pip.py ; sudo pip install -U pip ; sudo pip install pexpect;", run: "always"
  repo.vm.provision :shell, :inline => "pip install ansible", run: "always"
  repo.vm.synced_folder ".", "/vagrant"
#  repo.vm.provision :ansible_local do |ansible|
#   ansible.playbook = "/vagrant/playbooks/repo.yml"
#   ansible.install = false
#   ansible.compatibility_mode = "2.0"
#   ansible.config_file = "/vagrant/ansible.cfg"
#  end
end
config.vm.define "system1" do |system1|
  system1.vm.box = "puppetlabs/centos-7.0-64-nocm"
#  system1.vm.hostname = "system1.test.example.com"
  system1.vm.network "private_network", ip: "192.168.55.21"
  system1.vm.network "private_network", ip: "192.168.55.110"
  system1.vm.network "private_network", ip: "192.168.55.111"
  system1.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config; sudo systemctl restart sshd; echo vagrant | sudo passwd vagrant --stdin", run: "always"
  system1.vm.provision :shell, :inline => "sudo yum group install -y \"Development Tools\" ; sudo yum install -y python-devel curl ;sudo curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py ; python get-pip.py ; sudo pip install -U pip ; sudo pip install pexpect;", run: "always"
  system1.vm.provision :shell, :inline => "sudo yum install -y httpd sshpass", run: "always"
  system1.vm.provision :shell, :inline => "pip install ansible", run: "always"
  system1.vm.provider "virtualbox" do |system1|
    system1.memory = "1024"

    if not File.exist?(file_to_disk1)
      system1.customize ['createhd', '--filename', file_to_disk1, '--variant', 'Fixed', '--size', 10 * 1024]
    end
    system1.customize ['storagectl', :id, '--name', 'SATA Controller', '--add', 'sata', '--portcount', 2]
    system1.customize ['storageattach', :id,  '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', file_to_disk1]
  end
    system1.vm.provision "shell", inline: <<-SHELL
    yes| sudo mkfs.ext4 /dev/sdb
    SHELL
#    system1.vm.synced_folder ".", "/vagrant"
#    system1.vm.provision :ansible_local do |ansible|
#      ansible.playbook = "/vagrant/playbooks/system1.yml"
#      ansible.install = false
#    end
  end
config.vm.define "system2" do |system2|
  system2.vm.box = "puppetlabs/centos-7.0-64-nocm"
#  system2.vm.hostname = "system2.test.example.com"
  system2.vm.network "private_network", ip: "192.168.55.22"
  system2.vm.network "private_network", ip: "192.168.55.112"
  system2.vm.network "private_network", ip: "192.168.55.113"
  system2.vm.provision :shell, :inline => "sudo yum install -y httpd sshpass", run: "always"
  system2.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config; sudo systemctl restart sshd; echo vagrant | sudo passwd vagrant --stdin", run: "always"
  system2.vm.provision :shell, :inline => "sudo yum group install -y \"Development Tools\" ; sudo yum install -y python-devel curl ;sudo curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py ; python get-pip.py ; sudo pip install -U pip ; sudo pip install pexpect;", run: "always"
  system2.vm.provision :shell, :inline => "yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm -y; sudo yum install -y sshpass httpd vsftpd createrepo pki-ca", run: "always"
  system2.vm.provision :shell, :inline => "pip install ansible", run: "always"
  system2.vm.synced_folder ".", "/vagrant"
  system2.vm.provider "virtualbox" do |system2|
    system2.memory = "1024"

    if not File.exist?(file_to_disk2)
      system2.customize ['createhd', '--filename', file_to_disk2, '--variant', 'Fixed', '--size', 10 * 1024]
    end
    system2.customize ['storagectl', :id, '--name', 'SATA Controller', '--add', 'sata', '--portcount', 1]
    system2.customize ['storageattach', :id,  '--storagectl', 'SATA Controller', '--port', 2, '--device', 0, '--type', 'hdd', '--medium', file_to_disk2]
  end
  
    system2.vm.provision "shell", inline: <<-SHELL
    yes| sudo mkfs.ext4 /dev/sdb
    SHELL
#    system2.vm.synced_folder ".", "/vagrant"
#    system2.vm.provision :ansible_local do |ansible|
#     ansible.playbook = "/vagrant/playbooks/system2.yml"
#     ansible.install = false
#    end
end

config.vm.define "ipa" do |ipa|
  ipa.vm.box = "centos/7"
  ipa.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config; sudo systemctl restart sshd;", run: "always"
  ipa.vm.provision :shell, :inline => "yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm -y; sudo yum install -y sshpass httpd vsftpd createrepo pki-ca", run: "always"
  ipa.vm.provision :shell, :inline => "sudo yum install -y httpd sshpass", run: "always"
#  ipa.vm.hostname = "ipa.test.example.com"
  ipa.vm.network "private_network", ip: "192.168.55.20"
  ipa.vm.provider :virtualbox do |ipa|
    ipa.customize ['modifyvm', :id,'--memory', '2048']
  end

  ipa.vm.provision :shell, :inline => "sudo yum group install -y \"Development Tools\" ; sudo yum install -y python-devel curl ;sudo curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py ; python get-pip.py ; sudo pip install -U pip ; sudo pip install pexpect;", run: "always"
  ipa.vm.provision :shell, :inline => "pip install ansible", run: "always"
  ipa.vm.synced_folder ".", "/vagrant"
#  ipa.vm.provision :ansible_local do |ansible|
#    ansible.playbook = "/vagrant/playbooks/ipa.yml"
#    ansible.install = false
#    ansible.compatibility_mode = "2.0"
#   end
#   ipa.vm.provision :ansible_local do |ansible|
#    ansible.playbook = "/vagrant/playbooks/system1.yml"
#    ansible.install = false
#    ansible.compatibility_mode = "2.0"
#    ansible.inventory_path = "/vagrant/inventory"
#    ansible.config_file = "/vagrant/ansible.cfg"
#    ansible.limit = "system1"
#   end
#  ipa.vm.provision :ansible_local do |ansible|
#     ansible.playbook = "/vagrant/playbooks/system2.yml"
#     ansible.install = false
#     ansible.compatibility_mode = "2.0"
#     ansible.inventory_path = "/vagrant/inventory"
#     ansible.config_file = "/vagrant/ansible.cfg"
#     ansible.limit = "system2"
#    end
#  ipa.vm.provision :ansible_local do |ansible|
#    ansible.playbook = "/vagrant/playbooks/kerberos.yml"
#    ansible.install = false
#    ansible.compatibility_mode = "2.0"
#    ansible.inventory_path = "/vagrant/inventory"
#    ansible.config_file = "/vagrant/ansible.cfg"
#    ansible.limit = "ipa"
#    end
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
