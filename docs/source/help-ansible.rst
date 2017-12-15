Ansible Help
================
This help section contains information on the creation and debugging of the Ansible playbook cluster.yml_.
The goal of the vagrantfile is a minimum setup of multiple vm then passing off the machine information so ansible can futher provision each vm.

.. _cluster.yml: https://github.com/2cld/auth/blob/master/cluster.yml

#. Ansible - Automation via 'cluster.yml' playbook and 'inventory'

    1. Ansible Example Command Line

     Shown below is an ansible-playbook command line using vagrant inventory structure::

        catmini:auth cat$ /usr/local/bin/ansible-playbook --connection=ssh --timeout=30 --limit=all --inventory-file=/Users/cat/auth/.vagrant/provisioners/ansible/inventory --become --forks=2 --flush-cache cluster.yml

    2. Additional Ansible Help

     .. toctree::
        :maxdepth: 2

        auth-create-ansible
        auth-restore-ansible
         

===========
Quick Start
===========
Below is quickstart vagrant steps::

  catmini:~ cat$ git clone https://github.com/2cld/auth.git
  catmini:~ cat$ cd auth
  catmini:~ cat$ vagrant up
  ... wait ... vagrant provison will run cluster.yml
  catmini:~ cat$ vagrant provision
  ... will run ansible through vagrant and show detail of ansible run... 
  catmini:~ cat$


==========
References
==========
 + https://github.com/2cld/auth/blob/master/Vagrantfile
 + https://www.vagrantup.com/docs/vagrantfile/
 + https://github.com/floragunncom/kerberos_ldap_environment/blob/master/Vagrantfile
 + https://www.vagrantup.com/docs/other/debugging.html
 + https://stackoverflow.com/questions/10864372/how-to-ssh-to-vagrant-without-actually-running-vagrant-ssh
 + https://galaxy.ansible.com/bertvv/
 + https://github.com/bertvv/ansible-toolbox
 + https://github.com/bertvv/ansible-skeleton
 + https://github.com/bertvv/ansible-role-rh-base
 + https://youtu.be/qJ0VNO6z68M?t=99
 + https://galaxy.ansible.com/bertvv/rh-base/
 + https://github.com/mikrotik-ansible/mikrotik-firewall
 + https://galaxy.ansible.com/geerlingguy/
 + https://galaxy.ansible.com/bennojoy/
 + https://github.com/bertvv/ansible-role-bind

