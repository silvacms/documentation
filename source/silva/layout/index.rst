
Creating a new visual theme
===========================

You can create a new visual theme (or layout) as a filesystem-based
extension.

You need first to create an extension (please refer to
:ref:`creating-an-extension`). A full example will be provided in that
documentation, otherwise you can look at the source of the `multiflex
theme <https://svn.infrae.com/silvatheme.multiflex/trunk>`_, or
`standard issue theme
<https://svn.infrae.com/silvatheme.standardissue/trunk/>`_.

First some vocabulary on visual themes:

.. glossary::

   *layer*
      A layer is an Zope interface to which are attached resources
      (CSS, images) and templates. Theses resources and templates are
      going to be used as soon as your layer is applied to your
      request, i.e. used.

   *skin*
      A Skin is an Zope interface which inherit from a set of
      layer. It a have a name, and can be considered as a visual theme
      in Silva, and can be selected as such in the SMI. Some people
      call this element layout as well, but layout will be used to
      define an other object, so prefer to use the term skin or visual
      theme.

   *view*
      A view is a Python object, to which can be associated to a
      template, mainly used to render a content as an HTML
      page. Different views can be defined and used on the same
      content.

      Interactions: A view is linked to a type of content, and a
      layer (or a skin).

   *layout*
      A layout is an Python object that can be attached to a layer (so
      to a skin). It's going to be like *the main template* of your
      visual theme, and render the main structure of your HTML pages,
      in which blanks will be filled with pages or content providers.
      You can as well register different layout for different type of
      content.

   *page*
      A page is a view, which is designed to render a web page of the
      site, at a given URL. It is going to look for a layout object,
      to render itself inside it (in order to reuse the HTML structure
      defined by the layout). For instance, the default page ``index``
      will be used to display your content itself, and extra pages,
      like ``search.html`` or ``detailed.html`` can be added reusing
      the visual design defined by the layout object.



To define a new visual theme, you need to define define two new
interfaces: one layer to attach your resources, and one skin to use
them.

You can extend an already existing theme, by having your layer
inheriting of the theme layer. For instance you can reuse default
theme definition done in the Porto layout.


Visual theme elements
---------------------

You dispose of a few more elements to build your theme:

.. glossary::

   *content provider*
      .. image:: contentproviders.png
         :alt: Content providers
         :align: right

      A content provider is a named piece of Python code, with a
      template which render a piece of HTML for a given content type,
      layer and page. You can register the same template code for
      different pages or type contents, and only change the HTML
      rendered by the content provider on those pages or contents by
      registering new ones. That's how you can implement a layout
      design, and having different ways to fill the content region
      depending of your content type or template.

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
   providers
