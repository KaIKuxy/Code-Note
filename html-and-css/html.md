# HTML

## Useful Resource

[HTML tutorials from w3schools](https://www.w3schools.com/html/default.asp)

[HTML docs from MDN](https://developer.mozilla.org/en-US/docs/Web/HTML)

Hypertext Markup Language \(HTML\) is a language in which one can describe:

* The display and format of text
* The display of graphics
* Pointers to other html files
* Pointer to files containing graphics, digitzed video and sound
* Forms that capture information from the viewer

[HTML Living Standard](https://html.spec.whatwg.org/)

## Basic structure

HTML documents have a head and a body.

A leadling line indicates which version of HTML this document conforms to.

```markup
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    
</body>
</html>
```

Browsers will never throw an error. \(e.g. ingore markups / tags they don't understand\)

E.g. Internet Explorer / Firefox are tolerant browsers

* They do not insist that the HTML document begin and end with &lt;HTML&gt;
* &lt;HEAD&gt; and / or &lt;BODY&gt; tags are not required
* But, there is no guarantee that this behavior will be the same for all browsers

Each element consists of a start tag, content, and / or an end tag

Attributes have the form name=value, names are casesensitive, values are not.

Comments start with `<!--` and end with `-->`.

Comments cannot be nested.

## Lists

* [Unordered list &lt;ul&gt;](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/ul)
* [Ordered list &lt;ol&gt;](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/ol)
* [Definition list &lt;dl&gt;](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dl)
* [Menu &lt;menu&gt; ](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/menu) \(**Menu button is not implemeneted by any known browsers yet**\)

For ordered list, START attribute can initialize the sequence to a number other than 1, TYPE attribute can be used to select the numbering style.

All lists follow the same pattern:

```markup
<ul> <!-- or <ol> -->
    <li></li> <!-- a list item -->
    <li></li>
</ul>

<dl>
    <dt>Beast of Bodmin</dt> <!-- a definition term -->
    <dd>A large feline inhabiting Bodmin Moor.</dd> <!-- a definition description -->

    <dt>Morgawr</dt>
    <dd>A sea serpent.</dd>

    <dt>Owlman</dt>
    <dd>A giant owl-like creature.</dd>
</dl>

<!-- A button, which displays a menu when clicked. -->
<button type="menu" menu="popup-menu">
  Dropdown
</button>

<menu type="context" id="popup-menu">
  <menuitem>Action</menuitem>
  <menuitem>Another action</menuitem>
  <hr/>
  <menuitem>Separated action</menuitem>
</menu>
```

## Table

`<table>`, a tag used to define a table

`<caption>`, a tag to label a table Its attributes are ALIGN = top, bottom, left, right

`<th></th>` or `<td></td>`, tags that identify a table header cell and table data cell Headers are the same as data except they use bold font and are centered

`<tr></tr>`, a tag that identifies a container for a row of table cells Same attributes as TH and TD

## Character

Character references in HTML appear in two forms:

1. Numeric character references \(either decimal or hexadecimal\) \(&\#34\)
2. Character entity references \(&quote\)

## Anchor

Anchor can link to the other part of the same document. 

The id attribute may be used to create an anchor at the start tag of any element \(including the A element\).

```markup
You may read more about this in 
<A href="#section2">Section Two</A>.
. . . more text . . . 
<H2 id="section2">Section Two</H2>
. . . more text
<P>Please refer to <A href="#section2">Section Two</A> above for more details.
```

### Universal Resource Identifier

URIs typically consist of three pieces:

1. The scheme of the mechanism used to access the resource.
2. The name of the machine hosting the resource.
3. The name of the resource itself, given as a path

E.g. http://www.usc.edu/dept/cs/index.html

**Fragment identifiers** are URIs that refer to a location within a resource, e.g. http://www.usc.edu/dept/cs/index.html\#section2

## Form

For checkboxs which have same name, the 

