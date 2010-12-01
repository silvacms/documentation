
Adding forms to your content
============================

To be able to add your content from the :term:`SMI`, you need to
create an *add form*. Probably you want to have an *edit form* to
modify your content as well.

.. contents::

Creating a form description
---------------------------

To create a form, you need first to create a :term:`Zope interface`
that will describe which fields your form should contains, using
:term:`Zope schema fields`.

.. glossary::

   *Zope schema fields*
     A Zope schema field is a :term:`Zope interface` attribute that
     describe the type of this attribute (is it a string, an
     integer...), and gives information about its properties (is it
     required, what is its English name or label...). Those schema
     fields are a particularly useful standard way to describe form
     fields.

For instance, let's create an interface to describe a form to enter a
comment:

.. code-block:: python
   :linenos:

   from zope import interface, schema
   from z3c.schema.email import RFC822MailAddress

   class ICommentFields(interface.Interface):
       """Describe a comment.
       """
       name = schema.TextLine(title=u"Your name", required=True)
       comment = schema.Text(title=u"Comment", required=True)
       email = RFC822MailAddress(title=u"Email", required=True)


On line 4, we create our :term:`Zope interface` as a subclass of
``zope.interface.Interface``. From line 7 to 9 we define the form
fields using the :term:`Zope schema fields`.

Please refer to :ref:`available-zope-schema-fields` for a list of
commonly used :term:`Zope schema fields`.

In case of an add form, you can make your field definition interface
inherit from those interfaces, who defined an ``id`` and a ``title``
for your content:

.. autointerface:: silva.core.conf.interfaces.IIdentifiedContent

.. autointerface:: silva.core.conf.interfaces.ITitledContent


Creating an add form
--------------------

To create an add form to add new content in the :term:`SMI`, you need
a content to add and a form description describing content fields.

For instance, you can create a
:py:class:`~Products.Silva.VersionedContent.VersionedContent` to store
a comment.


After you can create an add form for this content:

.. code-block:: python
   :linenos:

   from five import grok
   from silva.core.conf.interfaces import ITitledContent
   from zeam.form import silva as silvaforms

   class CommentAddForm(silvaforms.SMIAddForm):
       """Comment add form.
       """
       grok.context(Comment)
       grok.name('Comment')

       fields = silvaforms.Fields(ITitledContent, ICommentFields)

On line 5, we create the add form by inheriting from
:py:class:`~zeam.form.silva.SMIAddForm`. That class will be read by
:term:`Grok` and registered as an :term:`SMI` add form. That *needs*
to have a docstring, as in Zope 2, all published classes need to have
a docstring.

On line 9, we use the :term:`Grok directive` :py:func:`grok.name` to
associated our add form to the content: its name **must** be the same
value than the ``meta_type`` attribute of the content object. This
usage of ``grok.name`` is specific to the add form only.

On line 11, we declare the fields of the add form, first we add the
``id`` and ``title`` using the
:py:interface:`~silva.core.conf.interfaces.ITitledContent` interface,
and after the content fields we declare in the previous
example. Please note that fields will be shown in the order they have
been defined, so ``id`` and ``title`` will appear first, before our
content fields ``name``, ``comment`` and ``email`` at last.

You don't write any other Python or Template code to have your working
add form.

.. note::

   In case of a
   :py:class:`~Products.Silva.VersionedContent.VersionedContent`, the
   :py:class:`~Products.Silva.Version.Version` will be initialized
   with the form values, not the
   :py:class:`~Products.Silva.VersionedContent.VersionedContent`
   itself.

By default the add form will do all the validation, the add and
edition work for us. However you can still customize the adding
process you can do it:

.. class:: zeam.form.silva.SMIAddForm

   Create an add form. The form already have *Cancel*, *Save* and
   *Save and edit* actions.

   The form will effectively add, and edit the content.

   .. attribute:: fields

      Form fields for ``id``, ``title``, and other content value.

   .. method:: _add(parent, data)

      Override this method if you whish to customize the creation of
      the content.

      :parameter parent: container in which the new content must be added.
      :parameter data: form values to initialize the new content with.
      :type parent: :py:interface:`~silva.core.interfaces.content.IContainer`
      :type data: :py:class:`dict`
      :return: the new content correctly wrapper in Acquisition.
      :raise: in case of error, raise :py:exc:`ValueError`

   .. method:: _edit(parent, content, data)

      Overide this method, if you didn't override :py:meth:`_add`
      and whish to customize the edition of the new content.

      :parameter parent: container in which the new content have been added.
      :parameter content: new created content.
      :parameter data: form values to initialize the new content with.
      :type parent: :py:interface:`~silva.core.interfaces.content.IContainer`
      :type content: :py:interface:`~silva.core.interfaces.content.ISilvaObject`
      :type data: :py:class:`dict`
      :raise: in case of error, raise :py:exc:`ValueError`


