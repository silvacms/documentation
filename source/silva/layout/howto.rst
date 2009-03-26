Creation of a new Layout
========================

.. contents::

We are going to create the new layout in a Python extension, as an
egg. We are going to use Paster (see :ref:`installing-paster` to get that
tool working on your computer).

.. code-block:: sh

  $ cd src
  $ paster create -t basic_namespace
  Selected and implied templates:
    ZopeSkel#basic_namespace  A project with a namespace package

  Enter project name: silvatheme.demo
  Variables:
    egg:      silvatheme.demo
    package:  silvathemedemo
    project:  silvatheme.demo
  Enter namespace_package (Namespace package (like plone)) ['plone']: silvatheme
  Enter package (The package contained namespace package (like example)) ['example']: demo
  Enter version (Version) ['1.0']:
  Enter description (One-line description of the package) ['']: Demo
  Enter long_description (Multi-line description (in reST)) ['']: Demo Layout for Silva 2.2
  Enter author (Author name) ['']: Infrae
  Enter author_email (Author email) ['']: info@infrae.com
  Enter keywords (Space-separated keywords/tags) ['']: silvatheme demo
  Enter url (URL of homepage) ['']:
  Enter license_name (License name) ['GPL']: BSD
  Enter zip_safe (True/False: if the package can be distributed as a .zip file) [False]:
  Creating template basic_namespace
  Creating directory ./silvatheme.demo
    Recursing into +namespace_package+
      Creating ./silvatheme.demo/silvatheme/
      Recursing into +package+
        Creating ./silvatheme.demo/silvatheme/demo/
        Copying __init__.py_tmpl to ./silvatheme.demo/silvatheme/demo/__init__.py
      Copying __init__.py_tmpl to ./silvatheme.demo/silvatheme/__init__.py
    Copying README.txt_tmpl to ./silvatheme.demo/README.txt
    Recursing into docs
      Creating ./silvatheme.demo/docs/
      Copying HISTORY.txt_tmpl to ./silvatheme.demo/docs/HISTORY.txt
    Copying setup.cfg to ./silvatheme.demo/setup.cfg
    Copying setup.py_tmpl to ./silvatheme.demo/setup.py
  Running /usr/local/bin/python2.4 setup.py egg_info


.. warning::

   *Project name* should be take the same value than
   *namespace_package.package*, otherwise you might have errors.


This will create a directory called
``silvatheme.demo/silvatheme/demo`` in ``src`` which will be a Python
package available as ``silvatheme.demo``. We will work there
afterwards and refer to this place as your extension.

After you need to register the new egg in your buildout.

The next step is to :ref:`enable-grok-for-your-extension`.

.. note::

   By default we take the convention to make the egg using the
   ``basic_namespace`` template, and to call it
   ``silvatheme.layoutname``, where ``layoutname`` is the name of your
   layout in lower case.


.. note::

    We are assuming here that you are going to create an egg in a
    buildout related setup. You can still build a non-egg theme, for
    that refer on creating a extension to see how to create a non-egg
    extension.

Creation of a skin
------------------

Let's create a Python file called ``demo.py`` in our extension. It
will contain the definition of our layout.

.. code-block:: python
   :linenos:

   from silva.core.layout.interfaces import ISilvaSkin
   from silva.core.layout.porto.interfaces import IPorto
   from silva.core import conf as silvaconf


   class IDemo(IPorto):
       """Demo layer used to attach resources.
       """

   class IDemoSkin(IDemo, ISilvaSkin):
       """Demo skin.
       """

       silvaconf.skin('Demo')


On line 6, we define a new layer to collect resources for our
theme. Basically it's just an interface which inherit form ``IPorto``,
since we want to reuse Porto layout resources. If you don't want those
resources you sould create your layer by extending ``ISilvaLayer``
which is defined in ``silva.core.layout.interfaces``.

On line 10, we define the skin itself. It will include our layer, and
``ISilvaSkin`` which define it as a skin. Line 14 register it with the
name ``Demo``. It will make it available throught the interface so
user can select it in the *settings* screen and apply it.


Add file resources
------------------

In your skin extension, you can create a directory called
``static``. This should not be a Python package, but just a
directory. Files in that directory will exported through Zope using
the following URL
``http://zope-url/++resources++silvatheme.demo/file.css`` where
``silvatheme.demo`` is the Python name of your extension, and
``file.css`` is a file in the static directory. You can export any
type of file you want like this.


Automatic inclusion of ressource files on a layer
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you use the Porto layout, you can automatically include CSS and
Javascript files in your layout. For that, we assume you put those
resources in the ``static`` sub-directory. After, on your layer, you
can mention them to get them included:

.. code-block:: python

   class IDemo(IPorto):
       """Demo layer used to attach resources.
       """

       silvaconf.resource('demo.css')

If you run your Zope instance in debug mode, the CSS cache won't be
cached, so you can work safely on the filesystem and refresh directly
in the browser to your changes. In production mode, the CSS file might
be mergedd with others, and cached by Zope and your web browser.


CSS files as resources
~~~~~~~~~~~~~~~~~~~~~~

If you want to include a CSS file by hand, a nice trick is to use the
``import`` statement:

.. code-block:: html

  <style type="text/css">
    @import url(http://zope-url/++resources++silvatheme.demo/file.css);
  </style>

After all resources refered in the imported CSS will be look by
default in the same folder that the CSS which will be the exported
resources directory.

