# RHCSA 7 Automated Practice Deployment
_Powered by Ansible and Vagrant_ 

## Installation options below:
## macOS
_Gatekeeper will block virtualbox from installing. All you have to do is go to Security & Privacy of System Preferences and click Allow under the General tab and rerun installation._
##### Install all at once with the command below:
`brew install ansible ; brew install python ; brew cask install vagrant ; brew cask install VirtualBox ; brew cask install virtualbox-extension-pack`
##### Alternatively, you can install everything individually below.
- [Latest Version of Vagrant](https://www.vagrantup.com/downloads.html) - (`brew cask install vagrant`)
    - Vagrant Plugin - `vagrant plugin install vagrant-guest-ansible`
- [Latest Version of Virtualbox](https://www.virtualbox.org/wiki/Downloads) (`brew cask install VirtualBox`)
    - Virtual Box Extension Pack (`brew cask install virtualbox-extension-pack`)

## CentOS/RHEL 7 - Install all at once by Copy/Pasting the below command into your terminal as root.
_NOTE - If it's been awhile since you've run yum update, do that first. Reboot if the kernel was updated._
```
systemctl stop packagekit; yum install -y epel-release && yum install -y git binutils gcc make patch libgomp glibc-headers glibc-devel kernel-headers kernel-devel dkms libvirt libvirt-devel ruby-devel libxslt-devel libxml2-devel libguestfs-tools-c ; mkdir ~/Vagrant ; cd ~/Vagrant ; curl -o  vagrant_2.2.5_x86_64.rpm https://releases.hashicorp.com/vagrant/2.2.5/vagrant_2.2.5_x86_64.rpm && yum install -y vagrant_2.2.5_x86_64.rpm && vagrant plugin install vagrant-guest-ansible ; vagrant plugin install vagrant-libvirt ; wget -O /etc/yum.repos.d/virtualbox.repo wget http://download.virtualbox.org/virtualbox/rpm/rhel/virtualbox.repo ; yum install -y VirtualBox-6.0 && systemctl start packagekit
```
##### Also, install the Virtualbox extension pack below
- [Virtual Box Extension Pack](https://www.virtualbox.org/wiki/Downloads)

## Debian
_NOTE - If it's been awhile since you've run apt update, do that first. Reboot if the kernel was updated._

##### Install all at once by Copy/Pasting the below command into your terminal as root.
```
sudo snap install ruby ; sudo apt install ruby-bundler git -y; wget -c https://releases.hashicorp.com/vagrant/2.2.5/vagrant_2.2.5_x86_64.deb ; sudo dpkg -i vagrant_2.2.5_x86_64.deb ; wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add - ; wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add - ; sudo add-apt-repository "deb http://download.virtualbox.org/virtualbox/debian bionic contrib"; sudo apt update; sudo apt install -y virtualbox-6.0 && vagrant plugin install vagrant-guest-ansible
```
##### Also, install the Virtualbox extension pack below
- [Virtual Box Extension Pack](https://www.virtualbox.org/wiki/Downloads)

##### Once the above software is installed. Do the following if you're running the environment on Mac/Linux:
1. Create a separate `~/bin` directory and `cd` to it.  (The directory doesn't have to be ~/bin, it can be anything you want.)
2. Clone the environment repo to it with `git clone https://github.com/rdbreak/rhce7env.git`
3. Change to the `rhce7env` directory that is now in your `~/bin` directory.
4. Run `vagrant up` to deploy the environment 

## Windows 
- [Latest Version of Vagrant](https://www.vagrantup.com/downloads.html)
    - Vagrant Plugin - `vagrant plugin install vagrant-guest_ansible`
- [Latest Version of Virtualbox and Virtual Box Extension Pack](https://www.virtualbox.org/wiki/Downloads)

##### Once the above software is installed. Do the following if you're running the environment on Windows:
1. Create a separate `~/bin` directory and `cd` to it.  (The directory doesn't have to be ~/bin, it can be anything you want.)
2. Use your browser of choice and navigate to https://github.com/rdbreak/rhce7env, press the green “Clone or download” button then the “Download ZIP” button.
3. Once downloaded, unzip the file and move it to the directory you created earlier, `~/bin` in the above example.
3. Open CMD prompt as Administrator and cd to the `~/bin/rhce7env` directory then run `vagrant up` to deploy the environment

**Also, don't be spooked by any scary red font during the setup process. There are known issues that won't have a negative affect on the environment.**

_Now the deployment should be up and running!_

## (Optional) Install Github Desktop to make pulling down changes easier
_NOTE this requires a free Github account_
1. Navigate to https://desktop.github.com/ and download Github Desktop.
2. Create or sign in to your account.
3. Clone or pull changes to respctive repo

## Other Useful Information:
You can also use the VirtualBox console to interact with the VMs or through a terminal. If you need to reset the root password, you would need to use the console. I'm constantly making upgrades to the environments, so every once and awhile run `git pull` in the repo directory to pull down changes. The first time you run the vagrant up command, it will download the OS images for later use. In other words, it will take longest the first time around but will be faster when it is deployed again. You can run `vagrant destroy -f` to destroy your environment at anytime. **This will erase everything**. This environment is meant to be reuseable, If you run the `vagrant up` command after destroying the environment, the OS image will already be downloaded and environment will deploy faster. Once the setup is complete, the ipa server and client for realm TEST.EXAMPLE.COM will already be setup and paired. Deployment should take around 15 minutes depending on your computer. You shouldn't need to access the IPA server or Repo server during your practice exams. You shouldn't need to access the IPA or REPO server during your practice exams. Everything should be provided that you would normally need during an actual exam. Hope this helps in your studies!

## Included systems:
- ipa.test.example.com
- repo.test.example.com
- system1.test.example.com

## System Details:
> repo
- IP - 192.168.55.19
- Gateway - 192.168.55.1
- DNS - 192.168.55.5
> ipa
- 192.168.55.20
- Gateway - 192.168.55.1
- DNS - 192.168.55.5
> system1
- 192.168.55.21
- Gateway - 192.168.55.1
- DNS - 192.168.55.5
> system2
- 192.168.55.22
- Gateway - 192.168.55.1
- DNS - 192.168.55.5

There is a repo available to use from `http://repo.test.example.com/rpms`

## Accessing the systems
Remember to add the IP addresses to your local host file if you want to connect to the guest systems with the hostname.
Username - vagrant
Password - vagrant

- For root - use `sudo` or `sudo su`
Access example - `ssh vagrant@192.168.55.21` or `vagrant ssh system`

## LDAP users
- Username = dave, lisa
- Password = password

## Help
If you're having problems with the environment, please submit an issue by going to the `ISSUES` tab at the top. If you have more questions, looking for practice exams to use against this environment, or just looking for a fantastic Red Hat community to join, please navigate to #practiceexam in the [Red Hat Certs Slack Workspace](https://join.slack.com/t/redhat-certs/shared_invite/enQtNjI4Mjk1OTA4NDk4LTBiMWQ1OGM5MmJhZjhlNGZiNjMxYmViMGI2OTdjMDY4NjZkYTliYTE4M2IwYzFkYTJlMThjNmFlNDZmOTIyZTQ).
