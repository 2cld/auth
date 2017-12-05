Help
====

Ping admin_at_2cld_dot_net

==========
References
==========

 + github https://github.com/2cld/auth
 + ReadTheDocs http://2cldauth.readthedocs.io/en/latest/
 + Kubespray https://github.com/kubernetes-incubator/kubespray
 + rst-cheatsheet https://github.com/ralsina/rst-cheatsheet/blob/master/rst-cheatsheet.rst

=================
Build ReadTheDocs
=================

To edit and update the readthedocs::

 $ cd ~
 $ git clone https://github.com/2cld/auth
 $ cd auth/docs 
 $ make html
 $ open build/html/index.html
 $ vi source/help.rst
 $ make html
 $ open build/html/index.html
 (verify changes)
 $ make clean
 $ cd ~/auth
 $ git add *
 $ git commit -m "Update documents"
 $ git push
 (wait some min for webhooks to hit)
 $ open http://2cldauth.readthedocs.io/en/latest/
 (inspect changes)
