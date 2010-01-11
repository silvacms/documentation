
Creating a new visual theme
===========================

You can create a new layout as a filesystem-based extension. So you
need first to create an extension (:ref:`creating-an-extension`). A
full exemple will be provided in that documentation, otherwise you can
look at the source of the `multiflex layout source
<https://svn.infrae.com/silvatheme.multiflex/trunk>`_.

First some vocabulary on layouts:

.. glossary::

   *layer*
      A layer is an Zope interface to which are attached resources
      (CSS, images) and templates. Theses resources and templates are
      going to be used as soon as your layer is applied to your
      request, i.e. used.

   *skin*
      A Skin is an Zope interface which inherit from a set of
      layer. It a have a name, and can be considered as a layout (or
      theme) in Silva.

   *layout*
      A layout is an object that can be attached to a layer, and so to
      a skin. It's going to be like *the main template* for your site,
      and let you render the content of a page in it. It's the element
      that will contain most of the HTML needed for you design.

   *page*
      A page correspond to a page of the site, that will be used to
      render a content at a given URL. For instance, by default, you
      have a default page ``index`` which use the defined view of your
      content to display itself. You can implement extra pages, like a
      ``search.html`` or ``detailed.html`` if you need.

      A page can be linked to a specific skin, and to a specific
      content. If you already have a generic page of the same name
      available for a skin, you will be able to customized that page
      by registering an another one only for a more specific content
      type.

      A page is going to look for the correct layout to use depending
      of your settings and render itself inside it.


So to define a new layout, we are going to define two new
interfaces. You can either define your all layout from scratch, or
re-use an existing one, like the Porto layout.


Layout Elements
---------------

You dispose of a few more elements to build your theme:

.. glossary::

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
