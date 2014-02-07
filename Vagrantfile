# -*- mode: ruby -*-
# vi: set ft=ruby :

# Set your bridged interface here
BRIDGED_INTERFACE = "en3: Thunderbolt Ethernet"
#BRIDGED_INTERFACE = "en0: Wi-Fi (AirPort)"

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

#Vagrant::Config.run do |config|
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  #Avoid the "stdin: is not a tty" errors.  
  config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"

  config.vm.define :server do |server_config|
    # Every Vagrant virtual environment requires a box to build off of.
    server_config.vm.box = "ubuntu-12.04"

    server_config.vm.provider :virtualbox do |vb|
      # Add a little more memory to allow for internal hosts
      vb.customize ["modifyvm", :id, "--memory", "3072"]
    end

    server_config.vm.network :public_network, ip: "192.168.33.11", :bridge => BRIDGED_INTERFACE
    server_config.vm.host_name = "vagrant-server.vm"

    # server_config.vm.network :bridged

    server_config.vm.network "forwarded_port", guest: 8080, host: 8070

    server_config.vm.network "forwarded_port", guest: 8081, host: 8081

    server_config.vm.network "forwarded_port", guest: 8082, host: 8082

    server_config.vm.network "forwarded_port", guest: 8083, host: 8083
    # Nginx Tasseo
    server_config.vm.network "forwarded_port", guest: 8084, host: 8084
    # Tasseo
    server_config.vm.network "forwarded_port", guest: 5000, host: 8085
    # Statsd telnet interface (see https://github.com/etsy/statsd)
    # telnet localhost 8086
    # use commands 'stats' 'counters' or 'timers'
    server_config.vm.network "forwarded_port", guest: 8126, host: 8086
    # JMX
    server_config.vm.network "forwarded_port", guest: 1105, host: 8087
    # ES
    server_config.vm.network "forwarded_port", guest: 9200, host: 8088

    # RabbitMQ
    server_config.vm.network "forwarded_port", guest: 55672, host: 8089
    # Sensu-dashboard
    server_config.vm.network "forwarded_port", guest: 8090, host: 8090
    # Sensu-api
    server_config.vm.network "forwarded_port", guest: 8091, host: 8091

    # We need to increase timeout due to the forward ports default = 10
    #server_config.ssh.timeout = 1000
    #server_config.ssh.max_tries = 3000

    server_config.vm.provision :shell, :path => "definitions/ubuntu-12.04/chef-client.sh"

    # server_config.vm.share_folder "v-data", "/vagrant_data", "../data"

    server_config.vm.provision :chef_solo do |chef|
      chef.cookbooks_path = [ "cookbooks" , "monigusto/cookbooks","site-cookbooks"]
      chef.roles_path = "roles"
      chef.data_bags_path = "data_bags"
      chef.add_role "monigusto_server"
    end
  end

  config.vm.define :client do |client_config|
    # Every Vagrant virtual environment requires a box to build off of.
    client_config.vm.box = "ubuntu-12.04"

    client_config.vm.provider :virtualbox do |vb|
      # Add a little more memory to allow for internal hosts
      vb.customize ["modifyvm", :id, "--memory", 1024]
    end

    client_config.vm.network :public_network, ip: "192.168.33.10", :bridge => BRIDGED_INTERFACE
    client_config.vm.host_name = "vagrant-client.vm"

    # client_config.vm.network :bridged

    # client_config.vm.forwarded_port 80, 8080

    # client_config.vm.share_folder "v-data", "/vagrant_data", "../data"
    client_config.vm.provision :shell, :path => "definitions/ubuntu-12.04/chef-client.sh"

    client_config.vm.provision :chef_solo do |chef|
      chef.cookbooks_path = [ "cookbooks" , "monigusto/cookbooks","site-cookbooks"]
      chef.roles_path = "roles"
      chef.log_level = :debug
      chef.data_bags_path = "data_bags"
      chef.add_role "monigusto_client"
    end
  end

end
