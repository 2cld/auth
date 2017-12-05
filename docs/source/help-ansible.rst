Ansible Help
================
This help section contains information on the creation and debugging of the Ansible playbook cluster.yml_.
The goal of the vagrantfile is a minimum setup of multiple vm then passing off the machine information so ansible can futher provision each vm.

.. _cluster.yml: https://github.com/2cld/auth/blob/master/cluster.yml

===========
Quick Start
===========
Below is quickstart vagrant steps::

  catmini:~ cat$ git clone https://github.com/2cld/auth.git
  catmini:~ cat$ cd auth
  catmini:~ cat$ vagrant up
  ... wait ... vagrant provison will run cluster.yml
  catmini:~ cat$ ansible-playbook cluster 
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