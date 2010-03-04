SilvaFind
=========

Layout for SilvaFind
--------------------

With the **Silva 2.0** release the Silva Find extension has been
included in the ‘all’ distribution. Silva users that have existing
layouts and plan on using Silva Find should change their layout to
include an extra CSS when using the extension.

Compared to Silva Search (which should now be considered deprecated)
Silva Find is replete with customizable functionality. It has far
better search reporting, displays an icon, a ranking, lists the
publication date, and quotes text snippets with highlighted search
terms. All these features can be turned on and off in the
configuration interface. It also allows customized, ‘canned’ searches
to be setup for specific purposes. If you’re making use of Silva’s
metadata system, Silva Find will also search the content of specified
metadata fields (if you have your own metadata sets, those fields need
to be indexed in the catalog to produce a result).

There is a simple search box implementation of Silva Find included in
the Silva 2.0 documentation. You can try it here too:
http://infrae.com/products/silva/docs

.. note::

  If you’re installing a fresh Silva 2.0 you can ignore all the
  following, as everything will be installed automatically. But if you
  have an existing public layout, two changes are needed. This is
  explained in the Silva Find INSTALL.txt, but nobody reads that :-)
  and since it’s now in the distribution this document is a heads up.

Layout modifications
,,,,,,,,,,,,,,,,,,,,

When a user is in a Silva Find content type, an extra stylesheet is
called to style the search results. This CSS is located in:

http://[yourSilva]/service_resources/SilvaFind/search.css

In order to call the CSS, there needs to be a check for the metatype
included in your layout. Add the following code within the ``<head>``
section of your ``layout_macro``, after the stylesheet links.

.. code-block:: html

   <!-- Injector for content-specific css/js inclusion -->
   <link tal:replace="structure here/head_inject | nothing" />

This calls a (new) page template called ``head_inject``. To install
it, you can reinstall the layout templates as explained here:

http://infrae.com/products/silva/docs/manager/ZMI_Silva#ZMI__Layout_templates

This will install all the templates including the new head_inject page
template. You can delete all the ‘default_*’s that appeared.

Customization
,,,,,,,,,,,,,

If you want to modify the ``search.css``, you can copy its selectors
and change the head_inject to call yourSearch.css. Don’t modify the
search.css on the file system in service_resources, as it should be
overwritten in an upgrade.

Silva Layout extension users
++++++++++++++++++++++++++++

If you’re using Five-based Silva Layout you’ll need to add the above
link tag to your main and possibly other templates.

Creating a head_inject manually
+++++++++++++++++++++++++++++++

You can skip installing the layout templates and create a page
template called ``head_inject`` in your Silva root with the following
code:

.. code-block:: html

  <tal:comment replace="nothing">
    define a model variable that works in public and smi preview skins
  </tal:comment>
  <tal:block tal:define="model request/model|here">
    <tal:block tal:condition="python:model.meta_type == ‘Silva Find’">
      <tal:comment replace="nothing">
        load custom styles for search form and search results
        if we’re viewing a Silva Find instance
      </tal:comment>
      <link rel="stylesheet" type="text/css" media="all"
        tal:define="metatypecss here/service_resources/SilvaFind/search.css/absolute_url | nothing"
        tal:condition="metatypecss"
        tal:attributes="href metatypecss" />
      <tal:comment replace="nothing">
        Make sure search results are never cached.
      </tal:comment>    
      <tal:block replace="structure model/@@set_nocache" />
    </tal:block>
  </tal:block>

.. note::

   Extension developers can use this to call specific css files for
   special content types. Modify the ``head_inject`` to check for your
   metatypes too. However, in the Five-based future, extensions will
   control their own views and this metatype checking won’t be
   necessary.