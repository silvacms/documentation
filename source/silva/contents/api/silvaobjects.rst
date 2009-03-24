
Silva Objects
=============

All Silva content objects must implements the two following
interfaces. They are the basics of the Silva API.


Silva Security interface
------------------------

The security interface defines all security related methodes.

.. autointerface:: silva.core.interfaces.ISecurity


Base Silva Object interface
---------------------------

The base Silva Object let you have dublin core-related information
(creation date, modification date, title) about an object, and to
methodes to display it.

.. autointerface:: silva.core.interfaces.ISilvaObject


