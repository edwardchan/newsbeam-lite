VAGRANTFILE_API_VERSION = '2'

# PROJECT VARIABLES
project_name = "laravelreact"
ip_address = "172.22.22.92"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Configure The Box
  config.vm.box = 'parallels/ubuntu-14.04'
  config.vm.hostname = 'homestead'

  # Don't Replace The Default Key https://github.com/mitchellh/vagrant/pull/4707
  config.ssh.insert_key = false

  config.vm.synced_folder "./", "/var/www/html/" + project_name + "/", type: "nfs"

  config.vm.provider :parallels do |v|
    v.cpus = 1
    v.memory = 2048
    v.update_guest_tools = true
  end
  
  # Use hostonly network with a static IP Address and enable
  # hostmanager so we can have a custom domain for the server
  # by modifying the host machines hosts file
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.vm.define project_name do |node|
    node.vm.hostname = project_name + ".local"
    node.vm.network :private_network, ip: ip_address
    node.hostmanager.aliases = [ "www." + project_name + ".local" ]
  end
  config.vm.provision :hostmanager


  # Configure Port Forwarding
  config.vm.network 'forwarded_port', guest: 80, host: 8000
  config.vm.network 'forwarded_port', guest: 3306, host: 33060
  config.vm.network 'forwarded_port', guest: 5432, host: 54320
  config.vm.network 'forwarded_port', guest: 35729, host: 35729

  # Disable the default /vagrant share
  # config.vm.synced_folder './', '/vagrant', disabled: true


  # Setup provisioning with an ansible playbook
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "provisioning/playbook.yml"
    ansible.groups = {
      "web" => [project_name],
      "database" => [project_name],
      "memcache" => [project_name]
    }
    ansible.extra_vars = {
      "project_name" => project_name
    }
  end
end