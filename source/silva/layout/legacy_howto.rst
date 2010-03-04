Legacy site design and templates
================================

.. note::

   This document describes the 'legacy' layout system. That ZODB-based
   approach has been superseded by the SilvaLayout product and
   silva.core.layout. If you're creating a new layout we recommend
   using silva.core.layout. However you can use the legacy layout for
   quick proof of concept work and transfer most of the template work
   to the file system later.

This document describes how template designers can customize layouts
at different levels and locations within Silva.

The first section explains how the public view can be customized in
different locations. The second section describes the default layout
that appears in the `PreviewScreen
<http://infrae.com/++preview++/products/silva/author_basic/preview_document/>`_

Assumptions: The reader is assumed to have a working knowledge of
Zope, and a high level knowledge of HTML, CSS, and experience with
ZPT’s (Zope Page Templates). For the purposes of locating objects, the
Silva installation is assumed to be ‘default’, where Silva is
installed at the root level of a Zope instance, and named silva. All
paths in this document begin with ‘/silva’.

.. note::

   To limit confusion, we don’t use the word ‘user’. Silva has several
   built in roles (Author, Editor, ChiefEditor, SiteManager). These
   are always spelled with a capital letter.

Adding custom layouts in Silva
------------------------------

If no local custom layouts exist, public pages will be defined by a
ZPT at the top level of Silva:::

  /silva/layout_macro.html

