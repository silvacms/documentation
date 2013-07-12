Testing API
===========

.. module:: Products.Silva.testing

.. contents::

Test layer definition
---------------------

A :term:`test layer` inheriting from the default Silva test layer will
create for you a Silva test site in order to run your tests in it and
provides with you utilities to access it.

.. py:class:: SilvaLayer

   Layer definition used to set up a Silva test site, where the
   default Silva ``service_mailhost`` is replaced by a
   :py:class:`MockMailHost`.


   .. py:method:: get_application()

      :returns: The Silva Root content of the test site.

   .. py:method:: get_browser(settings=None)

      Create and return a Python-based web browser suitable for
      functional testing.

      :param settings: If provided, this function will be called with
                       the browser as first parameter to initialize
                       this last one, like to add expressions and
                       macros to it.
      :returns: A `infrae.testbrowser.browser.Browser`_ instance
                that queries the Silva Root of the test site.

   .. py:method:: get_web_browser(setting=None)

      Create and return a Selenium-based web browser suitable for
      functional testing.

      :returns: A `infrae.testbrowser.browser.selenium.Browser`_
                instance that queries the Silva root of the test site.

   .. py:method:: login(username)

      :param login: Username to use to login. The list of available
                    username in the test site is defined by the
                    attribute :py:attr:`default_users`.

      Login as the given username into Zope.

   .. py:method:: logout()

      Logout from Zope.

   .. py:method:: open_fixture(filename, globs=None, mode='rb')

      Open the test fixture identified by ``filename`` located in the
      sub-directory ``test/data`` relatively from the directory where
      is the Python module defining the test layer.

      :returns: An opened file object to the test fixture.

   .. py:method:: get_fixture(filename, globs=None)

      Compute the full path to the test fixture identified by
      ``filename`` located in the sub-directory ``test/data`` relatively
      from the directory where is the Python module defining the test layer.

      :returns: The full path to the test fixture.

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


Unittest testing
----------------

Some extra tools are available for unittest tests:

.. py:class:: TestRequest

   This class creates a request object that behave like the Zope
   request object. It can be used in unit testing, for view and form
   lookup and testing for instance.

.. py:class:: Transaction

   This class is meant to be used as a Python context manager. It will
   commit in the test database the modification done by the code
   executed inside it.

.. py:class:: CatalogTransaction

   This class is meant to be used as a Python context manager. It will
   commit in the test database the modification done by the code
   executed inside it, with the catalog optimization activated.


Functional testing
------------------

With the test layer, you can easily write functional tests. The method
:py:meth:`SilvaLayer.get_browser` will let you retrieve a Python based
web browser: manageable via your test script, it will behave like a real
web browser from the point of view of the Silva application (without
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

.. note::

   This browser doesn't support Javascript.

Functional testing with Javascript-based application
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Settings for functional testing
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. module:: Products.Silva.ftesting

Different functions can be used in order to configure test browser
obtained with :py:meth:`SilvaLayer.get_browser` (Python-based browser)
or :py:meth:`SilvaLayer.get_web_browser` (Selenium-based browser):

.. function:: smi_settings(browser)

   Initialize the created browser with expressions that let you
   retrieve SMI elements like *feedback*, *error*, *listing* and
   more.

.. function:: public_settings(browser)

   Initialize the created browser with expressions that let you public
   elements rendered by Silva and the default Porto Layout components.

.. function:: zmi_settings(browser)

   Initialize the created browser with expressions that let you test
   services in ZMI and forms created by Silva for ZMI objects.

.. function:: rest_settings(browser)

   Initialize the created browser to be able to test REST calls. It is
   not compatible with the Selenium based browser.


Mail testing
------------

.. py:currentmodule:: Products.Silva.testing

You might want to test code that sends emails. For this, the Silva
mail service (``service_mailhost``) have been mocked using
:py:class:`MockMailHost`. After testing your code, you can use this
mocked `service_mailhost` to check if your code did send the messages
like expected, and if the content of those messages is correct.

The mock email service provides implements the following attributes
and methods:

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
.. _infrae.testbrowser.browser.selenium.Browser: http://pypi.python.org/pypi/infrae.testbrowser
