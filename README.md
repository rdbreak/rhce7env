# Vagrant/Ansible for RHCE 7 Study/Test Environment.

## Required software before setting up:
- Ansible - (`yum install ansible` or `brew install ansible`)
- Python - (`yum install python`or `brew install python`)
- [Vagrant](https://www.vagrantup.com/downloads.html) - (`brew cask install vagrant`)
- [Virtualbox](https://www.virtualbox.org/wiki/Downloads) (`brew cask install VirtualBox`)
### Install at once with the command below:
`brew install ansible && brew install python && brew cask install vagrant && brew cask install VirtualBox`

### It includes three systems:
- ipa.example.com
- system1.example.com
- system2.example.com

### Network Details:
###### system1
192.168.55.21
###### system2
192.168.55.22

### Local users
- Username = vagrant
- Password = vagrant
- For root = use `sudo` or `sudo su`
### LDAP users
- Username = dave, lisa
- Password = password

Keytab is provided on http://ipa.example.com/system.keytab

## Set Up Instructions
1. Create a seperate `/bin` directory and `cd` to it. 
2. Clone the environment repo to it with `git clone https://github.com/rdbreak/rhce7env.git`
3. Run `vagrant up --provider virtualbox` to deploy the environment _(You must be in the directory you cloned the repo to in order to run vagrant commands.)_

_NOTE - You can use the VirtualBox console to interact with the VMs or through a terminal. If you need to reset the root password, you would need to use the console though._

The first time you run the vagrant up command, it will download the OS images for later use. In other words, it will take longest the first time around but will be faster when it is deployed again. You can run `vagrant destroy -f` to destroy your environment at anytime. **This will erase everything**. This environment is meant to be reuseable, If you run the `vagrant up --provider virtualbox` command after destroying the environment, the OS image will already be downloaded and environment will deploy faster. Once the setup is complete, the ipa server and client for realm EXAMPLE.COM will already be setup and paired. Deployment should take around 15 minutes depending on your computer. You shouldn't need to access the IPA server during your practice exams. Everything should be provided that you would normally need during an actual exam. Hope this helps you in your studies!