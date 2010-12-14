
Member Objects
==============

Members, which are mainly the editors have objects to manage their
personal information:

.. autointerface:: silva.core.interfaces.IMember


Some members have the right to modify their own settings:

.. autointerface:: silva.core.interfaces.IEditableMember

.. versionadded:: 2.2

Those members objects can be retrieved using
:class:`silva.core.interface.ISecurity` or ``service_members``.
