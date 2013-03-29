
.. _available-zope-schema-fields:

Available Zope schema fields
============================

Each Zope schema fields accept at least the following parameters:

``title``
   Title of the field, displayed in the form as field label. This must
   be a unicode string or a translated string.

``description``
   Description of the field, displayed in the form as field
   description. This must be a unicode string or a translated string.

``required``
   Boolean indicated if the field is required, or not.


General Zope schema fields
--------------------------

All the following schema fields are provided by default in Zope:

.. class:: zope.schema.Int

   Input a integer.


.. class:: zope.schema.Float

   Input a float number.


.. class:: zope.schema.Bool

   Input ``True`` or ``False``.


.. class:: zope.schema.Text

   Input a text, that might contains multiple line.


.. class:: zope.schema.TextLine

   Input a text, only one line.


.. class:: zope.schema.Password

   Input a password.


.. class:: zope.schema.URI

   Input a URL.


.. class:: zope.schema.Bytes

   Input a file.


.. class:: zope.schema.Date

   Input a :py:class:`datetime.date` object.


.. class:: zope.schema.Datetime

   Input a :py:class:`datetime.datetime` object. In the :term:`SMI`,
   this will render a date time popup widget using `JQueryUI`_.


.. class:: zope.schema.Time

   Input a :py:class:`datetime.time` object.


.. class:: zope.schema.Choice

   Select a choice among a fixed list of possibilities. Choice fields
   requires an ``source`` argument set to a :term:`Zope vocabulary`,
   expressing the different possible choices.

   If you want to have a multiple choice schema field, you need build
   a list (or tuple, set) of choice.

   .. seealso::

      - :ref:`defining-zope-vocabulary`

      - :ref:`collection-zope-schema`

.. glossary::

   *Zope vocabulary*
     A Zope vocabulary is a defined set of values that you can
     use. For each value, you have an associated *token* that is a
     serializable string that can be transmitted over HTTP, and a
     *title* that will be displayed to the user.


The useful package `z3c.schema`_ provides the following fields:

.. class:: z3c.schema.email.RFC822MailAddress

   Input and validate an email address.

Silva provides the following extra schema fields for use in the
:term:`SMI`:

.. class:: silva.core.conf.schema.ID

   Input a valid identifier for a new Silva content. When used in a
   form, the validation of this field will only succeed if the new
   identifier is not already in used in the nearest container where
   the form have accessed from and respect the criterias to be a valid
   identifier.

.. class:: silva.core.conf.schema.HTMLText

   Input rich text using a WYSIWYG editor with the help of
   ``silva.core.editor``.

.. class:: silva.core.references.schema.Reference

   Input a reference to a content. In :term:`SMI`, it will create a
   `JQueryUI`_ popup that will let you select a content to refer
   to. The widget will return a content reference id, than is
   understandable by the reference mechanism. You can use the
   parameter ``interface`` to restrict the content to one who
   implements the given :term:`Zope interface`.

.. _collection-zope-schema:

Zope schema fields for collections
----------------------------------

A collection fields let generate fields to input more than one value.
They have a required parameter ``value_type``, describing the type of
the contained value.

For example, to have a list of strings you will write:

.. code-block:: python

   names = schema.List(title=u"People  names",
                       value_type=schema.TextLine(required=True),
                       required=True)

For each collection type, usually a widget containing *Add* and
*Remove* buttons are generated, that let you add or remove values to
the collection.


Zope provides you with the following collection schema fields by
default:

.. class:: zope.schema.List

   Input  a list of values.


.. class:: zope.schema.Tuple

   Input a tuple of values.


.. class:: zope.schema.Set

   Input a set of values.


.. _defining-zope-vocabulary:

Defining a Zope vocabulary
--------------------------

If you want to define a new vocabulary, you have to build it using
``zope.schema.vocabulary.SimpleVocabulary`` and
``zope.schema.vocabulary.SimpleTerm``:

.. code-block:: python
   :linenos:

   from zope.schema.vocabulary import SimpleTerm
   from zope.schema.vocabulary import SimpleVocabulary

   @apply
   def weather_type():
       terms = []
       for value, token, title in [(1, 'sunny', u'Sunny'),
                                   (2, 'raining', u'Raining'),
                                   (3, 'snowing', u'Snowing')]:
           terms.append(SimpleTerm(value=value, token=token, title=title))
       return SimpleVocabulary(terms)


On line 4, we use the Python decorator ``apply`` to set as value to
``weather_type`` the result of calling it, i.e it will do:

.. code-block:: python

   weather_type = weather_type()

On lines 6 through 11 we create the vocabulary dynamically. Of course,
for a static vocabulary you could have wrote:

.. code-block:: python

   weather_type = SimpleVocabulary(
      SimpleTerm(value=1, token='sunny', title=u'Sunny'),
      SimpleTerm(value=2, token='raining', title=u'Raining'),
      SimpleTerm(value=1, token='sunny', title=u'Sunny'))


Defining a context-dependent Zope vocabulary
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Some vocabularies need to know where they are used in the site in
order to be able to provide a list of terms. You can implement them as
a function providing the interface
``zope.schema.interfaces.IContextSourceBinder``:

.. code-block:: python
   :linenos:

   from zope.schema.interfaces import IContextSourceBinder
   from five import grok

   @grok.provider(IContextSourceBinder)
   def addable_silva_types(context):
       terms = []
       for addable in context.get_container().get_silva_addables():
           terms.append(SimpleTerm(
               value=addable['instance'],
               token=addable['name'],
               title=addable['name']))
       return SimpleVocabulary(terms)

On line 4, we use the Grok Python decorator :py:func:`grok.provider`
to register the fact that our function implement the
``IContextSourceBinder`` interface.

On line 5, we define our vocabulary, as a function who takes one
argument, ``context`` which will in case of a form its context, the
content object on which the form have been called.

On lines 6 to 12 we dynamically construct the vocabulary using values
fetched from the ``context`` content object.

.. _JQueryUI: http://jqueryui.com/
.. _z3c.schema: http://pypi.python.org/pypi/z3c.schema
