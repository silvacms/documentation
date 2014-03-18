
Creating packages from buildout
===============================

You have the possibility to convert your buildout installation into a
native package that can be installed on Linux. This will permit you to
deploy all the settings and extensions you customized easily and
reliably into production.

.. contents::


Creating Debian/Ubuntu packages
-------------------------------

All the files required to create a package for Debian or Ubuntu are
located inside the sub-directory ``debian`` of the buildout directory.

In order to do this, just run this command in the buildout directory:

.. code-block:: sh

  $ debuild -uc -us

It will create a Debian package the Buildout profile
``profile/simple-instance.cfg``. In order to change which profile will
be used, you can edit the file ``debian/rules`` and change the profile
used in the *buildout.cfg* template located at the top of the file.


Creating Redhat/Centos packages
-------------------------------

You can create a RedHat package for RedHat, Centos or Fedora. Since
Python 2.7 isn't available on some of those systems, you can create a
package  for it as well.

From the buildout directory, start by creating the Python package:

.. code-block:: sh

   $ cd redhat
   $ sudo rpmbuild -bb python27.spec

Install the two created packages ``python27`` and
``python27-devel``. People installing your package will only need to
install ``python27``, ``python27-devel`` is only required to create
the silva package. So you need to install those two packages in order
to create your Silva one. When this is done, you can build the silva
package again from your buildout directory:

.. code-block:: sh

   $ cd redhat
   $ sudo rpmbuild -bb silva.spec

Like for the Debian package, it will create a package based on the
buildout profile ``profile/simple-instance.cfg``. To change it, edit
the ``silva.spec`` file and set your buildout profile instead.

Using packages created from buildout
------------------------------------

By default, packages created for Debian or Ubuntu and packages created
for Redhat or Centos follows the same conventions.

Silva will be installed in ``/opt/local``. Configuration files for the
default intance will be stored in ``/opt/local/etc/silva``. Data for
this instance will be stored in ``/var/lib/silva``. A script ``silva``
in ``/etc/init.d`` will be added in order to start and stop this Silva
instance with the help of *uwsgi*. For security reason, this instance
will run with a specific ``silva`` user. The Silva instance will
listen to the HTTP protocol on the port 8080 and to the uwsgi protocol
on the port 8081. This last port can be used by ``mod_proxy_uwsgi`` in
Apache or directly via NGinx, but this configuration is not done by
the package. For more information about this, please refer to
:ref:`presenting-your-silva-site-to-the-world`.

Logs will be stored in ``/var/log/silva`` and PID files in
``/var/run/silva``.

Starting Silva
~~~~~~~~~~~~~~

As root you will be able to use the following command to start Silva:

.. code-block:: sh

   $ sudo service silva start


Stopping Silva
~~~~~~~~~~~~~~

As root you will be able to use the following command to stop Silva:

.. code-block:: sh

   $ sudo service silva start

