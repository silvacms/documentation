
Silva Objects
=============

All Silva content objects must implements the two following
interfaces. They are the basics of the Silva API.


Silva Security interface
------------------------

The security interface defines all security related methodes.

.. autointerface:: silva.core.interfaces.ISecurity


Silva Object interface
----------------------

The Silva Object let you have dublin core-related information
(creation date, modification date, title) about an object.

.. autointerface:: silva.core.interfaces.ISilvaObject


