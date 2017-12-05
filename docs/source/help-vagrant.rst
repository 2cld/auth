Vagrantfile Help
================
This help section contains information on the creation and debugging of the Vagrantfile.
The goal of the vagrantfile is a minimum setup of multiple vm then passing off the machine information so ansible can futher provision each vm.

===========
Quick Start
===========
Below is quickstart vagrant steps::

  catmini:~ cat$ git clone https://github.com/2cld/auth.git
  catmini:~ cat$ cd auth
  catmini:~ cat$ vagrant up
  ... wait ...
  catmini:~ cat$ vagrant ssh 
  catmini:~ cat$


==========
References
==========
 + https://github.com/2cld/auth/blob/master/Vagrantfile
 + https://www.vagrantup.com/docs/vagrantfile/
 + https://github.com/floragunncom/kerberos_ldap_environment/blob/master/Vagrantfile
 + https://www.vagrantup.com/docs/other/debugging.html
 + https://stackoverflow.com/questions/10864372/how-to-ssh-to-vagrant-without-actually-running-vagrant-ssh

=====================
Vagrantfile Explained
=====================
In the Vagrantfile https://github.com/2cld/auth/blob/master/Vagrantfile are comments with the following format::

 # Help Reference: <text for subsection below>

-----------------
ansible inventory
-----------------
In the Vagrantfile https://github.com/2cld/auth/blob/master/Vagrantfile search comments for the following::

 # Help Reference: ansible inventory

The below chunck of Vagrantfile checks / sets where the ansible inventory should live in the repo::

    # if $inventory is not set, try to use example
    $inventory = File.join(File.dirname(__FILE__), "inventory") if ! $inventory

The below chunck of Vagrantfile taps into vagrant generated structure to link in the inventory::

    if ! File.exist?(File.join(File.dirname($inventory), "hosts"))
        $vagrant_ansible = File.join(File.dirname(__FILE__), ".vagrant",
                            "provisioners", "ansible")
        FileUtils.mkdir_p($vagrant_ansible) if ! File.exist?($vagrant_ansible)
        if ! File.exist?(File.join($vagrant_ansible,"inventory"))
            FileUtils.ln_s($inventory, $vagrant_ansible)
        end
    end

----------
box config
----------
In the Vagrantfile https://github.com/2cld/auth/blob/master/Vagrantfile search comments for the following::

 # Help Reference: box config

The below chunck of Vagrantfile sets url source of the box image to use::

  config.vm.box = $box
  if SUPPORTED_OS[$os].has_key? :box_url
    config.vm.box_url = SUPPORTED_OS[$os][:box_url]
  end

--------
ssh keys
--------
In the Vagrantfile https://github.com/2cld/auth/blob/master/Vagrantfile search comments for the following::

 # Help Reference: ssh keys

The below chunck of Vagrantfile sets ssh user::

  # always use Vagrants insecure key
  config.ssh.insert_key = false
  config.ssh.username = SUPPORTED_OS[$os][:user]

ssh vagrant links
+++++++++++++++++

See "ssh keys" in Vagrantfile comments

 + https://stackoverflow.com/questions/10864372/how-to-ssh-to-vagrant-without-actually-running-vagrant-ssh

------------------
vm instance create
------------------
In the Vagrantfile https://github.com/2cld/auth/blob/master/Vagrantfile search comments for the following::

 # Help Reference: vm instance create

The below chunck of Vagrantfile BEGINS the loop that creates vm instances and the instance hostname::

   (1..$num_instances).each do |i|
    config.vm.define vm_name = "%s-%02d" % [$instance_name_prefix, i] do |config|
      # Help Reference: vm hostname
      config.vm.hostname = vm_name
    ... snip ...
    end
   ... snip ...
   end

--------
vm proxy
--------
In the Vagrantfile https://github.com/2cld/auth/blob/master/Vagrantfile search comments for the following::

 # Help Reference: vm proxy

The below chunck of Vagrantfile I'm not sure of and need to figure out::

      if Vagrant.has_plugin?("vagrant-proxyconf")
        config.proxy.http     = ENV['HTTP_PROXY'] || ENV['http_proxy'] || ""
        config.proxy.https    = ENV['HTTPS_PROXY'] || ENV['https_proxy'] ||  ""
        config.proxy.no_proxy = $no_proxy
      end


---------------
vm port forward
---------------
In the Vagrantfile https://github.com/2cld/auth/blob/master/Vagrantfile search comments for the following::

 # Help Reference: vm port forward

The below chunck of Vagrantfile forwards ports to host of the instance::

      $forwarded_ports.each do |guest, host|
        config.vm.network "forwarded_port", guest: guest, host: host, auto_correct: true
      end


-----------------
vm shared folders
-----------------
In the Vagrantfile https://github.com/2cld/auth/blob/master/Vagrantfile search comments for the following::

 # Help Reference: vm shared folders

The below chunck of Vagrantfile sets standard vagrant shared folders::

      $shared_folders.each do |src, dst|
        config.vm.synced_folder src, dst
      end


-----------------
vm ip and network
-----------------
In the Vagrantfile https://github.com/2cld/auth/blob/master/Vagrantfile search comments for the following::

 # Help Reference: vm ip and network

The below chunck of Vagrantfile sets IP of instance::

      ip = "#{$subnet}.#{i+100}"
      host_vars[vm_name] = {
        "ip": ip,
        "bootstrap_os": SUPPORTED_OS[$os][:bootstrap_os],
        "local_release_dir" => $local_release_dir,
        "download_run_once": "False",
      }
      
      config.vm.network :private_network, ip: ip


-----------------------
run ansible provisioner
-----------------------
In the Vagrantfile https://github.com/2cld/auth/blob/master/Vagrantfile search comments for the following::

 # Help Reference: run ansible provisioner

The below chunck of Vagrantfile checks / sets where the ansible inventory should live in the repo::

      # Only execute once the Ansible provisioner, when all the machines are up and ready.
      if i == $num_instances
        config.vm.provision "ansible" do |ansible|
          ansible.playbook = "cluster.yml"
          if File.exist?(File.join(File.dirname($inventory), "hosts"))
            ansible.inventory_path = $inventory
          end
          ansible.become = true
          ansible.limit = "all"
          ansible.host_key_checking = false
          ansible.raw_arguments = ["--forks=#{$num_instances}", "--flush-cache"]
          ansible.host_vars = host_vars
          #ansible.tags = ['download']
          ansible.groups = {
            "tocld-psmaster" => ["#{$instance_name_prefix}-0[1:#{$tocld_master_instances}]"],
            "tocld-node" => ["#{$instance_name_prefix}-0[1:#{$tocld_node_instances}]"],
            "tocld-cluster:children" => ["tocld-psmaster", "tocld-node"],
          }
        end
      end

-------
tbd end
-------