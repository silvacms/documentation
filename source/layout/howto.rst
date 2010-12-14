Create a Layout
===============

.. contents::

You are going to create a new layout as a Python egg. We are going to
use Paster (see :ref:`installing-paster` to get the Paster tool
working on your computer).

.. code-block:: sh

  $ cd src
  $ paster create -t basic_namespace
  Selected and implied templates:
    ZopeSkel#basic_namespace  A basic Python project with a namespace package

  Selected and implied templates:
    ZopeSkel#basic_namespace  A basic Python project with a namespace package

  Enter project name: silvatheme.demo
  Variables:
    egg:      silvatheme.demo
    package:  silvathemedemo
    project:  silvatheme.demo
  Expert Mode? (What question mode would you like? (easy/expert/all)?) ['easy']: all
  Namespace Package Name (Name of outer namespace package) ['silvatheme']: silvatheme
  Package Name (Name of the inner namespace package) ['demo']: demo
  Version (Version number for project) ['1.0']:
  Description (One-line description of the project) ['']: Demo
  Long Description (Multi-line description (in ReST)) ['']: Demo Layout for Silva 2.2
  Author (Name of author for project) ['']: Infrae
  Author Email (Email of author for project) ['']: info@infrae.com
  Keywords (List of keywords, space-separated) ['']: silvatheme demo
  Project URL (URL of the homepage for this project) ['http://svn.plone.org/svn/collective/']: 
  Project License (Name of license for the project) ['GPL']: BSD
  Zip-Safe? (Can this project be used as a zipped egg? (true/false)) [False]:
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
    Copying setup.py_tmpl to ./silvatheme.demo/setup.py
  Running /usr/bin/python2.4 setup.py egg_info

.. warning::

   *Project name* should be take the same value than
   *namespace_package.package*, otherwise you might have errors.

Now in your ``src`` directory you should have a new Python package
``silvatheme.demo`` with the following directory structure:
``silvatheme.demo/silvatheme/demo``

Now that the basic structure is in place you need to register the new
egg in your buildout see (:ref:`setup-dev-env`).

The next step is to :ref:`enable-grok-for-your-extension`.

.. note::

   By default we use the convention to make the egg using the
   ``basic_namespace`` template, and to call using the following
   standard: ``silvatheme.layoutname``, where ``layoutname`` is the
   name of your layout in lower case.

.. note::

    We are assuming here that you are going to create an egg in a
    buildout related setup. You can still build a non-egg theme, for
    that refer on creating a extension to see how to create a non-egg
    extension.

Creation of a Skin
------------------

Let's create a Python file called ``demo.py`` in our extension. It
will contain the definition of our layout.

.. sourcecode:: python
   :linenos:

   from silva.core.layout.interfaces import ISilvaSkin
   from silva.core.layout.porto.interfaces import IPorto
   from five import grok


   class IDemo(IPorto):
       """Demo layer used to attach resources.
       """

   class IDemoSkin(IDemo, ISilvaSkin):
       """Demo skin.
       """

       grok.skin('Demo')

On line 6, we define a new layer to collect resources for our
theme. Basically it's just an interface which inherit from ``IPorto``,
since we want to reuse Porto layout resources. If you don't want those
resources you should create your layer by extending ``ISilvaLayer``
which is defined in ``silva.core.layout.interfaces``.

On line 10, we define the skin itself. It will include your layer, and
``ISilvaSkin`` which defines it as a skin. Line 14 registers the
layout with the name ``Demo``. It will make it available throught the
interface so user can select it in the SMI through the *settings* on
`properties` page.

Adding file resources
---------------------

In your skin extension, you can create a directory called
``static``. This should not be a Python package, but just a
directory. Files in that directory will be exported through Zope using
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
can call ``resource`` to get include the resource file:

.. sourcecode:: python

   from silva.core import conf as silvaconf

   class IDemo(IPorto):
       """Demo layer used to attach resources.
       """

       silvaconf.resource('demo.css')

If you run your Zope instance in debug mode, the CSS won't be cached,
so you can work safely on the filesystem and refresh directly in the
browser to see your changes. In production mode, the CSS file might be
merged with others, and cached by Zope and your web browser.

CSS files as resources
~~~~~~~~~~~~~~~~~~~~~~

If you want to include a CSS file by hand, a nice trick is to use the
``import`` statement in the HTML code:

.. code-block:: html

  <style type="text/css">
    @import url(http://zope-url/++resources++silvatheme.demo/file.css);
  </style>

Now all resources refered in the imported CSS will be looked at by
default in the same folder that the CSS, which will be the exported
resources directory.

.. note::

     If you include CSS and JS by hand, it is recommanded to include
     the CSS first and JS after, as this usually improves the loading
     speed of the resources by current web browsers.

Changing the HTML code of the layout
------------------------------------

The default Porto layout defines some content provider with which you
can overrides some HTML content of the ``<body>`` tag. You have by
default:

*header*
   Top section of the site. It is included by default a *breadcrumbs*
   provider that define a breadcrums navigation.

*navigation*
   Right after the top section this provider defines generic
   navigation code. Use can customize this behavior by inheriting it
   and settings some configuration attributes on it.

*footer*
   Bottom section of the site.

The three content providers are themselves included in a more global
one, *layout* which encompasses the entire body tag. If you wish to
replace all the HTML code inside the body section, you can override
the last one. For layouts which don`t follow the described structure
it is best to implement them.

.. note::

     The *layout* content provider is a content provider and not a
     layout object. The layout object in the Porto layout is called
     *mainlayout*. If you whish to replace that one, you can, but you
     will have to call the content provider *resources* yourself if
     you wish to use the automatic inclusion of CSS/JS resources.

So if we go back to in your ``demo.py`` file, you can add at the
bottom:

.. sourcecode:: python
   :linenos:

   from silva.core.layout.porto import porto

   grok.layer(IDemo)

   class Layout(porto.Layout):
       pass


This code declares a new content provider *layout* which just extends
the one from the Porto layout.

Now we can create an another directory called ``demo_templates`` in
our skin extensions, like we did for ``static``. This will be used to
add your *layout* template: like for templates associated with a view,
they are by default stored in a directory which starts with the name
of the Python module where they are and where you append
``_templates``.

