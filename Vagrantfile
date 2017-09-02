# -*- mode: ruby -*-
# vi: set ft=ruby :

domain          = "test"
setup_complete  = false

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"

  {
    # 'solr'  => '10.11.12.103',
    # 'db'    => '10.11.12.102',
    'immport'   => '10.33.33.101'
  }.each do |short_name, ip|
    config.vm.define short_name do |host|
      host.vm.network 'private_network', ip: ip
      # NOTE: currently set to alternate ports to not conflict with GalaxyKickStart
      host.vm.network "forwarded_port", guest: 80, host: 8081
      host.vm.network "forwarded_port", guest: 21, host: 2122
      host.vm.hostname = "#{short_name}.#{domain}"
      # presumes installation of https://github.com/cogitatio/vagrant-hostsupdater on host
      host.hostsupdater.aliases = ["#{short_name}"]
      # avoiding "Authentication failure" issue
      host.ssh.insert_key = false
      host.vm.synced_folder ".", "/vagrant", disabled: true

      host.vm.provider "virtualbox" do |vb|
        vb.name = "#{short_name}.#{domain}"
        vb.memory = 4096
        vb.linked_clone = true
      end

      if short_name == "immport" # last in the list
        setup_complete = true
      end

      if setup_complete
        host.vm.provision "ansible" do |ansible|
          ansible.galaxy_role_file = "requirements.yml"
          ansible.inventory_path = "inventory/vagrant"
          ansible.playbook = "setup.yml"
          ansible.limit = "all"
          ansible.verbose = "v"
        end
      end
    end
  end
end
