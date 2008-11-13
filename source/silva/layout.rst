
Creating a new layout
=====================

You can create a new layout as a filesystem-based extension. So you
need first to create an extension (:ref:`creating-an-extension`).

First some vocabulary:

.. glossary::

   layer

     A Layer is an Zope interface to switch are attached resources
     (CSS, images) and templates. Theses resources and templates are
     going to be used as soon as your layer is applied to your
     request, i.e. used.

   skin

     A Skin is an Zope interface which inherit from a set of layer. It
     a have a name, and can be considered as a layout (or theme) in
     Silva.


So to define a new layout, we are going to define two new
interfaces. You can either define your all layout from scratch, or
re-use an existing one, the Porto layout.


Layout elements
---------------

You dispose of a few elements to build your theme:

.. glossary::

   template

      A template can be used to define the structure of your page for
      a given content type (interface) and a given layer.

   content provider

      A content provider is a named piece of template, or Python code
      which render a piece of HTML for a given template, content type
      and layer. For instance, you could imagine a content provider
      called ``content``, which is going to display the name and email
      of people for the imaginary *Subscription* content type on the
      ``index`` page. On the ``search`` page of the same content type,
      it an another content provider of the same name will render a
      search box to search people in the subscription type.

   viewlet manager

      A viewlet manager is basicly a content provider which can
      contains viewlets.

   viewlet

      A viewlet is a template, or Python code, which render a piece of
      HTML which can be inserted in a viewlet manager.

