2cld auth Install - Ansible
===============================

==========
References
==========

 #. Public Repo at https://github.com/2cld/kerberos
 #. Public Documents at http://2cldkerberos.readthedocs.io/en/latest/
 #. Model References 

    #. http://docs.ansible.com/ansible/latest/intro_installation.html#latest-releases-via-pip 
    #. https://github.com/kubernetes-incubator/kubespray 
    #. https://github.com/bennojoy/ansible-roles 

 #. kerberos-server

    #. https://galaxy.ansible.com/bennojoy/kerberos_server 
    #. https://github.com/bennojoy/kerberos_server
    #. https://github.com/2cld/kerberos_server

 #. kerberos-client 

    #. https://galaxy.ansible.com/bennojoy/kerberos_client
    #. https://github.com/bennojoy/kerberos_client
    #. https://github.com/2cld/kerberos_client

 #. openldap-server

    #. https://galaxy.ansible.com/bennojoy/openldap_server
    #. https://github.com/bennojoy/openldap_server
    #. https://github.com/2cld/openldap_server
 
 #. ntp

    #. https://galaxy.ansible.com/bennojoy/ntp/
    #. https://github.com/bennojoy/ntp
    #. https://github.com/2cld/ntp

 #. network

    #. https://galaxy.ansible.com/bennojoy/network_interface
    #. https://github.com/bennojoy/network_interface
    #. https://github.com/2cld/network_interface

=====================
Ansible Install Steps
=====================

node creation 
-------------
 #. Centos base image (with ssh) ps1
 #. ssh root@ps1
 #. Centos base image (with ssh) ctnode1
 #. ssh root@ctnode1
 #. Centos base image (with ssh) ctnode2
 #. ssh root@ctnode2

krb5-server installation
------------------------
 #. [root@ps1 ~]# yum install krb5-server
 #. [root@ps1 ~]# vi /etc/krb5.conf