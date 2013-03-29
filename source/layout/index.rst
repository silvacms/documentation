
.. _creating-a-new-visual:

======================
Creating a Silva theme
======================

You can create a Silva theme (or sometime called a layout) as a Silva
extension.

You need first to create an Silva extension (please refer to
:ref:`creating-an-extension`). A full example is provided in this
documentation. If you wish to see more code examples, you can have a
look at the default layout extensions that are provided by default
with Silva. The code is available through Mercurial:

- `standard issue theme <https://hg.infrae.com/silvatheme.standardissue>`_,

- `multiflex theme <https://hg.infrae.com/silvatheme.multiflex>`_


Silva theme main components
===========================

Some components are defined in order to let the user determine the
visual aspect and HTML rendering of the content accessed:

.. glossary::

   *Layer*
      A layer is a :term:`Zope interface` to which are attached
      resources (CSS, images) and templates. Theses resources and
      templates are going to be used as soon as your layer is applied
      to your request, i.e. used.

   *Skin*
      A skin is a :term:`Zope interface` which inherits from a set of
      layers. It has a name, and can be considered as a visual theme
      in Silva, and can be selected as such in the SMI. Some people
      call this a layout as well, but layout will be used to define
      another object, so it is better to use the term skin or visual
      theme.

   *View*
      A view is a Python object, to which can be associated a
      template, mainly used to render content as an HTML
      page. Different views can be defined and used on the same
      content.

      Interactions: This is a view is linked to a type of content, and
      a layer (or a skin).

      You can refer to :ref:`creating-a-default-view` for more
      information about views.

   *Layout*
      A layout is a Python object that can be attached to a layer
      (also to a skin). This will act like *the main template* of your
      visual theme, and render the main structure of your HTML pages,
      in which blanks will be filled with pages or content providers.
      You can as well register different layouts for different type of
      content.

   *Page*
      A page is a special :term:`view`, which is designed to render a
      web page of a site, at a given URL. A page will look for a
      :term:`layout` object, to render itself inside it (in order to
      reuse the HTML structure defined by the layout). For instance,
      the default page ``index`` will be used to display your content,
      and extra pages, like ``search.html`` or ``detailed.html`` can
      be added reusing the visual design defined by the layout object.

To define a new visual theme, you need to define define two new
:term:`Zope interface`: a :term:`layer` to attach your resources and a
:term:`skin` to use them.

You can extend an already existing theme, by having your layer inherit
this theme layer, or create them by yourself. For instance you can
reuse default theme definitions done in the Porto layout.

Silva theme extra components
============================

You use a few more elements to build your theme:

.. glossary::

   *Content Provider*
      .. image:: contentproviders.png
         :alt: Content providers
         :align: right

      A content provider is a named piece of Python code, with a
      template which renders a piece of HTML for a given content type,
      layer and page. You can register the same layout for different
      pages or contents type and only change the HTML rendered by the
      content provider on specific ones by registering a new content
      provider. This is how you can implement a generic layout and
      have different ways to fill-in specific HTML region depending of
      your page or content type.

      For instance, you could imagine a content provider called
      ``registered``, which is going to display the name and email of
      people for an imaginary *Subscription* content type on the
      ``index`` page. On the ``search`` page of the same content type,
      is registered an another content provider with the same name
      that will render a search box to search people in the
      subscription type instead of displaying the list.

   *Viewlet Manager*
      A viewlet manager is basicly a content provider which can
      contain viewlets.

   *Viewlet*
      .. image:: viewlets.png
         :alt: Viewlets
         :align: right

      A viewlet is a template, or Python code, which renders a piece
      of HTML which can be inserted in a viewlet manager, for a given
      layer and content type. The typical use is when you want to
      have blocks where actions, or other code can freely register
      it. You could imagine having a colunm on your layout, where
      layout parts can register dependently of the content.


Creating a Silva theme
======================

.. toctree::
   :maxdepth: 2

   base
   porto
