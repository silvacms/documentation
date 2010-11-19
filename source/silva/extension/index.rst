.. _creating-an-extension:

Creating an extension
=====================

There are two different ways to create a Silva extension:

1. As a standard Zope product

2. As an egg

After creating an extension there are two ways to register it, and its
components with Silva:

1. ZCML (Zope Configuration Markup Language),

2. Grok. Grok is a kind of scanner which looks at your code and
   figures out what should be registered, and then registers it. If
   you want to use Grok, we recommend to use only Grok, and not mix
   Grok with ZCML declaration.

.. note::

   ZCML is a XML file format, in a file called ``configure.zcml`` by
   default. This file uses namespaced tags to be able to handle
   different registration actions used by Silva extensions. In this
   documentation, we will be making a ``configure.zcml`` file for the
   blog product shown here :ref:`base`.

Creating a new extension as a Zope Product
------------------------------------------

In order to make a Silva extension it needs to be a Zope product. A
Zope product consists of the following:

- The product is a directory (this can include sub-directories and
  files).

- The product must have a file called ``__init__.py`` in the root
  directory. This file can be empty, but we advise to at least place
  the Python comment ``#`` at the top of the page, because some
  systems do not lik empty files.

- The product directory is installed in a place that Zope looks for
  products, such as the Zope instance's ``Products`` directory. If you
  are using buildout this would be the ``products`` directory in your
  buildout instance of Zope.

- The name of your product directory must be the same as your product.

Basically, a Zope product is a Python package. You can Call the
product like a python module with ``from`` or ``import``. For example:
``Products.product_directory``. This is also true if your product is
not located in a directory called ``Products``. This of course does
not follow the requirements just listed above for a Zope product. The
truth of the matter is, where Zope looks for products can have any
name, as long as it is registered to contain products. For the sake of
Silva and Silva's built using buildout, all products should be located
either in the ``Products`` directory of the Zope instance, or the
``products`` directory of a buildout Zope instance.

Creating a new extension As an `Egg`
------------------------------------

`Eggs` are a way to provide and ship Python extensions in a automated
way. The best way to use `eggs`, is to use them with the help of
`buildout <https://svn.infrae.com/buildout/silva/INSTALL.txt>`_.

You can create the structure of your `egg` with the help of the
`paster` command, and `ZopeSkel
<http://pypi.python.org/pypi/ZopeSkel>`_ (see :ref:`installing-paster`
to install those tools):

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

.. warning::

   *Project name* should be take the same value as
   *namespace_package.namespace_package2.package*, otherwise you might
   encounter errors.

Here the Silva Blog product will reside in the newly created directory
``silva.app.blog/silva/app/blog``, which is a Python package,
accessible in your Python code via ``silva.app.blog``. This will also
be the name of your product in Zope (there is no ``Products``).

You need to create a file called ``configure.zcml`` in this directory,
to declare the extension as a Zope product. The ``configure.zcml`` is
an XML file and should contain:

.. code-block:: xml

  <configure
      xmlns="http://namespaces.zope.org/zope"
      xmlns:five="http://namespaces.zope.org/five">

    <five:registerPackage package="." />
  </configure>

Now, if you use buildout, you can update your ``buildout.cfg`` file:

.. code-block:: buildout

  [buildout]
  # Tell buildout that this egg exists.
  develop +=
     src/silva.app.blog

  [instance]
  # Add your egg to your Zope instance.
  eggs +=
     silva.app.blog
  # Load the ZCML for this egg.
  zcml +=
     silva.app.blog

And re-run ``buildout``:

.. code-block:: sh

  $ ./bin/buildout

This will let you use your egg while you will develop it, otherwise
you would have to install it each time you want to test your code
using the command ``easy_install``.

.. note::

  - Here use the `nested_namespace` template to create our egg. If you
    plan to create something called `silva.extension` (there is no
    `app`), you can use the `basic_namespace` template of `paster`.

  - Since you are using a specific Zope python package only present in
    a Zope environment, you may not use your egg outside Zope. This
    also means you cannot create and egg called `silva.mytech`, and
    `silva.mytech.extension`, if the first extension uses a specific
    Zope package, otherwise operations on the this egg (such as
    building and uploading) may fail due to missing
    imports. Operations like building and uploading are not handled by
    Zope or in the Zope environment.

At this moment you should be able to restart your Zope instance and
see your new extension. In the ZMI, go to the `Control_Panel`, then
`Products Management` and the extension should be in the listing.

Installation into the Silva Root
--------------------------------

To do this you need an installer which is going to install your
extension in the selected Silva root. An installer is a class that
defines the following methods:

``install``

   Is used to install the extension.

``uninstall``

   Is used to uninstall the extension.

``is_installed``

   Return ``True`` if the extension is installed, ``False`` otherwise.

You can code the installer directly into the ``__init__.py``. When the
installer is coded in the same file you need to create an instance of
the installer (see below).

A default installer can be used, and extended. It provides the
following installation steps:

1. Add addable content (all Silva content, no versioned content) to
   the list of addables of the Silva site.

2. Register contents to the metadata service in order to be able to
   set them on our objects. All contents are registered, but for
   versioned content objects, classes representing versions are
   registered instead of the class representing the content itself.

3. If you have a ``views`` directory register it in the
   ``service_views``.

3. Eventually if you have one, register the ``views`` directory of
   your extension to the ``service_views``.

Also, this installer uses a marker interface on the
``service_extensions`` to show if the extension is installed.

Add this to ``__init__.py`` file of your extension:

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

The first argument to the install object is the name of our extension
`SilvaBlog`. The second is the marker interface.

.. note::

  Your installer can also be a module ``install.py`` in your
  extension, which defines ``install``, ``uninstall`` and
  ``is_installed`` as functions.

Registration with Grok
~~~~~~~~~~~~~~~~~~~~~~

.. _enable-grok-for-your-extension:

Enable Grok for your extension
``````````````````````````````

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

   silvaconf.extension_name("SilvaBlog")
   silvaconf.extension_title("Silva Blog")

The ``extension_name`` will represent the name of the extension, and
the ``extension_title`` will displayed in the Silva interfaces (like
in ``service_extension``).

If your extension depends on others extensions, like `Silva Document`
you can use the ``extension_depends`` directive to declare this:

.. code-block:: python

   silvaconf.extension_depends("SilvaDocument")

If your extension has multiple dependencies, ``extension_depends``
allows you to specify a tuple:

.. code-block:: python

   silvaconf.extension_depends(("SilvaDocument", "Foo", "Bar"))

Registration with ZCML
~~~~~~~~~~~~~~~~~~~~~~

In your extension directory, add the following ZCML directives to the
``configure.zcml``, or create the file if it doesn't exist yet:

.. code-block:: xml

  <configure
    xmlns="http://namespaces.zope.org/zope"
    xmlns:silva="http://infrae.com/ns/silvaconf">

    <silva:extension
      name="SilvaBlog"
      title="Silva Blog"
      />

  </configure>

Here you use the special ``silva:extension`` directive to specify the
extension name. You can also give an extension title, which is a brief
description describing what the extension is about.

Reset point
~~~~~~~~~~~

At this point, you should be able to restart your Zope instance, and
view, install, and uninstall your extension using `service_extensions`
in the Silva root.

Upgrade step
------------

An upgrade step can be used to upgrade content between two versions of
Silva. The upgrade method of the upgrader will be called against each
content of the given content type. Here, a sample to upgrade `Silva
Document` and `Silva Link` objects to Silva `2.1`:

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
against all the contents, whatever their meta types.
