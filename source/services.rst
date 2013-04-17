
.. _creating-a-new-silva-service:

Creating a Silva service
========================

Services are configurable utilities. An utility is an helpers which is
not content-specific: it doesn't store any data. However since they
are configurable, they can be configured to behave differently. The
configuration can be done through the web, using the :term:`ZMI` in
the services tab.

.. contents::

In order to create a new service, you must inherit from the class
:py:class:`silva.core.services.base.SilvaService`:

.. class:: silva.core.services.base.SilvaService

   Base class for services.

By default a service will be addable via the :term:`ZMI`, only
directly inside the Silva Root container. Services that implements the
interface :py:interface:`silva.core.interfaces.ILocalService` can be
added in any local site. A local site can be created by enabling it
inside a Silva Publication in the :term:`SMI` on the settings screen.

Any service must implement a specific interface that used to look it
up afterwards as an :term:`zope utility`.

Example of a service:

.. code-block:: python

  from silva.core import conf as silvaconf
  from silva.core.interfaces import ISilvaService
  from silva.core.services.base import SilvaService

  class IMySortingService(ISilvaService)

      def sort(items):
          pass

  class MySortingService(SilvaService):
      meta_type = 'Silva Sorting Service'

      silvaconf.name('service_sorting')
      silvaconf.icon('sorting.png')
      silvaconf.implements(IMySortingService)

      def sort(self, items):
          return sorted(items)

If your service needs to be folderish, you need to inherit from
``Folder`` first:

.. code-block:: python

  from OFS.Folder import Folder

  class MySortingService(Folder, SilvaService):
      pass

Accessing your Service
----------------------

After a service have been added to either the Silva Root or a local
site, you can lookup a service using its interface, like a :term:`Zope
utility`:

.. code-block:: python

   from zope.component import getUtility

   my_service = getUtility(IMYSortingService)
   my_service.sort([2, 1, 3])

Views on your service
---------------------

You can define view on your service for administration tasks:

.. code-block:: python

  from silva.core.views import views as silvaviews

  class MySortingSettings(silvaviews.ZMIView):
      silvaconf.context(MySortingService)
      silvaconf.name('manage_sorting')


Rules on ZMI views are exactly the same as on public views, however
there is no ``content`` attribute on them (since it's not for a
content). You still have to add your view name to the
``manage_options`` of your service.

If you need to write a template, your can use the following skeleton
in order to add the default ZMI tabs and style:

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

Forms on your service
---------------------

You can define forms in your service in order to edit its settings or
as user interface.

As an example, a migration form for the previous service. First you
need to define an interface which declares which fields (information)
are needed by your form:

.. code-block:: python

   from zope.interface import Interface
   from zope import schema

   class IMigrationForm(Interface):
      path = schema.TextLine(title="Path to migrate")

And then the form:

.. code-block:: python

   from zeam.form import silva as silvaforms
   from silva.core import conf as silvaconf

   class FileServiceMigrationForm(silvaforms.ZMIForm):
      silvaconf.context(IFilesService)
      silvaconf.name('manage_migration')

      fields = silvaforms.Fields(IMigrationForm)

      @silvaforms.action(_(u"Migrate"))
      def migrate(self):
          # do stuff
          pass

This works exactly like content based forms. You can as well reuse the
default edit action ``silvaforms.EditAction()``.

.. class:: zeam.form.silva.ZMIForm

   Base class for forms in the :term:`ZMI`.

.. seealso::

   :ref:`adding-forms-to-content`.

Using more complex forms in ZMI
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You have the possibility to define more complicated forms for your
service.

.. class:: zeam.form.silva.ZMIComposedForm

   Create a composed form that is usuable in the :term:`ZMI`.

.. class:: zeam.form.silva.ZMISubForm

   Create a sub form that is usuable in the :term:`ZMI`. It need to
   be associated with a :py:class:`zeam.form.silva.ZMIComposedForm`.

.. class:: zeam.form.silva.ZMISubTableForm

   Create a sub table form that can be used in the :term:`ZMI` if it
   is associated with a :py:class:`zeam.form.silva.ZMIComposedForm`.

Factories
---------

You can define factories by hand, but we recommand you not
to. Factories will let you define a custom add form and customize the
creation process of the service.

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
