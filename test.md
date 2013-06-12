Welcome to Markdown test
====
This page is designed to test the capabilities of markdown parser.

Introduction {#intro}
----
This is a sample section, however it should be using anchor `intro`

<div markdown="1">
This is *true* markdown text.
</div>

Code Highlighting {#code}
----
Inline code highlighting: `$this->add('AgileToolkit');`

Code highlighting by indent:

    function foo(){
        do_something_sensible();
    }

Code highlighting by github ticks:

```
function bar(){
    do_something_bad();
}
```

Code highlighting by HTML pre tags (for code examples in lists)

<pre>
function bar(){
    do_something_sinful();
}
</pre>

Some text might be *italic* or **bold**

We can also have [Links to other pages](http://yahoo.com/)

And we can have a link to the API docs: {api:DB_dsql}

Paragraph test
----

### Header H3

#### Header H4

##### Header H5

###### Header H6

Item List
----
I like the following things:

- cars
- girls
- money
  - dollars
  - euros  
  - sterling

1. Numbered
1. List
1. Also
1. Works


Quotes
----

I said, what is this?
> the man replied, this is the Matrix
> why don't you plug-in
> > No thanks, I told him
> > No thanks, I told him
> > No thanks, I told him

Code Samples:
----
This is a paragraph introducing code blocks:

<?Example?>
$form=$page->add('Form');
$form->addField('line','name');
$form->addField('line','surname');
$form->addSubmit();
<?/?>

Table test
----

| Function name | Description                    |
| ------------- | ------------------------------ |
| `help()`      | Things aren't going so well    |
| `destroy()`   | **Destroy your computer!**     |
