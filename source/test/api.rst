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


Functional testing
------------------

With the test layer, you can easily write functional tests. The method
:py:meth:`SilvaLayer.get_brower` will let you retrieve a Python web
browser: mangable via your test script, it will behave like a real web
browser from the point of view of the Silva application (without
Javascript support however).

Using this browser, you will be able to browser your application,
login, logout, fill and post forms and analyze page content in order
to verify that application behave like expected.

As an example:

.. code-block:: python
   :linenos:

   import unittest
   from Products.Silva.testing import FunctionalLayer, smi_settings

   class FunctionalTestCase(unittest.TestCase):
       layer = FunctionalLayer

       def setUp(self):
           self.root = self.layer.get_application()
           self.layer.login('author')

       def test_folder_smi(self):
           factory = self.root.manage_addProduct['Silva']
           factory.manage_addFolder('folder', 'Folder')
           browser = self.layer.get_browser(smi_settings)
           self.assertEqual(browser.open('/folder/edit'), 401)
           browser.login('author')
           self.assertEauql(browser.open('/folder/edit'), 200)

Up to line 11, we define a new test case like we did in
:ref:`writing-a-new-test`. Line 11 to 17 represent our functional
test. Fist we add a folder into our test site (line 12 and 13),
following what we explained in :ref:`adding-a-content-python`.

On line 14, we create a browser to test our application, using the smi
settings. First on line 15 we are going to try to access the SMI for
the created folder, but fail are we are not authenticated with our
browser.

.. warning::

   Authentication in the test case done with
   :py:class:`SilvaLayer.login` doesn't apply on the test browser. You
   can be logged in the test browser and not in the test case and vice
   versa.

On line 16 we login with the browser (set the authentication header in
other words), and try again to access the SMI for the created folder
on line 17, expected success this time.

For a complete documentation about the browser API, please refer to
the `infrae.testbrowser.browser.Browser`_ documentation.

Mail testing
------------

You might want to test code that sends emails. For this, the Silva
mail service (``service_mailhost``) have been mocked using
:py:class:`MockMailHost`. After testing your code, you can use the
`service_mailhost` to check if your code did send the messages like
expected, and if the content of those messages is correct.

The API for your tests provided by the mock email service is:

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

Sent email messages are represented with:

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
