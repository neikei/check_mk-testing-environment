# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

# Check minimum Vagrant version
Vagrant.require_version ">= 2.0.1"

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "debian/buster64"

  config.vm.provider "parallels"
  config.vm.provider "virtualbox"

  if !Vagrant.has_plugin?('vagrant-hostmanager')
    puts "The vagrant-hostmanager plugin is required. Please install it with \"vagrant plugin install vagrant-hostmanager\""
    exit
  end

  # TODO: Review after this issue is fixed https://github.com/dotless-de/vagrant-vbguest/issues/351
  if Vagrant.has_plugin?('vagrant-vbguest')
    config.vbguest.auto_update = false
  end

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.customize ['modifyvm', :id, '--memory', 2048]
    vb.customize ["modifyvm", :id, "--cpus", 2]
    vb.customize ["modifyvm", :id, "--name", "checkmk-testing-environment"]
  end

  config.vm.provider "parallels" do |v|
    v.memory = 2048
    v.cpus = 2
  end

  # Configure the VM
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true
  config.hostmanager.aliases = 'checkmk.test'
  config.vm.hostname = 'checkmk-testing-environment'
  config.vm.network :private_network, ip: "192.168.56.155"

  # Run the provisioning
  ## Install Ansible
  config.vm.provision "shell", path: "ansible/tools/install_ansible_in_Vagrantbox.sh"

  ## Install and configure software
  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "ansible/playbook.yml"
    ansible.become = true
    ansible.verbose = ""
  end

  # Post-up message
  config.vm.post_up_message = "See https://github.com/neikei/check_mk-testing-environment for help and bug reports."

end