This ZPT can be adjusted by the customer. It will be acquired by all
documents in lower folders. Before we analyze its structure, first we
should explain what happens when a browser request comes in. When an
index is requested (e.g. http://www.x.yz/silva/folder) there is a
Python script at the top level of Silva which checks for custom
layouts:::

  /silva/index_html

Using override.html
,,,,,,,,,,,,,,,,,,,

By placing an ``override.html`` in a folder, you insert a page that is
completely custom, meaning that it doesn’t necessarily use XML content
or layout macros from Silva.

Adjusting layout_macro.html and content.html
,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,

If no override is found, the index_html script uses content.html to
render the page. There is a ``content.html`` at the top level of
Silva. If you open it, you’ll find that it uses a macro:

.. code-block:: html

   metal:use-macro="container/layout_macro.html/macros/layout"

and it contains a number of fill-slots. (If you’re not familiar with
METAL, see http://www.zope.org/Members/tone/METAL for an explanation.)
Lets first look at the macro.

Analysis of layout_macro.html
,,,,,,,,,,,,,,,,,,,,,,,,,,,,,

The idea behind this macro is it should be as generic as possible, and
thus can be used throughout an entire site or branch. To achieve this
it contains a hierarchy of define-slots, which can be filled in a
local ``content.html``, if necessary. The slots don’t have to be
filled, in fact the fill-slots in content.html can be deleted if
they’re not used.

The layout macro contains the following slots:

  * title: the HTML title
  * meta: for local metatags
  * style: insert stylesheet references
  * script: if javascript is needed
  * page_header
  * navigation: the example content.html fills this slot with a breadcrumb trail
  * table_of_contents
  * main: content.html fills this slot with content from Silva
  * page_footer

One of the metatags is set up to get the Subject field metadata from a
Silva document:

.. code-block:: html

   <meta name="subject" content="Silva metadata"
     tal:attributes="content python:getattr(here, 'subject', '')" />

Title, meta, and style are singleton tags, while the others follow
this format:

.. code-block:: html

   <div metal:fill-slot="page_header" tal:omit-tag="">
     <!-- Page header slot (empty) -->
   </div>

The ``tal:omit-tag=""`` is there so that if the slot is not filled in
``content.html`` the tag is not rendered in the HTML. However in the
demo all the above slots are filled, either with content or a comment.

With this setup, generic layout characteristics can be coded in the
layout macro, while local elements can be implemented in
``content.html``. For instance, the current layout uses a table to
present the content. The table never changes, so it’s coded in the
layout macro. To demonstrate the navigation slot, we placed the
breadcrumb trail in content.html, but it really should be in the
layout macro, because it will work in all locations.

If you view the HTML source of the presentation preview, you’ll find
comments which identify all of the slots except main, which contains
Silva content. We use a convention for the comments in these
templates: comments which will appear in the HTML source start with a
capital letter. Those which will be replaced by TAL actions are in
lower case.

To simplify the ``content.html`` ZPT, most of the slots can be
deleted. In:::

  /silva/release_demo/local_content/content.html/manage_main

you’ll find a simplified version, with a bit of custom text to
demonstrate the workings.

There are two additional slots in layout_macro.html which contain
those listed above:

  * head
  * body

When necessary you can replace the entire head or body with custom
content created in ``content.html``. The layout_macro supports this
and thus doesn’t need to be changed.

Using content.html
,,,,,,,,,,,,,,,,,,

In the ZMI, if you go to the release_demo folder:::

  /silva/release_demo/local_content/manage_main

you won’t find a ``layout_macro.html``. When ``content.html`` calls
the macro, it’s acquired from the top level. You also won’t find a
content.html. When you view ``/silva/release_demo``, the index_html
script doesn’t find an override, so it looks for ``content.html``, and
moves up the acquisition tree until it finds one, in this case on the
top level.

However, in the subfolder ``/silva/release_demo/local_content`` the
script does find a ``content.html``, so it uses that one.

.. warning::

   When in the Silva Management Interface, if you copy and paste a
   Silva folder, all of the contents – including invisible items like
   content.html – are pasted with it.

When you want to customize content locally, use ``content.html``. But
remember that it will also be acquired by subfolders. If you don’t
want that to happen, use an ``override.html`` in that location.

If every page on your site is custom, then you’ll have to locate each
document in a Silva folder with a ``content.html``.

Wrap up
,,,,,,,

Ideally you can set up your site so that there is one
``layout_macro.html`` and one ``content.html`` at the top level. This
will make it very easy to maintain, since the layout code exists in
just one location. If you do need different layouts, you can copy the
templates into a branch, and hopefully design them so they render the
entire branch.

Then let the Authors and Silva do the work....

Use of absolute_url when referencing images
-------------------------------------------

Most of the time Zope acquisition works nicely, even correcting
mistakes in paths. If you’re working in a document:::

  /foo/bar/document

and insert an image which is actually in a top level images directory,
but the path is not complete (no starting slash):

.. code-block:: html

   <img src="images/logo.gif" ... />

then Zope still finds it. Zope climbs the acquisition tree until it
finds images, then it finds ``logo.gif``, and the page displays
fine. The problem is, the web browser thinks logo.gif is in:::

  /foo/bar/images/

and if ``logo.gif`` is used in multiple pages (because it’s included
in a template), as a visitor descends further, the web browser thinks
there’s an images directory in all directories, such as:

  /foo/bar/daz/images/

This means ``logo.gif`` is not cached by the browser, because it
thinks it’s another ``logo.gif`` in another location. The same thing
happens with stylesheet references.

The trick to solve this problem is of general interest (not just for
Silva). If you reference the image with a TAL attribute that ends with
``absolute_url``:

.. code-block:: html

   <img src="/images/logo.gif" ...
     tal:attributes="src here/images/logo.gif/absolute_url | nothing" />

then when the html is rendered, the image src is:::

  http://www.x.yz/images/logo.gif

The browser caches the image, and every other reference to
``logo.gif`` will be the same, so it stays cached. The ``| nothing``
at the end of the attribute insures that if the object is not found,
no Zope error gets triggered.

.. note::

   For Silva developers: in the SMI templates, we use an additional
   element which defines the silva_root. In 0.8 this is a property in
   the root folder. Since 0.8.3 it’s included in the core code (which
   also fixes the problem of renaming silva to something else). By
   defining this at the top of a template, Zope doesn’t have to climb
   the tree for every reference. The same can be done in the layout
   templates if there are a lot of images in being referenced.

Stylesheet references
,,,,,,,,,,,,,,,,,,,,,

Note that the stylesheet references in the Silva layout templates use
the same absolute_url technique:

.. code-block:: html

   <link rel="stylesheet" href="globals/silva.css" type="text/css"
     tal:attributes="href here/globals/silva.css/absolute_url | nothing" />