.. warning::

   Since Silva 2.3, this is **only** supported way to create an add
   form for you content.


Creating an edit form
---------------------

To create an edit form in :term:`SMI`, you need your content to edit
and a form description describing content fields. You can reuse the
same content description than the add form, knowing that:

- You don't need the ``id`` field anymore, as a content edit form
  doesn't rename the content.

- Often the content title is modified in the metadata screen of Silva.

So you can create your edit form:

.. code-block:: python
   :linenos:

   class CommentEditForm(silvaforms.SMIEditForm):
       """Comment edit form.
       """
       grok.context(Comment)

       fields = silvaforms.Fields(ICommentFields)


On line 1, we create the edit form by inheriting from
:py:class:`~zeam.form.silva.SMIEditForm`. Like for the add
form, this class will be read by :term:`Grok` and register as an
:term:`SMI` edit form for you content. Like for the add form, the
class *needs* to have a docstring to be published (Zope 2
requirement).

On line 4, we use the :term:`Grok directive` :py:func:`grok.context`
to associate our edit form to our content. We don't change here the
name of the form using the :term:`Grok directive`
:py:func:`grok.name`, which by default is ``tab_edit`` (default
:term:`SMI` edit form name).

On line 6, we define which fields will be edited. Unlike for the add
form, we didn't used
:py:interface:`~silva.core.conf.interfaces.ITitledContent`, we only
used our content fields (``name``, ``comment`` and ``email``).

Like for add form, no additional Python or Template is required to get
a working edit form.

.. note::

   In case of a
   :py:class:`~Products.Silva.VersionedContent.VersionedContent`, the
   currently editable :py:class:`~Products.Silva.Version.Version` will
   edited, not the
   :py:class:`~Products.Silva.VersionedContent.VersionedContent`
   itself. If there is no editable version, the current published
   version will be displayed, an action will propose you to create a
   new Version to edit it.

.. class:: zeam.form.silva.SMIEditForm

   Create an edit form. The form already have *Cancel* and *Save*
   actions.

   .. attribute:: fields

      Form fields to edit content values.


Creating a public form
----------------------

A public form is a form that won't be displayed in the :term:`SMI` but
to the public as a public view. Like for a public view, the layout
system will include the layout around the form.

For this create your form by inheriting from:

.. class:: zeam.form.silva.PublicForm

   Create a form for the public front-end of the site.

   .. attribute:: label

      Form title (unicode or unicode translatable string).

   .. attribute:: description

      Form description (unicode or unicode translatable string).

   .. attribute:: fields

      Form fields. Your form is not required to have fields.

   .. attribute:: actions

      Form actions. Your form is not required to define actions using
      this attribute.

   The next API can be used by actions to work on the form:

   .. attribute:: status

      Feedback message that can be set on the form by an action.

   .. attribute:: formErrors

      List of errors who append while extracting the form data. It is empty
      before :py:meth:`extractData` is called.

   .. method:: extractData()

      Method called by an action to extract the form data.

      :return: a tuple containing the data and a list of potential errors.


As example, we can make a contact form. We need first a form description:

.. code-block:: python
   :linenos:

   from zope import interface, schema
   from zope.schema.vocabulary import SimpleVocabulary, SimpleTerm
   from z3c.schema.email import RFC822MailAddress

   question_type = SimpleVocabulary(
       SimpleTerm(term='support', token='support', title=u'Technical Support'),
       SimpleTerm(term='training', token='training', title=u'Training'),
       SimpleTerm(term='marketing', token='marketing', title=u'Marketing'),
       SimpleTerm(term='commercial', token='commercial', title=u'Commercial'))

   class IContactFormFields(interface.Interface):
       """Contact form fields.
       """
       name = schema.TextLine(title=u"Name", required=True)
       company_name = schema.TextLine(title=u"Company", request=False)
       question = schema.Text(title=u"Question", required=True)
       question_type = schema.Choice(title=u"Question", source=question_type, required=False)
       email = RFC822MailAddress(title=u"Email", required=True)
       phone = schema.TextLine(title=u"Phone", required=False)

