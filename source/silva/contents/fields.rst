
Available Zope schema fields
============================

Each fields accept at least the following parameters:

``title``
   Title of the field, displayed in the form as field label. This must
   be a unicode string or a translated string.

``description``
   Description of the field, displayed in the form as field
   description. This must be a unicode string or a translated string.

``required``
   Boolean indicated if the field is required, or not.


General Zope schema fields
--------------------------

.. class:: zope.schema.Int

   Input a integer.


.. class:: zope.schema.Float

   Input a float number.


.. class:: zope.schema.Bool

   Input ``True`` or ``False``.


.. class:: zope.schema.Text

   Input a text, that might contains multiple line.


.. class:: zope.schema.TextLine

   Input a text, only one line.


.. class:: zope.schema.Password

   Input a password.


.. class:: zope.schema.URI

   Input a URL.


.. class:: zope.schema.Bytes

   Input a file.


.. class:: zope.schema.Date

   Input a :py:class:`datetime.date` object.


.. class:: zope.schema.Datetime

   Input a :py:class:`datetime.datetime` object.


.. class:: zope.schema.Time

   Input a :py:class:`datetime.time` object.


.. class:: zope.schema.Choice


Collection Zope schema fields
-----------------------------

.. class:: zope.schema.List

.. class:: zope.schema.Tuple

.. class:: zope.schema.Set


