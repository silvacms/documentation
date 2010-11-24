Introduction to the Zope component architecture
===============================================

This introduction should cover everything you need to know in order to
use Silva API. If you wish a more detailed documentation, you can have
a look at: http://www.muthukadan.net/docs/zca.html.

.. contents::


How to retrieve a Zope adapter
------------------------------

.. module:: silva.core.interfaces.auth

If you wish to retrieve an adapter of the functionality provided by an
adapter, for instance :py:interface:`IAuthorizationManager`, you can
directly call the interface:

.. code-block:: python

   from silva.core.interfaces.auth import IAuthorizationManager

   adapter = IAutorizationManager(content)

Depending of the content (which :term:`Zope interface` it implements),
you will get a different adapter implementation matching the content
that provides you with the :term:`Zope interface` you asked for (here
:py:interface:`IAuthorizationManager`).

If no adapter can be found to provide you with the :term:`Zope
interface` you are requesting, an exception will be raised. You can
prevent this by specifying a default value while doing the adapter
look up:

.. code-block:: python

   from silva.core.interfaces.auth import IAuthorizationManager

   adapter = IAutorizationManager(content, None)
   if adapter is None:
      # No adapter have been found
   else:
      # Can use adapter.


How to retrieve a Zope multiple-adapter
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Some adapters requires multiple objects to adapt in order to provide a
new :term:`Zope interface`.

You can look for those adapters using
``zope.component.getMultiAdapter``.


How to retrieve a Zope utility
------------------------------

.. module:: silva.core.services.interfaces

If you wish to retrieve an utility, you can use
``zope.component.getUtility``. For instance to retrieve Silva's
*service_members* (:py:interface:`IMemberService`):

.. code-block:: python

   from zope.component import getUtility
   from silva.core.services.interfaces import IMemberService

   service = getUtility(IMemberService)


If the utility is not found, an exception will be raised. If you wish
just to test if an utility is available, you can use
``zope.component.queryUtility`` that will return ``None`` is no
utility is found.