On line 5 to 9 we define a vocabulary to use in our form description
(on line 17). For details refer to :ref:`defining-zope-vocabulary`.

On line 11 to 19, we define our form description, that we can use to
create a form with an action ``Contact``:

.. code-block:: python
   :linenos:

   from zeam.form import silva as silvaforms

   class ContactForm(silvaforms.PublicForm):
        """Contact form.
        """
        grok.name('contact.html')

        label = u'Contact'
        description = u'You can contact us using this form'
        fields = silvaforms.Fields(IContactFormFields)

        @silvaforms.action(u"Contact us!", identifier='contact', accesskey='c')
        def contact(self):
            data, errors = self.extractData()
            if errors:
                self.status = u'Please correct the errors.'
                return silvaforms.FAILURE
            # Send some mail here for instance.
            self.status = u'Thank you for your request.'
            return silvaforms.SUCCESS

On line 3, we define our form by inheriting from
:py:class:`~zeam.form.silva.PublicForm`. Like for other forms,
:term:`Grok` will read our class and register it.

On line 6, we use the :term:`Grok directive` :py:func:`grok.name` to
name our form ``contact.html``: it will be available at the URL
``/contact.html`` appended to any Silva content object URL.

On line 8, we set a title, on 9 a description. On line 10 we create
the form fields out of the field definition we defined before.

On 12 to 20, we define a new form action *Contact us!*. The action is
registered with the help of the decorator on line 12. On line 14, we
extract the form data entered by the user from the Zope request, if
there was an error, we set accordingly a status message on the form,
and return a failure marker. If everything went alright, we have a
Python :py:class:`dict` containing the form data, that we can work with.

Apart from the class which you have to inherit from, public form works
the same way than the other forms.

Embedding a public form in a existing view
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can embbed a form as a :term:`Content Provider` or a
:term:`Viewlet` in a public view. To do this you can create your form
by inheriting from one of those classes instead of
:py:class:`~zeam.form.silva.PublicForm`:

.. class:: zeam.form.silva.PublicContentProviderForm

   Create a form in a :term:`Content Provider`.


.. class:: zeam.form.silva.PublicViewletForm

   Create a form in a :term:`Viewlet`.

Of course, you will to use the :term:`Grok directive`
:py:func:`grok.view` and possibly :py:func:`grok.viewletmanager` to
register them correctly.

.. seealso::

   :ref:`creating-a-default-view`


Using more complex forms
------------------------

More complicated forms can be created, like it is done for the access
tab in the :term:`SMI`.

Complex form components that can be used in the :term:`SMI` are:

.. class:: zeam.form.silva.SMIComposedForm

   Create a composed form: the form is composed of other sub forms,
   that can interact between each of them.

.. class:: zeam.form.silva.SMISubForm

   Create a sub form. It have to be associated to a
   :py:class:`~zeam.form.silva.SMIComposedForm` or a
   :py:class:`~zeam.form.silva.SMISubFormGroup` in order to be used.

.. class:: zeam.form.silva.SMISubFormGroup

   Create a group of sub form inside a
   :py:class:`~zeam.form.silva.SMIComposedForm`.

.. class:: zeam.form.silva.SMISubTableForm

   Create a sub form that can work on multiple items at once. This
   create a table in the user interface, with each table columns
   representing each form fields, and each line representing each
   content.  This form can host table actions, that are executed
   against each selected lines upon form submission.

   Like for :py:class:`~zeam.form.silva.SMISubForm`, it have to be
   associated to a :py:class:`~zeam.form.silva.SMIComposedForm` or a
   :py:class:`~zeam.form.silva.SMISubFormGroup` in order to be used.

.. class:: zeam.form.silva.SMIViewletForm

   Create a form as a :term:`Viewlet` in the :term:`SMI`.

.. class:: zeam.form.silva.SMIContentProviderForm

   Create a form as a :term:`Content Provider` in the :term:`SMI`.


Customizing default forms
-------------------------

For each form public or :term:`SMI`, it is possible to add and remove
default actions, change rendering templates, change default form
*label* or *description*.

