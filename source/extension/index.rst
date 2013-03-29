.. _creating-an-extension:

Creating an extension
=====================

Silva extensions are regular Python packages. Like Python packages,
they can be released and distributed as an :term:`egg`. The name of
the Silva extension will often by the same name than the Python
package holding it. For instance ``silva.app.document`` is a Silva
extension that is defined inside a Python package
``silva.app.document``.

.. glossary::

   *Egg*
     Format in which a Python package can distributed to other
     users. This include all the code of the Python package, and some
     metadata information, like version, description, installation
     requirements ...

Within the Silva extension, you need to add some configuration in
order to declare your extension and its components to Silva. For this
you will need to use :term:`ZCML` and :term:`Grok`.

.. glossary::

   *ZCML*
     ZCML, or Zope Configuration Markup Language, is an XML based
     configuration language. The XML contains lines, called
     directives, that are used to trigger configuration in Zope. Each
     Zope extension contains usually a ``configure.zcml``
     configuration file, which hold its configuration in this format.

   *Grok*
     Grok is a configuration scanner which analyze the Python code of
     your extension in order to detect components extending Silva and
     register them if needed. Grok is trigger by a directive in
     :term:`ZCML`.

   *Grok directive*
      Grok directives are functions called inside a Python file at the
      level of a module or a class level to set specific configuration on
      those.


.. contents::

Creating a Silva extension
--------------------------

To create a Silva extension, you will need to create a Python
package. Using the same name is recommended. For instance you can
create a Blog extension called ``silva.app.blog``.

A Python package is a directory containing an ``__init__.py`` file. To
create the extension ``silva.app.blog`` you need to create the
corresponding directories and ``__init__.py`` files corresponding to
the Python package::

  silva/
  silva/__init__.py
  silva/app/
  silva/app/__init__.py
  silva/app/blog
  silva/app/blog/__init__.py

In order not to break any other Python packages sharing the Python
package ``silva.app`` with you, you need to fill-in the
``silva/__init__.py`` and ``silva/app/__init__.py`` files with the
following Python code:

.. code-block:: python

   try:
       __import__('pkg_resources').declare_namespace(__name__)
   except ImportError:
       from pkgutil import extend_path
       __path__ = extend_path(__path__, __name__)

This ``silva`` directory will constitute the source directory of your
Silva extension


Creating an egg
~~~~~~~~~~~~~~~

An :term:`egg` is created with the help of a ``setup.py`` file. This
file will call a setup function defined by `setuptools`_ with all the
metadata about the Python package. Executing this file on the command
line let you create and manage the Python package in order to release
it.

For clarity, we will put this ``setup.py`` file outside of the source
directory. For instance, for the ``silva.app.blog`` extension we will
get as final skeleton::

  setup.py
  src/silva/
  src/silva/__init__.py
  src/silva/app/
  src/silva/app/__init__.py
  src/silva/app/blog
  src/silva/app/blog/__init__.py

The ``setup.py`` should contains:

.. code-block:: python
   :linenos:

   from setuptools import setup, find_packages

   setup(name='silva.app.blog',
         version='1.0',
         description="Blog extension for Silva",
         long_description="Blog extension for Silva",
         classifiers=[
            "Programming Language :: Python",
         ],
         keywords='silva blog',
         author='You',
         author_email='you@example.com',
         url='',
         license='BSD',
         package_dir={'': 'src'},
         packages=find_packages('src'),
         namespace_packages=['silva', 'silva.app'],
         include_package_data=True,
         zip_safe=False,
         install_requires=[
             'Products.Silva',
         ])

- Line 3 and 4 defines the name of the Python extension and its version.

- Line 5 to 14 defines metadata that makes your Python extension
  searchable when it is distributed on a Package like the `Pypi`_.

- Line 15 defines which directory contains the Python extension,

- Line 16 defines which Python packages your Python extension
  contains. In our case in use the utility function ``find_packages``
  that does it automatically for us.

- Line 17 who are the Python packages used as namespace. In our case
  it is ``silva`` and ``silva.app``. If you created a Python package
  called ``silvatheme.blogtheme`` it would be ``silvatheme``.

- Line 18 and 19 includes non Python files (like :term:`ZCML` files) a
  part of our extension.

- Line 20 defines the requirements needed for this Python extension in
  order to works. In our case it is Silva.

For more information about ``setup.py``, you can refer to the official
Python documentation: :ref:`distutils-index`.

.. note::

   In order to improve the packaging process, `setuptools`_ is
   used. It is only a wrapper around the regular Python mechanims used
   to distribute Python extensions.

