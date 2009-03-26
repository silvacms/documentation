
Creating a new layout
=====================

You can create a new layout as a filesystem-based extension. So you
need first to create an extension (:ref:`creating-an-extension`). A
full exemple will be provided in that documentation, otherwise you can
look at the source of the `multiflex layout source
<https://svn.infrae.com/silvatheme.multiflex/trunk>`_.

First some vocabulary on layouts:

.. glossary::

   *layer*
      A Layer is an Zope interface to switch are attached resources
      (CSS, images) and templates. Theses resources and templates are
      going to be used as soon as your layer is applied to your
      request, i.e. used.

   *skin*
      A Skin is an Zope interface which inherit from a set of
      layer. It a have a name, and can be considered as a layout (or
      theme) in Silva.


So to define a new layout, we are going to define two new
interfaces. You can either define your all layout from scratch, or
re-use an existing one, like the Porto layout.


Layout Elements
---------------

You dispose of a few elements to build your theme:

.. glossary::

   *template*
      A template can be used to define the structure of your page for
      a given content type (interface) and a given layer. It will be
      the main component of your layout.

   *content provider*
      .. image:: contentproviders.png
         :alt: Content providers
         :align: right

      A content provider is a named piece of template, or Python code
      which render a piece of HTML for a given template, content type
      and layer. You can register the same template code for different
      pages or type contents, and only change the HTML rendered by the
      content provider on those pages or contents by registering new
      ones. That's how you can implement a layout design, and having
      different ways to fill the content region depending of your
      content type or template.

      For instance, you could imagine a content provider called
      ``content``, which is going to display the name and email of
      people for the imaginary *Subscription* content type on the
      ``index`` page. On the ``search`` page of the same content type,
      it an another content provider of the same name will render a
      search box to search people in the subscription type.

   *viewlet manager*
      A viewlet manager is basicly a content provider which can
      contains viewlets.

   *viewlet*
      .. image:: viewlets.png
         :alt: Viewlets
         :align: right

      A viewlet is a template, or Python code, which render a piece of
      HTML which can be inserted in a viewlet manager, for a given
      template and content type. The typical use is when you want to
      have blocks where actions, or other codes can register freely to
      it. You could imagine having a colunm on your layout, where
      layout parts can register dependly of the content.


The Porto Layout
----------------

It's a default and simple layout shipped with
``silva.core.layout``. Its purpose is to contains default CSS for
Silva Documents, and be easilly overriden to implement your own
layout.


Layout Howto
------------

The next part will document the layout framework as more or like an
howto. It will the reference on how to build a new layout for Silva as
well.

.. toctree::
   :maxdepth: 2

   howto
