Services
========

Services are :term:`Zope utilities` that help you access some of the
functionalities of Silva.

.. contents::

Base Services
-------------

.. autointerface:: silva.core.interfaces.IZMIObject

.. autointerface:: silva.core.interfaces.ISilvaService

.. autointerface:: silva.core.interfaces.ISilvaLocalService

.. autointerface:: silva.core.interfaces.ISilvaInvisibleService


Silva Core Services
-------------------

Those services should always be present in any existing Silva Root.

.. autointerface:: silva.core.services.interfaces.ICatalogService

The extension service let you know if a Silva extension is installed
or not.

.. autointerface:: silva.core.services.interfaces.IExtensionService


The files service configure the storage of files and handle the
conversion of storage for existing fiels.

.. autointerface:: silva.core.services.interfaces.IFilesService

.. autointerface:: silva.core.services.interfaces.IContentFilteringService

.. autointerface:: silva.core.services.interfaces.IContainerPolicyService

.. autointerface:: silva.core.services.interfaces.ISecretService

You can refer as well to :ref:`security-and-user-information`.


Other Silva Services
--------------------

Those services are commonly found in a Silva Root, if the default
extensions are installed.

SMI related services
~~~~~~~~~~~~~~~~~~~~

.. autointerface:: silva.ui.interfaces.IUIService

.. autointerface:: silva.ui.interfaces.IUIGenericSettings

.. autointerface:: silva.ui.interfaces.IUIFolderSettings

Text edition related services
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. autointerface:: silva.core.editor.interfaces.ICKEditorService

.. autointerface:: Products.SilvaExternalSources.interfaces.ICodeSourceService
