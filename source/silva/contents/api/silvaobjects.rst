
Silva Objects
=============

All Silva content objects implements the following API.
They are the basics of the Silva API.


Silva Security interface
------------------------

This API gives you access to the authorship information:

.. autointerface:: silva.core.interfaces.ISecurity


Titled Object interface
-----------------------

This API let you access information about an object title:

.. autointerface:: silva.core.interfaces.ITitledObject

.. versionadded:: 2.3


Silva Object interface
----------------------

The Silva object API is the default API shared by *all* content than
you can create and edit in Silva.

.. autointerface:: silva.core.interfaces.ISilvaObject


