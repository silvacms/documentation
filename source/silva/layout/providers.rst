Creation of content providers and viewlets
==========================================

.. contents::

Adding a new content provider
-----------------------------

Using a content provider
------------------------

You can directly call a content provider from a Zope Page Template by
using the ``provider:`` notation:

.. sourcecode:: html

   <div id="advertisement" tal:content="structure provider:advertisement" />

This will retrieve it, update it, and render it inside your template.

If for some reason you need to get the content provider from the
Python code itself, you can query the Zope Component Architecture to
retrieve it:

.. sourcecode:: python

   from silva.core.views import views as silvaviews
   from zope import component

   class MyView(silvaviews.View):

       def get_advertisement(self):
           provider = component.getMultiAdapter(
              (self.context, self.request, self),
              name='advertisement')
           provider.update()
           return provider.render()

Adding viewlets and viewlet managers
------------------------------------

You can define a new viewlet manager by inheriting from
``silvaviews.ViewletManager``. Here you can define a manager to
contain page actions:

.. sourcecode:: python

   from silva.core.views import views as silvaviews
   from silva.core import interfaces
   from five import grok

   class MyPageActions(silvaviews.ViewletManager):
       grok.context(interfaces.ISilvaObject)

Now, you can define some actions that will be rendered in your page's
action manager:

.. sourcecode:: python

   class MyMailAction(silvaviews.Viewlet):
        grok.context(interfaces.ISilvaObject)
        grok.viewletmanager(MyPageActions)

        def render(self):
            return u'Send by mail'

By modifying the context, you can define actions that will appear only
on some types of content instead of all of content. For instance, you
want to be able to download only documents as PDF:

.. sourcecode:: python

   from Products.SilvaDocument.interfaces import IDocument

   class MyDocumentAction(silvaviews.Viewlet):
        grok.context(IDocument)
        grok.viewletmanager(MyPageActions)

        def render(self):
            return u'Document as PDF'

.. sourcecode:: python

   class MyFolderAction(silvaviews.Viewlet):
        grok.context(interfaces.IFolder)
        grok.viewletmanager(MyPageActions)

        def render(self):
             return u'Folder RSS'
