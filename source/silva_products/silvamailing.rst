Silva Mailing
=============

Requirements
------------

SilvaMailing allows users to create mailing lists. SilvaMailing is
valid for Silva-1.6 and Silva-2.x.

Install
-------

See :doc:`genericinstallation`

Configuration
-------------

In the Silva root navigate to the Services tab and click into the
service_extensions. Find the SilvaMailing product and click
install. Return to the Silva root and click into the ``Silva
/edit...`` view. Create a Silva Mailing Folder. Navigate into the
Silva Mailing Folder object and click properties. Edit the mailhost id
to point to service_subscription_mailhost. (Note, the
service_subscription_mailhost should be changed to the current mail
server being used. Click save.

Using
-----

Now the Silva Mailing product should be configured. After a Mailing
Folder object has been placed and configured properly, users can start
creating 'Silva Mailing Document' type objects inside it. These
documents each provide a mailing, with the contents of a single HTML
mail. To send a mailing, click on the mail document to get into
the 'editor' view. From here click on the publish tab and then the
'send mailing' button.

This box also provides a 'test email' field in which you can enter an
email address to send the mail to for testing purposes - in case this
is provided, email will not be sent off to the subscribers, only to
this address.