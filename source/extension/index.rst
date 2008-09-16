Creating an extension
=====================

There are two different ways to create a Silva extension:

1. As a standard Zope product

2. As an egg

After creating an extension there are two ways to register it, and its
components with Silva:

1. ZCML (Zope Configuration Markup Language),

2. Grok. Grok is a kind of scanner which looks at your code and infers
   what needs to be registered and registers it. If you want to use
   Grok, we recommend to use only Grok, and to not try to mix Grok
   with ZCML declaration.

.. note:: 

   ZCML is a XML file format called ``configure.zcml`` by
   default. This file uses namespaced tags to be able to handle
   different registration actions used by the Silva extension. In this
   documentation, we be making a ``configure.zcml`` file for the blog
   product.

Creating a new extension as a Zope Product
------------------------------------------

In order to make a Silva extension it needs to be a Zope product. A
Zope product consists of the following:

- The product is a directory (with optional sub-directories and
  files).

- The product has a file called ``__init__.py`` in the root
  directory. This file can be empty, but the advice is to put at least
  a Python comment line in it (``#``), as some systems do not like
  completely empty files.

- The product directory is installed in a place that Zope looks for
  products, such as the Zope instance's ``Products`` directory. If you
  are using buildout this would be the ``products`` directory in your
  buildout instance of Zope.

- The name of your product directory must be the same as your product.

Basically, a Zope product is a Python package. Calling the product as
a python module with ``from`` or ``import`` looks like this:
``Products.product_directory``. This is true even if your product is
not located in a directory called ``Products``. This of course does
not follow the requirements just listed for a Zope product. The truth
of the matter is, where Zope looks for products can have any name, as
long as it is registered to contain products. For the sake of Silva
and Silva built using buildout all products should be located either
in the ``Products`` directory of the Zope instance, or the
``products`` directory of a buildout Zope instance.

Creating a new extension As an `Egg`
------------------------------------

`Eggs` are an way to provide and ship Python extension in a automated
way. The best way to use `eggs`, is to use them with the help of
`buildout <https://svn.infrae.com/buildout/silva/INSTALL.txt>`_.

You can create the structure of your `egg` with the help of the
`paster` command, and `ZopeSkel <http://pypi.python.org/pypi/ZopeSkel>`_:

.. code-block:: sh

  $ cd src
  $ paster create -t nested_namespace
  Selected and implied templates:
    ZopeSkel#nested_namespace  A project with two nested namespaces.

  Enter project name: silva.app.blog
  Variables:
    egg:      silva.app.blog
    package:  silvaappblog
    project:  silva.app.blog
  Enter namespace_package (Namespace package (like plone)) ['plone']: silva
  Enter namespace_package2 (Nested namespace package (like app)) ['app']: app
  Enter package (The package contained namespace package (like example)) ['example']: blog
  Enter version (Version) ['1.0']: 
  Enter description (One-line description of the package) ['']: My Extension
  Enter long_description (Multi-line description (in reST)) ['']: Long description about my extension.
  Enter author (Author name) ['']: Sylvain Viollon
  Enter author_email (Author email) ['']: info@infrae.com
  Enter keywords (Space-separated keywords/tags) ['']: silva app extension
  Enter url (URL of homepage) ['']: 
  Enter license_name (License name) ['GPL']: ZPL 2.1
  Enter zip_safe (True/False: if the package can be distributed as a .zip file) [False]: 
  Creating template nested_namespace
  Creating directory ./silva.app.blog
    Recursing into +namespace_package+
      Creating ./silva.app.blog/silva/
      Recursing into +namespace_package2+
        Creating ./silva.app.blog/silva/app/
        Recursing into +package+
          Creating ./silva.app.blog/silva/app/blog/
          Copying __init__.py_tmpl to ./silva.app.blog/silva/app/blog/__init__.py
        Copying __init__.py_tmpl to ./silva.app.blog/silva/app/__init__.py
      Copying __init__.py_tmpl to ./silva.app.blog/silva/__init__.py
    Copying README.txt_tmpl to ./silva.app.blog/README.txt
    Recursing into docs
      Creating ./silva.app.blog/docs/
      Copying HISTORY.txt_tmpl to ./silva.app.blog/docs/HISTORY.txt
    Copying setup.cfg to ./silva.app.blog/setup.cfg
    Copying setup.py_tmpl to ./silva.app.blog/setup.py
  Running /usr/local/bin/python2.4 setup.py egg_info

Here the Silva Blog product will reside in the newly created directory
``silva.app.blog/silva/app/blog``, which is a Python
module, accessible via ``silva.app.blog`` in your Python
code. It will be as well the name of your product in Zope (there is no
``Products``).

You need to create a file called ``configure.zcml`` in this directory,
to declare it as a Zope product. This is an XML file and should
contains:

.. code-block:: xml

  <configure
      xmlns="http://namespaces.zope.org/zope"
      xmlns:five="http://namespaces.zope.org/five">

    <five:registerPackage package="." />
  </configure>

After, if you use buildout, you can update your ``buildout.cfg`` file:

.. code-block:: ini

  [buildout]
  # Tells buildout that this egg exists.
  develop +=
     src/silva.app.blog

  [instance]
  # Add your egg to your Zope instance.
  eggs +=
     silva.app.blog
  # Load the ZCML for this egg. 
  zcml +=
     silva.app.blog

Add re-run ``buildout``:

.. code-block:: sh

  $ cd ..                            # Come back to the root directory of your buildout tree
  $ ./bin/buildout

This will let you use your egg while you will develop it, otherwise
you will have to install it each time you want to test your code using
the command ``easy_install``.


.. note::
 
  - Here used the `nested_namespace` template to create our egg.  If
    you plan to create something called `silva.extension` (there is no
    `app`), you can use the `basic_namespace` template of `paster`.

  - Because of the use of specific Zope python package, only present
    in a Zope environment, you may not be able to use your egg outside
    Zope. That's means as well you can't create an egg called
    `silva.mytech`, and `silva.mytech.extension`, if the first use
    some specific Zope package, otherwise operation on this egg
    (building, uploading) may fails due to missing import (these
    operations are not done by Zope or in a Zope environment).


From now, you should be able to restart your Zope instance, in ZMI, go
on `Control_Panel`, them `Products Management` and you will see your
extension in the listing.

Installation into the Silva Root
--------------------------------

You need to have an installer which is going to install our extension
in the selected Silva root. An installer is a class which define the
following methods:

``install``

   Is used to install the extension.

``uninstall``

   Is used to uninstall the extension.

``is_installed`` 

   Return ``True`` if the extension is installed, ``False`` otherwise.

Then you have this class, you create a instance called ``install`` in
the ``__init__.py`` file of your extension.

A default installer can be used, and extended. It provides the
following installation steps:

1. Add addable contents (all Silva content, no version content) to the
   addable list of the Silva site.

2. Register contents to the metadata service, to be able to set them
   on our objects. All contents are registered, but for versioned
   contents, class representing versions are registered instead of the
   class representing the content itself.

3. Eventually if you have one, register the ``views`` directory of
   your extension to the ``service_views``.

You don't need more steps to build an extension which create/add new
content in Silva.

As well, this installer use a marker interface on the
``service_extensions`` to known if the extension is installed.

So you can add to the ``__init__.py`` of our extension:

.. code-block:: python

  from silva.core.conf.installer import DefaultInstaller
  from zope.interface import Interface

  class BlogInstaller(DefaultInstaller):
      """Installer for the blog extension. Override install, uninstall 
      to add more actions.
      """


  class IBlogExtension(Interface):
      """Marker interface for our extension.
      """

  install = BlogInstaller("SilvaBlog", IBlogExtension)


The first argument to the installer is the name of our extension, the
second is the marker interface.

.. note::

  Your installer can be as well a module ``install.py`` in your
  extension, which defines the ``install``, ``uninstall`` and
  ``is_installed`` functions.


Registration with Grok
~~~~~~~~~~~~~~~~~~~~~~

Enabling Grok for your extension
````````````````````````````````

If you want to use Grok, you need to enable it in your extension.
This can be done with the help of a ZCML directive in the
``configure.zcml`` file located in your extension directory:

.. code-block:: xml

  <configure
    xmlns="http://namespaces.zope.org/zope"
    xmlns:grok="http://namespaces.zope.org/grok">

    <grok:grok package="." />

  </configure>

That's the last piece of required ZCML.


Registration
````````````

In the ``__init__.py`` of your extension, you can use the following
Python code to register it to Silva:

.. code-block:: python

  from silva.core import conf as silvaconf

  silvaconf.extensionName("SilvaBlog")
  silvaconf.extensionTitle("Silva Blog")

The name will be used internally, and the title will displayed in
``service_extension``. If your extension depends on another extension,
like `Silva Document` add an ``extensionDepends`` directive to your
the file:

.. code-block:: python

  silvaconf.extensionDepends("SilvaDocument")


If your extension has multiple dependencies, ``extensionDepends``
allows you to specify a tuple of strings.



Registration with ZCML
~~~~~~~~~~~~~~~~~~~~~~

In your extension directory, add the following ZCML directives to the
``configure.zcml``, or create the file if it doesn't exists yet:

.. code-block:: xml

  <configure
    xmlns="http://namespaces.zope.org/zope"
    xmlns:silva="http://infrae.com/ns/silvaconf">

    <silva:extension
      name="SilvaBlog"
      title="Silva Blog"
      />

  </configure>

You use the special ``silva:extension`` directive to specify the
extension name. You also give an extension title, which is a brief
description describing what the extension is about.


Reset point
~~~~~~~~~~~

At this point, you should be able to restart your Zope instance, and
be able to view, install and uninstall your extension using
`service_extensions` in the Silva root.


Upgrade step
------------

An upgrade step can be use to upgrade a content between two versions
of Silva. The upgrade method of the upgrader will be called against
each content of the given content type. Here, a sample to upgrade
`Silva Document` and `Silva Link` objects to Silva `2.1`:

.. sourcecode:: python

  from Products.Silva.upgrade import BaseUpgrader, AnyMetaType

  class MyUpgrade(BaseUpgrader):

      def upgrade(self, obj):
          """You can upgrade your content in this function.
          """
          pass

  myUpgradeForDocument = MyUpgrade(2.1, 'Silva Document') # This register the step for Silva Document
  myUpgradeForLink = MyUpgrade(2.1, 'Silva Link') # This register the step for Silva Link


``AnyMetaType`` can be used to declare that the step would be run
against all the contents, whatever are theirs meta type.
