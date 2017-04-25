---
layout: docs
title: Templates
short_title: Templates
---

This section explains how the `template` and `table` blocks are used
in **docassemble** interviews.  If you are interested in document
templates, see [documents].  If you are interested in how to insert
variables into the text of your questions or documents, see [markup].

# <a name="template"></a>The `template` block

A `template` allows you assign text to a variable and then re-use the
text by referring to a variable.

{% include side-by-side.html demo="template" %}

The `content` of a `template` may contain [Mako] and [Markdown].

The name after `template:` is a [variable name] that you can refer to
elsewhere.

The `template` block, like [`question`] and [`code`] blocks, offers to
define a variable.  So when **docassemble** needs to know the
definition of `disclaimer` and finds that `disclaimer` is not defined,
it will look for a [`question`], [`code`], or `template` block that offers
to define `disclaimer`.  If it finds the `template` block above, it
will define the `disclaimer` variable.

A template, once defined, is a variable of type [`DATemplate`].
However, this variable will be undefined each time the screen loads.
Therefore, if the variables that the template depends on are
redefined, the template will change accordingly.

Optionally, a `template` can have a `subject`:

{% include side-by-side.html demo="template-subject" %}

You can refer to the two parts of the template by writing, e.g.,
`disclaimer.subject` and `disclaimer.content`.

Note that writing `${ disclaimer }` has the same effect as writing `${
disclaimer.content }`.

Templates are also useful for defining the content of e-mails.  See
[`send_email()`] for more information on using templates with e-mails.

You might prefer to write text in [Markdown] files, rather than in
[Markdown] embedded within [YAML].  To facilitate this,
**docassemble** allows you to create a `template` that references a
separate [Markdown] file.

{% include side-by-side.html demo="template-file" %}

The file [`disclaimer.md`] is a simple [Markdown] file containing the
disclaimer from the previous example.

The `source file` is assumed to refer to a file in the "templates"
folder of the same package as the interview source, unless a specific
package name is indicated.  (e.g., `source file:`
[`docassemble.demo:data/templates/hello_template.md`])

In the example above, the sample interview is in the file
[`docassemble.base:data/questions/examples/template-file.yml`], while
the [Markdown] file is located at
[`docassemble.base:data/templates/disclaimer.md`].

# <a name="table"></a>The `table` block

The `table` works in much the same way as a `template`, except its
content is a table that will be formatted appropriately whether it is
included in a [question] or in a [document].

This block should be used when each row of your table represents an
item in a [group]; that is, you do not know how many rows the table
will contain, because the information is in a [list], [dictionary], or
[set].  If you just want to format some text in a table format, see
the documentation about [tables] in the [markup] section.

In the following example, the variable `fruit` is a list of objects of
type [`Thing`], each of which represents a fruit.  Each row in the
resulting table will describe one of the fruits.

{% include side-by-side.html demo="table" %}

The `table: fruit_table` line indicates the name of the variable that
will hold the template for table.  The [`question`] block includes the
table simply by referring to the variable `fruit_table`.

The `rows: fruit` line indicates the variable containing the [group]
of items that represent rows in the table.  The `fruit` variable is a
[`DAList`] that gets populated during the interview.

Next, `columns` describes the header of each column and what should be
printed in each cell under that header.  Like a [`fields`] list within
a [`question`], `columns` must contain a [YAML] list where each item
is a key/value pair (a one-item dictionary) where the key is the
header of the column and the value is a [Python] expression
representing the contents of the cell for that column, for a given row.

In the example above, the header of the first column is "Fruit Name"
and the [Python] expression that produces the name of the fruit is
`row_item.name`.

There are two special variables available to these [Python]
expressions:

* `row_item`: this is the item in the [group] corresponding to the
  current row.
* `row_index`: this is `0` for the first row, `1` for the second row,
  `2` for the third row, etc. 

You can pretend that the [Python] expressions are evaluated in a
context like this:

{% highlight python %}
row_index = 0
for row_item in fruit:
  # evaluation takes place here
  row_index = row_index + 1
{% endhighlight %}

In this example, the first column will show name of the fruit
(`row_item.name`) and the second column will show the number of seeds
(`row_item.seeds`).

The header of each column is plain text (not a [Python] expression).
The header can include [Mako] and [Markdown].

If you have a complicated header, you can use the special keys
`header` and `cell` to describe the header and the cell separately.
(This is similar to using [`label` and `field`] within a [`fields`]
list.)

{% include side-by-side.html demo="table-alt" %}

You can use [Python] to create cells with content that is computed
from the items of a [group].

{% include side-by-side.html demo="table-python" %}

The above example prints the name of the fruit as a plural noun, and
inflates the number of seeds.

Remember that the [Python] code here is an [expression], not a block
of code.  If you want to use if/then/else logic in a cell, you will
need to use [Python]'s one-line form of if/then/else:

{% include side-by-side.html demo="table-if-then" %}

When `fruit_table` is inserted into the [`question`], the result will
be a [Markdown]-formatted table.

This:

{% highlight yaml %}
question: |
  Information about fruit
