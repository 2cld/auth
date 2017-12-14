Document: 2cld auth
===================

CeRe<BrAL - Create enviroment, Restore enviroment and Blockchain the resource/records of Auth(entic/orthiz)ation Logistics

 1. Create an enviroment from open knowlege into a useable instance with state.
 2. Restore a instance historical state enviroment with a known process in a know amount of time.
 3. Backup records of both Create and Restore environment process and data state.
 4. Logistics which include logging, monitoring events, event management, recovery statistics.

Auth - Authentication and Authorization

Create Auth
============

 1. Authentication will be with kerberos
 2. Authorization will be with open-ldap

Restore Auth
============

 1. kerberos restore is via a clonezilla image of the ps1 server and kerberos backup process
 2. open-ldap restore is via a clonezilla image of the ps1 server and open-ldap backup process

Methods
^^^^^^^

 #. Manual - Traditional 'human keyboard entry command line'

    1. Manual Example Command Line

     Shown below is ssh into::

        catmini:auth cat$ vagrant status
        Current machine states:

        tocld-01                  running (virtualbox)
        tocld-02                  running (virtualbox)

        This environment represents multiple VMs. The VMs are all listed
        above with their current state. For more information about a specific
        VM, run `vagrant status NAME`.
        catmini:auth cat$ vagrant ssh tocld-01
        Last login: Thu Dec  7 21:37:03 2017 from 10.0.2.2
        [vagrant@tocld-01 ~]$ 

     Finding the output of vagrant ssh command to ssh::

        catmini:auth cat$ ps aux | grep ssh
        cat               2816   0.0  0.0  4282060   4628   ??  S     7:42AM   0:00.01 /usr/bin/ssh-agent -l
        cat                712   0.0  0.1  4536116  24192   ??  S     7:31AM   0:01.05 /Library/PrivilegedHelperTools/ChromeRemoteDesktopHost.bundle/Contents/MacOS/remoting_me2me_host --host-config=/Library/PrivilegedHelperTools/org.chromium.chromoting.json --ssh-auth-sockname=/tmp/chromoting.cat.ssh_auth_sock
        cat               5653   0.0  0.0  1374216    148 s003  R+   12:12PM   0:00.00 grep ssh
        cat               5580   0.0  0.0  4315140   6068 s001  S+   12:11PM   0:01.30 ssh vagrant@127.0.0.1 -p 2222 -o LogLevel=FATAL -o Compression=yes -o DSAAuthentication=yes -o IdentitiesOnly=yes -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o IdentityFile="/Users/cat/.vagrant.d/insecure_private_key"
        cat               5579   0.0  0.0 558433096   1520 s001  S+   12:11PM   0:00.01 vagrant ssh tocld-01
        catmini:auth cat$ 

     Establish an ssh connection to one of the cluster nodes::

        ssh vagrant@127.0.0.1 -p 2222 -o LogLevel=FATAL -o Compression=yes -o DSAAuthentication=yes -o IdentitiesOnly=yes -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o IdentityFile="/Users/cat/.vagrant.d/insecure_private_key"

    2. Additional Manual Help

     .. toctree::
        :maxdepth: 2

        auth-create-manual
        auth-restore-manual
         

 #. Ansible - Automation via 'cluster.yml' playbook and 'inventory'

    1. Ansible Example Command Line

     Shown below is an ansible-playbook command line using vagrant inventory structure::

        catmini:auth cat$ /usr/local/bin/ansible-playbook --connection=ssh --timeout=30 --limit=all --inventory-file=/Users/cat/auth/.vagrant/provisioners/ansible/inventory --become --forks=2 --flush-cache cluster.yml

    2. Additional Ansible Help

     .. toctree::
        :maxdepth: 2

        auth-create-ansible
        auth-restore-ansible
        help-ansible
         

 #. Vagrantfile - vagrant up

    1. Vagrant Example Command Line

     Shown below is an vagrant vagrant inventory structure::

        catmini:auth cat$ vagrant up

    2. Additional Vagrant Help

     .. toctree::
        :maxdepth: 2

        auth-create-vagrant
        auth-restore-vagrant
        help-vagrant
         

Guides
^^^^^^

.. toctree::
   :maxdepth: 3

   auth-create-manual
   auth-restore-manual
   auth-create-ansible
   auth-restore-ansible
   auth-create-vagrant
   auth-restore-vagrant
   license
   help
   help-ssh
   help-vagrant
   help-ansible
   help-terraform
   help-docker


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

.. _auth-create-manual: ./auth-create-manual.html
.. _auth-restore-manual: ./auth-restore-manual.html
.. _auth-create-ansible: ./auth-create-ansible.html
.. _auth-restore-ansible: ./auth-restore-ansible.html
.. _auth-create-vagrant: ./auth-create-vagrant.html
.. _auth-restore-vagrant: ./auth-restore-vagrant.html
.. _help-ansible: ./help-ansible.html