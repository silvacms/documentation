
.. _creating-a-new-silva-service:

Creating a new Silva service
============================

Services are configurable utilities. By utilities they are helpers
which are not content-specific. By configurable, they are helpers that
can be configured to behave differently.

For instance, we could imagine a sorting service, which sorts Silva
items. We could configure its sorting criteria (by ids, title or
modification date) and sorting algorithm (bubble sort, quick sort) in
that service. The contents or views can request that service to sort
a bunch of items.

You can create a service object like this:

.. code-block:: python

  from Products.Silva.BaseService import SilvaService
  from silva.core import conf as silvaconf

  class MySortingService(SilvaService):
      meta_type = 'Silva Sorting Service'

      silvaconf.icon('sorting.png')
      silvaconf.factory('manage_addMySortingServiceForm')
      silvaconf.factory('manage_addMySortingService')

If your service needs to be folderish, you can inherit from
``Folder``:

.. code-block:: python

  from OFS.Folder import Folder

  class MySortingService(Folder, SilvaService):
      ...

It's important here to define factories, they have to be in the same
module:

.. code-block:: python

  from Products.PageTemplates.PageTemplateFile import PageTemplateFile
  from Products.Silva.helpers import add_and_edit

  def manage_addMySortingService(self, id, REQUEST=None):
      """Add a sorting service.
      """

      service = MySortingService(id)
      self._setObject(id, service)
      add_and_edit(self, id, REQUEST)
      return ''

  manage_addMySortingServiceForm = PageTemplateFile(
      "www/mySortingServiceAdd", globals(),
      __name__='manage_addMySortingServiceForm')

You need to create a file ``www/mySortingServiceAdd.zpt`` for your
create form. For an example, please refer to one in Silva in the
``Products/Silva/www`` directory (and don't forget to make your form
submitting to ``manage_addMySortingService``).

View on your service
--------------------

You can define view on your service for administration tasks:

.. code-block:: python

  from silva.core.views import views as silvaviews

  class MySortingSettings(silvaviews.ZMIView):

      silvaconf.context(MySortingService)
      silvaconf.name('manage_sorting')

Rules on ZMI views are exactly the same as on public views, however
there is no ``content`` attribute on them (since it's not for
content). You still have to add your view name to the
``manage_options`` of your service.

If you need to write a template, your can use the following skeleton:

.. code-block:: html

  <html xmlns="http://www.w3.org/1999/xhtml"
        xmlns:metal="http://xml.zope.org/namespaces/metal"
        xmlns:tal="http://xml.zope.org/namespaces/tal"
        xmlns:i18n="http://xml.zope.org/namespaces/i18n"
        metal:use-macro="context/@@standard_macros/page">
    <body>
      <div metal:fill-slot="body">
         Your template code
      </div>
    </body>
  </html>

Edit forms
----------

You can used formlib-based forms in your service to edit its
configuration. For instance, let's take the following interface which
defines a file service:

.. code-block:: python

  from Products.Silva.interfaces import ISilvaLocalService
  from zope import schema

  class IFilesService(ISilvaLocalService):

      storage = schema.Choice(title=_(u"Files Storage"),
                              description=_(u"Method used to store files"),
                              required=True,
                              vocabulary="File Storage Type")

You can define an edit form like this:

.. code-block:: python

  from five import grok

  class FileServiceManagementView(silvaviews.ZMIEditForm):

      silvaconf.context(IFilesService)
      silvaconf.name('manage_filesservice')

      form_fields = grok.Fields(IFilesService)

Regular forms
-------------

Like edit forms, you can create regular formlib-forms.

As an example, a migration form for the previous service. First you
need to define an interface which declares which fields (information)
are needed by your form:

.. code-block:: python

   from zope.interface import Interface

   class IMigrationForm(Interface):

      path = schema.TextLine(title=_("Path to migrate"))

And then the form:

.. code-block:: python

   class FileServiceMigrationForm(silvaviews.ZMIForm):

      silvaconf.context(IFilesService)
      silvaconf.name('manage_migration')

      form_fields = grok.Fields(IMigrationForm)

      @grok.action(_(u"Migrate"))
      def migrate(self, path):
          # do stuff
          pass

This works exactly like content-based forms.

