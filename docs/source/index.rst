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
   help-vagrant


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

