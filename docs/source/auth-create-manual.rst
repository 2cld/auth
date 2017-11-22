2cld auth Install - Manual
==========================

==========
References
==========

 #. https://github.com/2cld/kerberos
 #. http://2cldkerberos.readthedocs.io/en/latest/
 #. https://web.mit.edu/kerberos/krb5-1.12/doc/admin/realm_config.html
 #. https://web.mit.edu/kerberos/krb5-latest/doc/admin/install_kdc.html
 #. https://support.ca.com/us/knowledge-base-articles.TEC1213853.html

====================
Manual Install Steps
====================

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
     Uncomment lines that define realm and Search and Replace with your realm name::

          :%s/example.com/2cld.lan/
          :%s/EXAMPLE.COM/2CLD.LAN/

     Point kdc and admin_server to kerberos-master::

          kdc   ps1.2cld.lan
          admin_server = 2cldkrv5svr.2cld.lan

 #. [root@ps1 ~]# vi /var/kerberos/krb6kdc/kdc.conf
     Search and Replace with your realm name::

         :%s/EXAMPLE.COM/2CLD.LAN/

 #. [root@ps1 ~]# vi /var/kerberos/krb6kdc/kadm5.acl
     Search and Replace with your realm name::

         :%s/EXAMPLE.COM/2CLD.LAN/

 #. [root@ps1 ~]# kdb5_util_create -s -r 2CLD.LAN
     INPUT KDC database master key: (THIS IS PASSWORD)
 #. [root@ps1 ~]# systemctl enable kadmin
 #. [root@ps1 ~]# systemctl enable krb5kdc
 #. [root@ps1 ~]# systemctl start kadmin
 #. [root@ps1 ~]# systemctl start krb5kdc
 #. [root@ps1 ~]# systemctl status kadmin
 #. [root@ps1 ~]# systemctl status krb5kdc
 #. [root@ps1 ~]# firewall-cmd --get-services | grep kerberos --color
     Check if kerberos service is defined in firewall

 #. [root@ps1 ~]# firewall-cmd --permanet --add-service kerberos

admin and user authentication creation
--------------------------------------
 #. [root@ps1 ~]# kadmin.local
     This drops us into kadmin.local shell::

       kadmin.local:  addprinc root/admin_server
       Enter password for principal root/admin@2CLD.LAN: (THIS IS PASSWORD FOR PRINCIPAL)

     Add 2 client test nodes ctnode1 and ctnode2, then create keytabs::

       kadmin.local:  addprinc -randkey host/ctnode1.2cld.lan
       kadmin.local:  addprinc -randkey host/ctnode2.2cld.lan
       kadmin.local:  ktadd -k /tmp/ctnode1.keytab host/ctnode1.2cld.lan
       kadmin.local:  ktadd -k /tmp/ctnode2.keytab host/ctnode2.2cld.lan

     List principals::

       kadmin.local:  listprincs
       (Confirm you see the new hosts)

     Quit out of shell::

        kadmin.local: quit

 #. [root@ps1 ~]# scp /etc/krb5.conf /tmp/ctnode1.keytab ctnode1:/tmp/
 #. [root@ps1 ~]# scp /etc/krb5.conf /tmp/ctnode2.keytab ctnode2:/tmp/

krb5_workstation installation
-----------------------------
 #. [root@ctnode1 ~]# yum install pam_krb5 krb5_workstation
 #. [root@ctnode1 ~]# cp /tmp/krb5.conf /etc/
 #. [root@ctnode1 ~]# ktutil
     This drops us into ktutil shell::

       ktutil: rkt /tmp/ctnode1.keytab
       ktutil: wkt /etc/krb5.keytab
       ktutil: list
       (should list the keytab)
       ktutil: quit

 #. [root@ctnode2 ~]# yum install pam_krb5 krb5_workstation
 #. [root@ctnode2 ~]# cp /tmp/krb5.conf /etc/
 #. [root@ctnode2 ~]# ktutil
     This drops us into ktutil shell::

       ktutil: rkt /tmp/ctnode2.keytab
       ktutil: wkt /etc/krb5.keytab
       ktutil: list
       (should list the keytab)
       ktutil: quit