subquestion: |
  Here is a fruity summary.

  ${ fruit_table }
{% endhighlight %}

will have the effect of this:

{% highlight yaml %}
question: |
  Information about fruit
subquestion: |
  Here is a fruity summary.

  Fruit Name |Number of Seeds
  -----------|---------------
  Apples     |4
  Oranges    |3
  Pears      |6
{% endhighlight %}

For more information about [Markdown]-formatted tables, see the
documentation about [tables] in the [markup] section.

Instead of using a `table` block, you could construct your own
[Markdown] tables manually using a [Mako] "for" loop.  For example:

{% include side-by-side.html demo="table-mako" %}

The advantages of using the `table` block are:

* The `table` block describes the content of a table in a conceptual
  rather than visual way.  In [Markdown], simple tables look simple,
  but complicated tables can look messy.  The `table` block allows you
  to map out your ideas in outline form rather than squeezing
  everything into a single line that has a lot of punctuation marks.
* The `table` block will attempt to set the relative table widths in a
  sensible way based on the actual contents of the table.  If you
  create your own tables in [Markdown], and the text in any cell
  wraps, the relative table widths of the columns will be decided
  based on the relative widths of the cells in the divider row
  (`----|---------`).  You might not know in advance what the relative
  sizes of the text will be in each column.

The `table` block acts like a `template` block in that the variable it
sets will be a [`DATemplate`] object.  The `.content` attribute will
be set to the text of the table in [Markdown] format.

If the variable indicated by `rows` is empty, the table will display
with only the headers.  To suppress this, you can add `show if empty:
False` to the `table` block.  The resulting `.content` will be the
empty string, `""`.

{% include side-by-side.html demo="table-empty" %}

If you would like a message to display in place of the table in the
event that there are no `rows` to display, you can set `show if empty`
to this message.  [Mako] and [Markdown] can be used.  The message will
become the `.content` of the resulting [`DATemplate`].

{% include side-by-side.html demo="table-empty-message" %}

If you include a table in the content of an [`attachment`], you might
find that the table is too wide, or not wide enough.  [Pandoc] breaks
lines, determines the relative width of columns, and determines the
final width of a table based on the characters in the divider row
(`----|---------`).

By default, **docassemble** will construct a divider row is no longer
than 65 characters.  This should work for standard applications (12
point font, letter size paper).

You can change the number of characters from 65 to something else by
setting value of [`table width`] in a [`features`] block.

{% include side-by-side.html demo="table-width" %}

[Pandoc]: http://johnmacfarlane.net/pandoc/
[`table width`]: {{ site.baseurl }}/docs/initial.html#table width
[`features`]: {{ site.baseurl }}/docs/initial.html#features
[`attachment`]: {{ site.baseurl }}/docs/documents.html#attachment
[expression]: http://stackoverflow.com/questions/4782590/what-is-an-expression-in-python
[`fields`]: {{ site.baseurl }}/docs/fields.html#fields
[tables]: {{ site.baseurl }}/docs/markup.html#tables
[group]: {{ site.baseurl }}/docs/groups.html
[`docassemble.demo:data/templates/hello_template.md`]: {{ site.github.repository_url }}/blob/master/docassemble_demo/docassemble/demo/data/templates/hello_template.md
[`docassemble.base:data/questions/examples/template-file.yml`]: {{ site.github.repository_url }}/blob/master/docassemble_base/docassemble/base/data/questions/examples/template-file.yml
[`docassemble.base:data/templates/disclaimer.md`]: {{ site.github.repository_url }}/blob/master/docassemble_base/docassemble/base/data/templates/disclaimer.md
[`disclaimer.md`]: {{ site.github.repository_url }}/blob/master/docassemble_base/docassemble/base/data/templates/disclaimer.md
[markup]: {{ site.baseurl }}/docs/markup.html
[document]: {{ site.baseurl }}/docs/documents.html
[documents]: {{ site.baseurl }}/docs/documents.html
[Mako]: http://www.makotemplates.org/
[Markdown]: https://daringfireball.net/projects/markdown/
[Python]: https://www.python.org/
[`DATemplate`]: {{ site.baseurl }}/docs/objects.html#DATemplate
[`send_email()`]: {{ site.baseurl }}/docs/functions.html#send_email
[`question`]: {{ site.baseurl }}/docs/questions.html#question
[question]: {{ site.baseurl }}/docs/questions.html
[`code`]: {{ site.baseurl }}/docs/code.html#code
[variable name]: {{ site.baseurl }}/docs/fields.html#variable names
[YAML]: https://en.wikipedia.org/wiki/YAML
[`DAList`]: {{ site.baseurl }}/docs/objects.html#DAList
[`Thing`]:  {{ site.baseurl }}/docs/objects.html#Thing
[list]: https://docs.python.org/2.7/tutorial/datastructures.html
[dictionary]: https://docs.python.org/2/library/stdtypes.html#dict
[set]: https://docs.python.org/2/library/stdtypes.html#set
[`label` and `field`]: {{ site.baseurl }}/docs/fields.html#label