Vagrant.require_version ">= 1.6.0"
VAGRANTFILE_API_VERSION = "2"

# Require YAML module
require 'yaml'

# Read YAML file with box details
servers = YAML.load_file('servers.yml')


# Create boxes
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Iterate through entries in YAML file
  servers.each do |servers|
    # config.vm.synced_folder = servers["name"]+"/", "/home/vagrant"
    config.vm.define servers["name"] do |srv|
      if servers["box_url"] != nil
        srv.vm.box_url = servers["box_url"]
      else
        if servers["box_version"] and
          srv.vm.box_version = servers["box_version"]
          srv.vm.box_check_update = false
        end
      end
      servers["port_forwarding"].each do |pf|
        srv.vm.network "forwarded_port", guest: pf["guest_port"], host:pf["host_port"]
      end

      srv.vm.box = servers["box"]
      srv.vm.hostname = servers["name"]
      srv.vm.synced_folder servers["name"], "/home/vagrant", create: true

      srv.vm.network "private_network", ip: servers["ip"]
      # Virtaulbox specific configuration
      srv.vm.provider :virtualbox do |vb|
        vb.name = servers["name"]
        vb.memory = servers["ram"]
      end

      srv.vm.provision "ansible_local" do |ansible|
        ansible.playbook = "install-docker.yml"
      end

      # # Ansible provisioner
      # srv.vm.provision :ansible do |ansible|
      #   ansible.limit = servers[:name]
      #   ansible.playbook = "./playbooks/default.yml"
      #   ansible.inventory_path = "./inventories/inventory"
      #   #ansible.sudo = "true"
      #   #ansible.sudo_user = "root"
      #   ansible.host_key_checking = "false"
      #   ansible.verbose = "#{ansible_verbosity}"
      #   ansible.extra_vars = {
      #     some_var: "some_value",
      #     dict: {
      #       kay1: "val1",
      #       kay2: "val2"
      #     }
      #   }
      # end
    end
  end
end