openldap-servers installation
-----------------------------
 #. [root@ps1 ~]# yum install openldap-servers openldap-clients migrationtools
 #. [root@ps1 ~]# cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG 
 #. [root@ps1 ~]# chown -R ldap. /var/lib/ldap/ 
 #. [root@ps1 ~]# id ldap
      Should see the ldap user id gid and groups

 #. [root@ps1 ~]# slapppasswd
      Should get a SSHA hash out, copy it 

 #. [root@ps1 ~]# cd /etc/openldap/slapd.d/cn\ config
 #. [root@ps1 cn-config]# vi olcDatabase\-\{0\}config.ldif
     Append the following to end of file::

      olcRootPW: {SSHA}longhashstringfromslappasswd

 #. [root@ps1 cn-config]# vi olcDatabase\-\{2\}hdb.ldif
     Change the following in file::

      :%s/my-domain/2cld/
      :%s/com/lan/

     So should change following (and add olcRootPW, olcAccess to bottom::

      olcSuffix: dc=2cld,dc=lan
      olcRootDN: cn Manager,dc-2cld, dc-lan
      olcRootPW: {SSHA}longhashstringfromslappasswd
      NOW AT END OF file
      olcAccess: {0}to attrs=userPassword by self write by dn.base="cn=Manager,dc=2cld,dc=lan" write by anonymous auth by * none
      olcAccess: {1}to * by dn.base="cn=Manager,dc=2cld,dc=lan" write by self write by * read

 #. [root@ps1 cn-config]# vi olcDatabase\-\{1\}monitor.ldif
     Change the following in file:: 

      :%s/my-domain/2cld/
      :%s/com/lan/

     So should change following::
      ... "cn Manager,dc 2cld,dc lan"

 #. [root@ps1 cn-config]# systemctl enable slapd 
 #. [root@ps1 cn-config]# systemctl start slapd 
 #. [root@ps1 cn-config]# netstat -nltp
     Should see slapd service listen on port 389, LDAPS would run on 636

 #. [root@ps1 cn-config]# firewall-cmd --get-services | grep lapd --color
     Check if lapd service is defined in firewall

 #. [root@ps1 cn-config]# firewall-cmd --permanet --add-service lapd
 #. [root@ps1 cn-config]# ls -l /etc/openldap/schema/
     Check for the schema names to import

 #. [root@ps1 cn-config]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/opneldap/schema/cosine.ldif
 #. [root@ps1 cn-config]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/opneldap/schema/nis.ldif
 #. [root@ps1 cn-config]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/opneldap/schema/inetorgperson.ldif
 #. [root@ps1 cn-config]# cd

 #. [root@ps1 ~]# vi base.ldif
     Insert the following::

      dn: dc=2cld,dc=lan
      objectClass: dcObject
      objectClass: organization
      dc: 2cld
      o : 2cld

      dn: ou=People,dc=2cld,dc=lan
      objectClass: organizatinalUnit
      ou: People

      dn: ou=Group,dc=2cld,dc=lan
      objectClass: organizatinalUnit
      ou: Group

 #. [root@ps1 ~]# ldapadd -x -D cn=Manager,dc=2cld,dc=lan -W -F base.ldif
     Should ask for password

 #. [root@ps1 ~]# ldapsearch -x -D cn=Manager,dc=2cld,dc=lan -W -b dc=2cld,dc=lan
     Should ask for password but then get search results.
      
user authorization installation and creation
--------------------------------------------
 #. [root@ps1 ~]# useradd testuser1 
 #. [root@ps1 ~]# useradd testuser2
 #. [root@ps1 ~]# cd /usr/share/migrationtools/ 
 #. [root@ps1 migrationtools]# vi migrate_common.ph
     Set domain search for pald.com and repalce with 2cld.lan::

      $DEFAULT_MAIL_DOMAIN = "2cld.lan";
      $DEFAULT_BASE = "dc=2cld,dc=lan";
      $EXTENDED_SCHEMA = 1;

 #. [root@ps1 migrationtools]# grep demo /etc/passwd > /tmp/users 
 #. [root@ps1 migrationtools]# grep demo /etc/group > /tmp/groups 
 #. [root@ps1 migrationtools]# ./migrate_passwd.pl /tmp/users /tmp/users.ldif
 #. [root@ps1 migrationtools]# ./migrate_group.pl /tmp/groups /tmp/groups.ldif 
 #. [root@ps1 migrationtools]# ldapadd -x -D cn=Manager,dc=2cld,dc=lan -W -f /tmp/groups.ldif 
 #. [root@ps1 migrationtools]# ldapadd -x -D cn=Manager,dc=2cld,dc=lan -W -f /tmp/users.ldif 
 #. [root@ps1 migrationtools]# cd 
 
 #. [root@ps1 ~]# rpm -q nfs-utils
     Check if package is installed 
 #. [root@ps1 ~]# yum install nfs-utils
 #. [root@ps1 ~]# vi /etc/exports
     Create exports::

     /home  *(rw,sync)

 #. [root@ps1 ~]# systemctl enable rpcbind 
 #. [root@ps1 ~]# systemctl start rpcbind 
 #. [root@ps1 ~]# systemctl enable nfs-server 
 #. [root@ps1 ~]# systemctl start nfs-server 
 #. [root@ps1 ~]# firewall-cmd --permanent --add-service nfs 
 #. [root@ps1 ~]# firewall-cmd --reload 
 #. [root@ps1 ~]# showmount -e
     Should show /home * Export
      
ctnode1 workstation installation and configuration
--------------------------------------------------
 #. [root@ctnode1 ~]# yum install nss-pam-ldapd
 #. [root@ctnode1 ~]# authconfig-tui
     Should get config UI.  Check "Use LDAP" for user info, Check "Use Kerberos" Authentication. Server: ldap://ps1.2cld.lan Base DN: dc=2cld,dc=lan

 #. [root@ctnode1 ~]# vi /etc/nsswitch.conf
     Should see ldap added to passwd, shadow and group

 #. [root@ctnode1 ~]# grep testuser1 /etc/passwd
     Should return nothing as this node does not know that user.

 #. [root@ctnode1 ~]# getent passwd testuser1
     Should see user data from ldap.

 #. [root@ctnode1 ~]# id testuser1
     Should see user data from ldap.

 #. [root@ctnode2 ~]# yum install nss-pam-ldapd
 #. [root@ctnode2 ~]# authconfig-tui
     Should get config UI.  Check "Use LDAP" for user info, Check "Use Kerberos" Authentication. Server: ldap://ps1.2cld.lan Base DN: dc=2cld,dc=lan
 
 #. [root@ctnode1 ~]# rpm -qa | grep nfs
     Check if pakcage is installed

 #. [root@ctnode1 ~]# yum install nfs-utils autofs
 #. [root@ctnode1 ~]# vi /etc/auto.master
     Add the following to the end of the file::

      /home  /etc/auto.autofs --timeout=600

 #. [root@ctnode1 ~]# vi /etc/auto.autofs
     Create the auto.autofs file

      *     ps1:/home/&

 #. [root@ctnode1 ~]# vi /etc/ssh/ssh_config
      Uncomment the following and change to yes

       GSSAPIAuthentication yes
       GSSAPIDelegateCredentials yes

 #. [root@ctnode1 ~]# vi /etc/ssh/sshd_config
      Uncomment the following and change to yes

       GSSAPIAuthentication yes
       GSSAPICleanupCredentials yes

 #. [root@ctnode1 ~]# systemctl reload sshd

ctnode2 workstation installation and configuration
--------------------------------------------------
 #. [root@ctnode2 ~]# rpm -qa | grep nfs
     Check if pakcage is installed

 #. [root@ctnode2 ~]# yum install nfs-utils autofs
 #. [root@ctnode2 ~]# vi /etc/auto.master
     Add the following to the end of the file::

      /home  /etc/auto.autofs --timeout=600

 #. [root@ctnode2 ~]# vi /etc/auto.autofs
     Create the auto.autofs file

      *     ps1:/home/&

 #. [root@ctnode2 ~]# systemctl enable autofs
 #. [root@ctnode2 ~]# systemctl restart autofs

 #. [root@ps1 ~]# kadmin.local
     Should put us in kadmin.local shell::

      kadmin.local: addprinc testuser1
      kadmin.local: addprinc testuser2

workstation authentication and authorization testing
----------------------------------------------------

This is just a test video link for `testing <https://youtu.be/n_-IRoZlRAE?t=199>`_.

Another type of link test_ for testing.

Yet another `workstation testing`__.

__ test_

.. _test: https://youtu.be/n_-IRoZlRAE?t=199



