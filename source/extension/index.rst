.. _creating-an-extension:

Creating an extension
=====================

You can create a Silva extension as a regular Python package, that can
be distributed as an :term:`Egg`.

.. glossary::

   *Egg*
     Format in which a Python package can distributed to other
     users. This include all the code of the Python package, and some
     metadata information, like version, description, installation
     requirements ...

Within the extension, will need to write some registration code, to
register your extension and its components in Silva. You can use two
different systems for this:

.. glossary::

   *ZCML*
     ZCML, or Zope Configuration Markup Language, is an XML based
     configuration language. Each Zope extension contains usually a
     ``configure.zcml`` file, which hold its configuration using this
     language.

   *Grok*
     Grok is a configuration scanner which looks at your code detect
     components and register them if needed. If you want to use Grok,
     we recommend to use only Grok, and not mix Grok with :term:`ZCML`
     based registration.


Creating a new extension as an :term:`Egg`
------------------------------------------

You can create the structure of your :ref:`Egg` with the help of the
``paster`` command, and :term:`ZopeSkel` (see :ref:`installing-paster`
to install those tools):

.. code-block:: sh

  $ cd src
  $ paster create -t nested_namespace
  Selected and implied templates:
    ZopeSkel#nested_namespace  A project with with a nested namespace (2 dots in name)

  Enter project name: silva.app.blog
  Variables:
    egg:      silva.app.blog
    package:  silvaappblog
    project:  silva.app.blog
  Expert Mode? (What question mode would you like? (easy/expert/all)?) ['easy']:
  Version (Version number for project) ['1.0']:
  Description (One-line description of the project) ['']:
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
    Copying setup.py_tmpl to ./silva.app.blog/setup.py
  Running /usr/local/bin/python2.6 setup.py egg_info


Here the Silva Blog product will reside in the newly created directory
``silva.app.blog/silva/app/blog``, which is a Python package,
accessible in your Python code via ``silva.app.blog``. This will also
be the name of your product in Zope.

You need to create a :term:`ZCML` file called ``configure.zcml`` in
this directory, to declare this package as a Zope product. It should
contain:

.. code-block:: xml

  <configure
      xmlns="http://namespaces.zope.org/zope"
      xmlns:five="http://namespaces.zope.org/five">

    <five:registerPackage package="." />

  </configure>

In you buildout configuration ``buildout.cfg`` or your own profile
file, you can add your newly created package to the Silva instance:

.. code-block:: buildout
   :linenos:

   [buildout]
   develop +=
      src/silva.app.blog

   [instance]
   eggs +=
      silva.app.blog
   zcml +=
      silva.app.blog


On line 3, you tell :term:`Buildout` that there is a package to use in
the given directory. On line 7, you add the package you created as a
dependency of Silva so it will be included in the Silva instance. On
line 9 you load its configuration.

You need ``buildout`` to apply the changes:

.. code-block:: sh

  $ ./bin/buildout

.. note::

   Here you used the ``nested_namespace`` template to create our
   :term:`Egg`. If you plan to create something called
   ``silva.extension`` (there is no ``app``, just one namespace
   ``silva``), you can use the ``basic_namespace`` template of
   ``paster``.


If you now start your Zope instance, it should contain the extension
you created. You can verify it in the ZMI, in `Control_Panel`,
`Products Management` the extension should be in the listing.


Installing your extension in Silva
----------------------------------

To do this you need an installer which is going to install your
extension in the selected Silva root. An installer is a class that
defines the following methods:

``install``
   Install the extension.

``uninstall``
   Uninstall the extension.

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

  install = BlogInstaller("silva.app.blog", IBlogExtension)

The first argument to the install object is the name of our extension
`SilvaBlog`. The second is the marker interface.

.. note::

  Your installer can also be a module ``install.py`` in your
  extension, which defines ``install``, ``uninstall`` and
  ``is_installed`` as functions.

Registration with :term:`Grok`
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _enable-grok-for-your-extension:

Enable Grok in your extension
``````````````````````````````

If you want to use :term:`Grok`, you need to enable it in your extension.
This can be done with the help of a :term:`ZCML` directive in your
``configure.zcml`` file of your Python package:

.. code-block:: xml
   :linenos:

   <configure
     xmlns="http://namespaces.zope.org/zope"
     xmlns:grok="http://namespaces.zope.org/grok">

     <include package="five.grok" />
     <grok:grok package="." />

   </configure>

On line 5, we include the `five.grok`_ extension that let use grok to
register our code. On line 6, we trigger that will go read our code
and register our components.

If you use Grok, is the last required piece of :term:`ZCML`.

.. _registering-extension-using-grok:

Registration
````````````

.. glossary::

   *Grok directive*
      Grok directives are functions called at a module or a class level
      to set configuration settings on those modules or classes.

In the ``__init__.py`` of your extension, you can use Grok directives
to register it to Silva:

.. code-block:: python
   :linenos:

   from silva.core import conf as silvaconf

   silvaconf.extension_name("silva.app.blog")
   silvaconf.extension_title("Silva Blog")

On line 3, the ``extension_name`` directive will set the name of the
extension, and on line 4 the ``extension_title`` directive will set
the title of extension, displayed in the Silva User Interface (like in
``service_extension``).

If your extension depends on others extensions, like on `Silva
Document` you can use the ``extension_depends`` directive to declare
this:

.. code-block:: python

   silvaconf.extension_depends("SilvaDocument")

If your extension has multiple dependencies, ``extension_depends``
allows you to specify a tuple:

.. code-block:: python

   silvaconf.extension_depends(("SilvaDocument", "Foo", "Bar"))


Reset point
~~~~~~~~~~~

At this point, you should be able to restart your Zope instance, and
view, install, and uninstall your extension using ``service_extensions``
in the Silva root.

Upgrade step
------------

An upgrade step can be used to upgrade content between two versions of
Silva. The upgrade method of the upgrader will be called against each
content of the given content type. Here, a sample to upgrade ``Silva
Document`` and ``Silva Link`` contents to Silva ``2.1``:

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

.. _five.grok: http://pypi.python.org/pypi/five.grok