===========================================
ansible-tool-box_ (atb) Structure Explained
===========================================

 #. Clone ansible-skeleton_

    Clone the repo ansible-skeleton_ rename and cleanup.  This is what atb-init.sh_ does::

      catmini:~ cat$ git clone https://github.com/bertvv/ansible-skeleton.git test-project
      Cloning into 'test-project'...
      remote: Counting objects: 336, done.
      remote: Total 336 (delta 0), reused 0 (delta 0), pack-reused 336
      Receiving objects: 100% (336/336), 63.92 KiB | 2.06 MiB/s, done.
      Resolving deltas: 100% (177/177), done.
      catmini:~ cat$ cd test-project/
      catmini:test-project cat$ ls -alu
      total 48
      drwxr-xr-x  11 cat  staff   352 Dec 15 14:41 .
      drwxr-xr-x+ 97 cat  staff  3104 Dec 15 14:40 ..
      drwxr-xr-x  13 cat  staff   416 Dec 15 14:40 .git
      -rw-r--r--   1 cat  staff   403 Dec 15 14:40 .gitignore
      -rw-r--r--   1 cat  staff  1083 Dec 15 14:40 LICENSE
      -rw-r--r--   1 cat  staff  7601 Dec 15 14:40 README.md
      -rw-r--r--   1 cat  staff  4045 Dec 15 14:40 Vagrantfile
      drwxr-xr-x   4 cat  staff   128 Dec 15 14:40 ansible
      drwxr-xr-x   4 cat  staff   128 Dec 15 14:40 scripts
      drwxr-xr-x   3 cat  staff    96 Dec 15 14:40 test
      -rw-r--r--   1 cat  staff  1957 Dec 15 14:40 vagrant-hosts.yml
      catmini:test-project cat$ rm -rf .git
      catmini:test-project cat$ mkdir ansible/host_vars/
      catmini:test-project cat$ ansible-galaxy install -p ansible/roles bertvv.rh-base
      - downloading role 'rh-base', owned by bertvv
      - downloading role from https://github.com/bertvv/ansible-role-rh-base/archive/v2.3.0.tar.gz
      - extracting bertvv.rh-base to /Users/cat/test-project/ansible/roles/bertvv.rh-base
      - bertvv.rh-base (v2.3.0) was installed successfully
      catmini:test-project cat$ ansible-galaxy install -p ansible/roles bertvv.httpd
      - downloading role 'httpd', owned by bertvv
      - downloading role from https://github.com/bertvv/ansible-role-httpd/archive/v1.2.1.tar.gz
      - extracting bertvv.httpd to /Users/cat/test-project/ansible/roles/bertvv.httpd
      - bertvv.httpd (v1.2.1) was installed successfully

 #. Set basebox image in Vagrantfile

    In the Vagrantfile set your base box image::

     # Set your default base box here
     DEFAULT_BASE_BOX = 'bento/centos-7.4'

 #. Setup custer nodes in vagrant-hosts.yml

    The vagrant-hosts.yml file specifies the nodes that are controlled by Vagrant. You should at least specify a name:, other settings (see below) are optional. A host-only adapter is created and the given IP assigned to that interface. Other optional settings that can be specified.

     + ip: The IP address for the VM.
     + netmask: By default, the network mask is 255.255.255.0. If you want another one, it should be specified.
     + mac: The MAC address to be assigned to the NIC. Several notations are accepted, including "Linux-style" (00:11:22:33:44:55) and "Windows-style" (00-11-22-33-44-55). The separator characters can be omitted altogether (001122334455).
     + intnet: If set to true, the network interface will be attached to an internal network rather than a host-only adapter.
     + auto_config: If set to false, Vagrant will not attempt to configure the network interface.
     + synced_folders: A list of dicts that specify synced folders. Two keys, src (the directory on the host system) and dest (the mount point in the guest) are mandatory, another one, options is, well, optional. The possible options are the same ones as specified in the Vagrant documentation on synced folders. One caveat is that the option names should be prefixed with a colon, e.g. owner: becomes :owner:.

    Example::

        - name: srv002
          synced_folders:
            - src: test
              dest: /tmp/test
            - src: www
              dest: /var/www/html
              options:
                :create: true
                :owner: root
                :group: root
                :mount_options: ['dmode=0755', 'fmode=0644']

    The ansible/ directory contains the Ansible configuration, and should be structured according to Ansible's best practices. It should at least contain the standard site.yml.

 #. Adding nodes

    Initially, two hosts are defined as an example: srv001 and srv002. If you want to add new nodes, you should edit the files:

    vagrant-hosts.yml so a Vagrant box is created. A few examples that also illustrate the optional settings::

      - name: srv003
        ip: 192.168.56.13
        auto_config: false
        
      - name: srv004
        ip: 172.16.0.5
        netmask: 255.255.0.0
        intnet: true
        
      - name: srv005
        ip: 192.168.56.14
        mac: "00:03:DE:AD:BE:EF"
        playbook: server.yml  # defaults to site.yml

    site.yml to assign roles to your nodes, e.g.::

      - host: srv003
        become: true
        roles:
          - bertvv.rh-base
          - bertvv.httpd

 #. Testing via test_BATS_

    Testing via test_BATS_ that is Bash Automated Testing System is as simple as putting your BATS test scripts in the test/ directory and they will become available on your guest VMs as a synced folder, mounted in /vagrant/test. Scripts that you want to run on each host should be stored in the test/ directory itself, scripts for individual hosts should be stored in subdirectories with the same name as the host (see example below). Inside the VM, run

    Execute your tests via::

      sudo /vagrant/test/runbats.sh

    Suppose the test/ directory is structured like the example below::

      test/
      ├── common.bats
      ├── runbats.sh
      ├── srv001
      │   └── web.bats
      └── srv002
          └── db.bats

    On host srv001, the scripts common.bats and web.bats will be executed, on host srv002, it's common.bats and db.bats.



=====================
cluster.yml Explained
=====================
In the cluster.yml are comments with the following format::

 # Help Reference: <text for subsection below>


Local page References used

ansible-skeleton_
atb-init.sh_
ansible-tool-box_
test_BATS_

.. _ansible-skeleton: https://github.com/bertvv/ansible-skeleton.git
.. _atb-init.sh: https://github.com/bertvv/ansible-toolbox/blob/master/bin/atb-init.sh
.. _ansible-tool-box: https://github.com/bertvv/ansible-toolbox
.. _test_BATS: https://github.com/sstephenson/bats

