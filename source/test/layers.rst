Test layer
==========

.. py:module:: Products.Silva.testing

.. contents::

Layer definition
----------------

.. py:class:: SilvaLayer

   Layer definition used to set up a Silva test site, where the
   default Silva ``service_mailhost`` is replaced by a
   :py:class:`MockMailHost`.


   .. py:method:: get_application()

      :returns: The Silva Root content of the test site.

   .. py:method:: get_browser(init_func=None)

      :param init_func: If provided, this function will be called with
                        the browser as first parameter to initialize
                        this last one, like to add expressions and
                        macros to it.
      :returns: A `infrae.testbrowser.browser.Browser`_ instance
                that queries the Silva Root of the test site.

   .. py:method:: login(username)

      :param login: Username to use to login. The list of available
                    username in the test site is defined by the
                    attribute :py:attr:`default_users`.

      Login as the given username into Zope.

   .. py:method:: logout()

      Logout from Zope.

   The following attributes defined the configuration of the test site:

   .. py:attribute:: default_products

      List of Zope 2 products installed in the test site (Silva
      dependencies).

   .. py:attribute:: default_packages

      List of Zope 2 packages installed in the test site (Silva
      dependencies).

   .. py:attribute:: default_users

      List of Zope 2 users created in the test site, with their roles
      (set as a dict). Namely:

      - Username ``manager``: role manager,

      - Username ``editor``: role editor,

      - Username ``chiefeditor``: role chief editor,

      - Username ``author``: role author,

      - Username ``reader``: role reader,

      - Username ``viewer``: role viewer.



.. py:data:: FunctionalLayer

   Layer instance of :py:class:`SilvaLayer` to use in your test case
   that needs to have working a Silva site.


.. py:function:: smi_settings(browser)

   Function that can be passed to :py:meth:`SilvaLayer.get_browser` in
   order to initialize the created browser with expressions that let
   you retrieve SMI elements like *feedback*, *tabs*, *breadcrumbs*
   and more.


Mail testing
------------

Test might need to test email features. For this, the Silva
``service_mailhost`` have been mocked using the following classes:

.. py:class:: MockMailHost

   Mock the ``service_mailhost`` not to send mail, but store them as
   :py:class:`MockMail` during a test. Test code can after check is
   mail have been sent correctly.

   .. py:attribute:: messages

      Sent messages as a list of :py:class:`MockMail`.

   .. py:method:: reset()

      Empty the the list of :py:attr:`messages`.

   .. py:method:: read_last_message()

      :return: A :py:class:`MockMail` or None.

      Return the last sent message, or None. If messages where sent,
      they are all removed.

.. py:class:: MockMail

   Mock a sent mail. MockMail are created by a :py:class:`MockMailHost`.

   .. py:attribute:: subject

      Subject of the sent mail.

   .. py:attribute:: headers

      Headers of the sent mail as a dict.

   .. py:attribute:: content_type

      Content type of the sent mail.

   .. py:attribute:: charset

      Charset in which the mail is sent.

   .. py:attribute:: text

      Mail content as text.

   .. py:attribute:: urls

      List of web URLs contained in the mail :py:attr:`text`.

.. _infrae.testbrowser.browser.Browser: http://pypi.python.org/pypi/infrae.testbrowser
