2cld auth Create - Vagrantfile
===================================

==========
References
==========

 #. https://github.com/2cld/kerberos
 #. http://2cldkerberos.readthedocs.io/en/latest/
 #. https://github.com/floragunncom/kerberos_ldap_environment/blob/master/Vagrantfile

=========================
Vagrantfile Install Steps
=========================

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