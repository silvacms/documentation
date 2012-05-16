Silva errors
============

.. currentmodule:: silva.core.interfaces.errors

Custom exceptions can be triggered, or returned by methods and
coroutines in case of errors.

They follow this API:


.. autointerface:: IError

This is implemented by:

.. class:: Error

.. autointerface:: IContentError

This is implemented by:

.. class:: ContentError

.. autointerface:: IContainerError

This is implemented by:

.. class:: ContainerError

.. autointerface:: IVersioningError

This is implemented by:

.. class:: VersioningError

Some errors are related to the content import and export feature:

.. autointerface:: IExportError

This is implemented by:

.. class:: ExportError

.. autointerface:: IExternalReferenceError

This is implemented by:

.. class:: ExternalReferenceError

.. autointerface:: IImportError

This is implemented by:

.. class:: ImportError
