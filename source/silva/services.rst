Create a Silva service
======================

You can create a service object like this:

.. code-block:: python

  from Products.Silva.BaseService import SilvaService
  from silva.core import conf as silvaconf

  class MySortingService(SilvaService):
      meta_type = 'Silva Sorting Service'

      silvaconf.icon('sorting.png')
      silvaconf.factory('manage_addMySortingServiceForm')
      silvaconf.factory('manage_addMySortingService')

If your service need to be folderish, you can inherit from ``Folder``:

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
create form. For an example, please to refer to one of Silva in the
``Products/Silva/www`` directory (and don't forget to make your form
submitting to ``manage_addMySortingService``).

View on your service
--------------------

You can define view on your service for administration tasks:

.. code-block:: python

  from silva.core.views import views

  MySortingSettings(views.ZMIView):

      silvaconf.context(MySortingService)
      silvaconf.require('zope2.ViewManagementScreens')
      silvaconf.name('manage_sorting')
      ...
      

Rules on ZMI views are exactly the same than on public views, however
there is no ``content`` attribute on them (since it's not for a
content). You still have to add your view name to the
``manage_options`` of your service.

If you need to write template, your can use the following skeleton:

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
