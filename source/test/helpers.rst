
Helpers methods
===============

Some helpers can be used in your test to accomplish some common actions:

.. module:: Products.Silva.tests.helpers

.. function:: enablePreview()

   Enable the rendering of the preview instead of the normal view.

.. function:: resetPreview()

   Reset preview mode.

.. function:: approve_object(obj)

   :param obj: object to approve

   Approve the given object.

.. function:: publish_object(obj)

   :param obj: content to publish

   Publish the given object.

.. function:: publishApprovedObject(obj)

   :param obj: approved object to publish

   Publish the given object.

.. function:: open_test_file(filename, globs=None, mode='rb')

   :param filename: name of the file in the ``data`` sub-directory of the current python module
   :param globs: globals of the current python module to determine the position of the ``data`` sub-directory
   :param mode: which mode the file should be opened
   :return: An opened Python file object to the selected file

   Open a test file, and return a file descriptor object.
