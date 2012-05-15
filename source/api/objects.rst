
Silva Objects
=============

All Silva content objects implements the following API.
They are the basics of the Silva API.


Silva Security
--------------

This API gives you access to the authorship information:

.. autointerface:: silva.core.interfaces.content.ISecurity


Titled Object
-------------

This API let you access information about an object title:

.. autointerface:: silva.core.interfaces.content.ITitledObject

.. versionadded:: 2.3


Silva Object
------------

The Silva object API is the default API shared by *all* content than
you can create and edit in Silva.

.. autointerface:: silva.core.interfaces.content.ISilvaObject


Viewable Object
---------------

Some Silva object are viewable to the user with the help of a layout
or an view, some other are not (like news filters, files, or images).

.. autointerface:: silva.core.interfaces.content.IViewableObject

.. versionadded:: 3.0
