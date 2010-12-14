
Helpers methods
===============

Some helpers can be used in you test to accomplish some actions.

.. module:: Products.Silva.tests.helpers

.. function:: enablePreview()

   Enable the rendering of the preview instead of the normal view.

.. function:: resetPreview()

   Reset preview mode.

.. function:: approveObject(obj)

   :param obj: object to approve.

   Approve the given object.

.. function:: publishObject(obj)

   :param obj: object to publish.

   Publish the given object.

.. function:: publishApprovedObject(obj)

   :param obj: approved object to publish.

   Publish the given object.

.. function:: openTestFile(filename, mode='rb')

   :param filename: name of the file in the ``data`` directory.
   :param mode: which mode the file should be opened.

   Open a test file, and return a file descriptor object.
