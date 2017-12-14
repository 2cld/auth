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

=====================
cluster.yml Explained
=====================
In the cluster.yml are comments with the following format::

 # Help Reference: <text for subsection below>