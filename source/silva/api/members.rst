
Security and User information
=============================

This document describes how to search and access member information, and change
security settings.

.. contents::


Member Objects
--------------

Members, which are authors, editors, managers have objects to manage their
personal information:

.. autointerface:: silva.core.interfaces.IMember


Some members have the right to modify their own information:

.. autointerface:: silva.core.interfaces.IEditableMember

.. versionadded:: 2.2

Those members objects can be retrieved using the ``service_members``:

.. autointerface:: silva.core.services.interfaces.IMemberService


Group Objects
-------------

Members can be grouped into Groups. Roles can be given to groups, in
that case all members of this group will get the given role.

A group is defined by:

.. autointerface:: silva.core.interfaces.auth.IGroup

.. versionadded:: 2.3


Group objects can be retrieved using the ``service_groups``:

.. autointerface:: silva.core.services.interfaces.IGroupService

.. versionadded:: 2.3

.. warning:: Sometimes the ``service_members`` and ``service_groups``
     are the same. It is important to use ``getUtility`` in order to
     be sure to have the correct service.


Accessing and editing authorizations settings
---------------------------------------------

On any Silva Object, you can use the following adapters in order to
retrieve authorizations settings:

.. autointerface:: silva.core.interfaces.auth.IAuthorizationManager

.. versionadded:: 2.3


A authorization object provides you with:

.. autointerface:: silva.core.interfaces.auth.IAuthorization

.. versionadded:: 2.3


Restricting access to a content
-------------------------------

You can restrict access to a Silva Object using the following adapter:

.. autointerface:: silva.core.interfaces.auth.IAccessSecurity

.. versionadded:: 2.3
