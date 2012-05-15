
.. _creating-a-new-visual:

Creating a visual theme
=======================

You can create a visual theme (or layout) as a filesystem-based
extension.

You need first to create an extension (please refer to
:ref:`creating-an-extension`). A full example is provided in this
documentation. If you wish to see more code examples, you can have a
look at the default layout extensions that are provided by default
with Silva. The code is available through Mercurial:

- `standard issue theme <https://hg.infrae.com/silvatheme.standardissue>`_.,

- `multiflex theme <https://hg.infrae.com/silvatheme.multiflex>`_.


First some vocabulary on visual themes:

.. glossary::

   *layer*
      A layer is an Zope interface to which are attached resources
      (CSS, images) and templates. Theses resources and templates are
      going to be used as soon as your layer is applied to your
      request, i.e. used.

   *skin*
      A Skin is an Zope interface which inherits from a set of
      layers. It has a name, and can be considered as a visual theme
      in Silva, and can be selected as such in the SMI. Some people
      call this element layout as well, but layout will be used to
      define another object, so it is better to use the term skin or
      visual theme.

   *view*
      A view is a Python object, to which can be associated a
      template, mainly used to render content as an HTML
      page. Different views can be defined and used on the same
      content.

      Interactions: This is a view is linked to a type of content, and
      a layer (or a skin).

   *layout*
      A layout is a Python object that can be attached to a layer
      (also to a skin). This will act like *the main template* of your
      visual theme, and render the main structure of your HTML pages,
      in which blanks will be filled with pages or content providers.
      You can as well register different layouts for different type of
      content.

   *page*
      A page is a view, which is designed to render a web page of a
      site, at a given URL. A page will look for a layout object, to
      render itself inside it (in order to reuse the HTML structure
      defined by the layout). For instance, the default page ``index``
      will be used to display your content, and extra pages, like
      ``search.html`` or ``detailed.html`` can be added reusing the
      visual design defined by the layout object.

To define a new visual theme, you need to define define two new
interfaces: a layer to attach your resources, and a skin to use them.

You can extend an already existing theme, by having your layer inherit
the theme layer. For instance you can reuse default theme definitions
done in the Porto layout.

Visual theme elements
---------------------

You use a few more elements to build your theme:

.. glossary::

   *Content Provider*
      .. image:: contentproviders.png
         :alt: Content providers
         :align: right

      A content provider is a named piece of Python code, with a
      template which renders a piece of HTML for a given content type,
      layer and page. You can register the same template code for
      different pages or type contents, and only change the HTML
      rendered by the content provider on those pages or contents by
      registering new ones. That's how you can implement a layout
      design, and having different ways to fill the content region
      depending of your content type or template.

      For instance, you could imagine a content provider called
      ``content``, which is going to display the name and email of
      people for an imaginary *Subscription* content type on the
      ``index`` page. On the ``search`` page of the same content type,
      is an another content provider of the same name, will render a
      search box to search people in the subscription type.

   *Viewlet Manager*
      A viewlet manager is basicly a content provider which can
      contain viewlets.

   *Viewlet*
      .. image:: viewlets.png
         :alt: Viewlets
         :align: right

      A viewlet is a template, or Python code, which renders a piece
      of HTML which can be inserted in a viewlet manager, for a given
      template and content type. The typical use is when you want to
      have blocks where actions, or other code can freely register
      it. You could imagine having a colunm on your layout, where
      layout parts can register dependently of the content.

The Porto Layout
----------------

Is a default and simple layout shipped with ``silva.core.layout``. Its
purpose is to contain default CSS for Silva Documents, and be easily
overriden to implement your own layout.

Layout Howto
------------

The next part will document the layout framework as a howto. It will
be a reference on how to build a new layout for Silva as well.

.. toctree::
   :maxdepth: 2

   howto
   providers
   legacy_howto
