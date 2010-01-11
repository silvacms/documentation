Using your Silva instance
=========================

.. contents::

.. _starting-creating-silva-site:

Starting your instance and creating a Silva site
------------------------------------------------

After having installed your Silva environment, you should get a
``bin/instance`` script in your installation directory. You can use to
start the Zope instance:

.. code-block:: sh

   $ bin/instance start

After few seconds, the server should be running, so open a web browser and
type in the server location:

    http://localhost:8080

This should return the 'Zope Quick Start' page. You can log into the
Zope administration interface (*ZMI*) by adding ``manage_main`` to the
URL:

    http://localhost:8080/manage_main

Zope will prompt you for a username and password. Fill in the values
you choose in the paster configuration step, and press the *OK*
button. If you didn't fill anything while installing Silva, or choose
the default values, those are username ``admin`` and password ``admin``.

Now you are in the ZMI. Let's add a Silva site by selecting *Silva
Root* from the drop down list on the top right of the screen.

You should get a *Add Silva Root* page, fill in an id (f.e. ``silva``)
and a title (f.e. ``My Silva Site``). You can check the *Add Silva
Documentation* checkbox, so the Silva documentation get installed by
default.  Now click the *Add and Edit* button, this may take a while
(if *Add Silva Documentation* was chosen).

Now you are back in the Zope Management Interface, but inside of the
Silva site (check your URL). You can click on the *Silva / edit...*
tab to enter the Silva Management Interface (SMI).

Here you can add and edit your content. If you installed the
documentation, you should be able to click on *Silva Documentation* on
the left of the screen to see some example content.

On the bottom right there is a link called *view public version*. You
can click on this link to see how this page looks in the public
website.

You can go back to the *Silva Management Interface* at anytime by
adding ``/edit`` to the URL displayed in your web browser.

.. note::

   You can add more than one *Silva Root* (or Silva instance) in one
   Zope instance and use them independently.


Stopping and restarting your Silva Site
---------------------------------------

You can stop your Silva instance with:

.. code-block:: sh

   $ ./bin/instance stop

You can restart directly the instance with:

.. code-block:: sh

   $ ./bin/instance restart

And the following command can tell you if Silva is running.

.. code-block:: sh

   $ ./bin/instance status