.. note::

   In addition you should create a MANIFEST.in file in order to ensure
   all the files you want are distributed with your extension when you
   make a tarball (:ref:`manifest`).

Configuring a Silva extension
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You need to create a :term:`ZCML` file called ``configure.zcml``
inside your Silva extension to declare it to Silva. It should contain:

.. code-block:: xml

  <configure
      xmlns="http://namespaces.zope.org/zope"
      xmlns:five="http://namespaces.zope.org/five"
      xmlns:grok="http://namespaces.zope.org/grok">

     <include package="Products.Silva" />
     <five:registerPackage package="." />
     <grok:grok package="." />

  </configure>

For the ``silva.app.blog`` example, the file should go inside the
``src/silva/app/blog`` directory.

Adding a Silva extension to an existing Buildout directory
----------------------------------------------------------

In you Buildout configuration file ``buildout.cfg`` or your own
profile file, you can declare the extension using the ``develop``
option of the ``buildout`` section. You can after add this extension
to the Zope instance using the ``egg`` option of the ``instance``
section and load its ZCML configuration with the ``zcml`` option of
the same section.

For the ``silva.app.blog`` example, if you created the Silva extension
inside the sub directory ``src/silva.app.blog`` of the Buildout
directory, you can configure it like this:

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


After modifying the Buildout configuration, you need to run
``buildout`` to apply the changes and restart your Zope instance:

.. code-block:: sh

  $ cd Silva
  $ ./bin/buildout
  $ ./bin/paster serve debug.ini

.. note::

  It is the recommended way to add a released Silva extension to your
  Buildout directory, however it is the recommended way for Silva
  extension that you are *developing*.


.. seealso::

   :ref:`creating-a-test-script`.

Installing an extension in Silva with ``service_extension``
-----------------------------------------------------------

In order to conditionally activate features in Silva, you might want
to declare your Silva extension to ``service_extension`` and create an
installer that will activate your Silva extension on demand.

The Silva extension can be declared with the help of :term:`Grok
directive` and a default installer can be used. It will let you
automatically activate and configure any content type that your Silva
extension creates.

.. note::

   Silva themes don't require to be activated via
   ``service_extension``. If your Silva extension contains only a
   theme, you are not required to declare your extension to
   ``service_extension`` and provide an installer.

Registring an extension with ``service_extension``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This configuration is done in Python, in the ``__init__.py`` file of
your extension. For example in the case of the ``silva.app.blog``
extension:

.. code-block:: python
   :linenos:

   from silva.core import conf as silvaconf

   silvaconf.extension_name("silva.app.blog")
   silvaconf.extension_title("Silva Blog")

- On line 3, the ``extension_name`` directive set the name of the
  extension.

- On line 4 the ``extension_title`` directive will set the title of
  extension, displayed in the :term:`ZMI`.

If your extension depends on others extensions, like on `Silva
Document` you can use the ``extension_depends`` directive to declare
them:

.. code-block:: python

   silvaconf.extension_depends("silva.app.document")

If your extension has multiple dependencies, ``extension_depends``
allows you to specify a tuple:

.. code-block:: python

   silvaconf.extension_depends(("silva.app.document", "silva.app.subscriptions"))


Activating a Silva extension via ``service_extension``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You need to provide an installer under the name of ``install`` in the
same ``__init__.py`` file that you declared your Silva extension to be
able to activate it.

You can extend the default installer provided by Silva. It will for
any content type found in your Silva extension, it will:

- Register them in Zope,

- Declare metadata set for them,

- Define and configure permission to add them,

- Declare them to the add menu,

- Handle the status activated or un-activated of the extension.

It can be used like this:

.. code-block:: python

  from silva.core.conf.installer import DefaultInstaller
  from zope.interface import Interface

  class BlogInstaller(DefaultInstaller):
      """Installer for the blog extension.
      """

      def install_custom(self, root)
          # Any custom installation action can be done here.
          pass

  class IBlogExtension(Interface):
      """Marker interface for our extension.
      """

  install = BlogInstaller("silva.app.blog", IBlogExtension)

The first argument to the install object is the name of our extension
``silva.app.blog``. The second is a :term:`Zope interface` used as
marker to know if the Silva extension is installed or not.

At this point, you should be able to restart your Zope instance, and
view, install, and uninstall your extension using ``service_extensions``
in the Silva root.

.. _Pypi: https://pypi.python.org/pypi
.. _five.grok: http://pypi.python.org/pypi/five.grok
.. _setuptools: https://pypi.python.org/pypi/setuptools
