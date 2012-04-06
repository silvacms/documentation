.. Content layout demo documentation master file, created by
   sphinx-quickstart on Thu Mar 29 13:55:40 2012.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Content Layout Demo product overview
====================================

The demo product makes it possible to get several page designs in Silva.
The 'silva.demo.contentlayout' product contains several files and folders.

``templates``

  Folder where templates .pt, .cpt and .upt are located.

``static``

  Folder where static resources like css and javascript files are located.

``templates.py``

  A python file in which two designs are defined.

We start by setting a resource directory where the templates are located.

::

  grok.templatedir('templates')

The python file also defines two designs, a simple design called 'OneColumn', and a slightly more complex design with 'TwoColumns'.

Simple design
~~~~~~~~~~~~~

The simple design uses one template which gets filled by one slot:

.. py:class:: OneColumn(Template)

::

  class OneColumn(Template):
      silvaconf.icon("one_column.png")
      grok.order(10)
      grok.name('demo.one_column')
      grok.title(_(u"One Column (standard)"))

      description = _(u"A simple one column design")
      slots = {'main': Slot()}

This simple design has it's own icon 'one_column.png' located in the root of the product, a description and one slot called 'main'. As the name is 'OneColumn' it will automatically use a template called 'onecolumn.upt' from the 'templates' folder. You can specify the ordering of components by setting the ``Grok.order('numeric value')``. This order will be used in Silva for the design selection.

Complex design
~~~~~~~~~~~~~~

As with the first OneColumn design this second design will also use a CSS resource 'two_columns.css' and has two slots 'one' and 'two'.

To add the css we must create an interface:

.. py:class:: ITwoColumnsResources(IDefaultBrowserLayer)

::

  class ITwoColumnsResources(IDefaultBrowserLayer):
      silvaconf.resource('two_columns.css')

We'll use this interface in our design class:

.. py:class:: TwoColumns(Template)

::

  class TwoColumns(Template):
      silvaconf.icon("two_columns.png")
      grok.order(20)
      grok.name('demo.two_column')
      grok.title(_(u"Two Columns (standard)"))

      description = _(u"A simple two columns design")
      slots = {'one': Slot(),
               'two': Slot()}


      def update(self):
          need(ITwoColumnsResources)

As with the simple design this design also has it's own icon 'two_column.png', a description. Additionally it has multiple slots and an extra css. The order has an increased value and it will use a template called 'twocolumns.upt' automatically.

advanced.py
-----------

This is an advanced design based on the 'porto' layout.

Like for all designs we register the 'templates' resource directory:

::

  grok.templatedir('templates')

We set the design so it can only be set on a Page:

.. py:class:: IAdvancedTemplate(ICustomizableTag)

::

  class IAdvancedTemplate(ICustomizableTag):
      """Advanced design template for Page
      """
      silvaconf.only_for(IPage)

We register the CSS resource it will use:

.. py:class:: IAdvancedResources(IDefaultBrowserLayer)

::

  class IAdvancedResources(IDefaultBrowserLayer):
      silvaconf.resource('advanced.css')

Like in the other designs we tell the design which template(s) and resource(s) to use. In this advanced design we also define and restrict slots, and give it an extra menu.

We create a class, give it a grok order and a grok context which will set the context for this particular class:

.. py:class:: AdvancedTemplate(Template)

::

  class AdvancedDesign(Design):
      grok.order(5)
      grok.name('demo.advanced_template')
      grok.title(_(u"Advanced design (StandardIssue)"))

Like other designs we give it a description:

::

      description = _(u'A design that uses a section from a layout')

We set a marker for it:

::

      markers = [IAdvancedTemplate]

We create a set of slots and apply restrictions on them:

::

      slots = {
          'one': Slot(
              restrictions=[
                  restrictions.CodeSourceName('cs_citation')]),
          'two': Slot(
              restrictions=[
                  restrictions.CodeSourceName('cs_toc'),
                  restrictions.BlockAll()]),
          'navigation': Slot(
              restrictions=[
                  restrictions.Content(IImage),
                  restrictions.BlockAll()]),
          'footer': Slot(css_class="horizontal-blocks")}

Above you will notice that slot 'one' can only be filled with a Code Source named 'cs_citation', but all other Silva assets like images are still allowed. In slot 'two' you can only add a Code Source named 'cs_toc', and you can't add any other Silva assets. In slot three you can only add a Silva image.

We define an update to set the root and the resources:

::

      def update(self):
          self.root = self.content.get_publication()
          need(IAdvancedResources)

We also add some extra navigation code for this design to create a top menu:

::

      def top_menu_items(self):
          for content in self.root.get_ordered_publishables():
              if not IPublication.providedBy(content):
                  continue
              yield {'title': content.get_title_or_id(),
                     'css': content in self.request.PARENTS and 'active' or '',
                     'url': absoluteURL(content, self.request)}


We create our advanced design based on porto.Layout):

::

  class AdvancedLayout(porto.Layout):
      grok.context(IAdvancedTemplate)
      grok.layer(IStandardIssue)
      grok.name('layout')
