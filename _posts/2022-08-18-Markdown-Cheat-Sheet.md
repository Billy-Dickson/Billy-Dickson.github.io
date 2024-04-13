---
title:  "Markdown Cheatsheet"
date:   2022-08-17
categories: [Homelab, Linux, Web]
tags: [homelab, linux, web]
image:
    path: ../assets/img/posts/2022-08-18-Markdown-Cheat-Sheet/Cheat-Sheet.webp
---

This page is intended as a quick reference to help me while I'm writing my blogs, for more complete information see [John Gruber's original specification](https://daringfireball.net/projects/markdown/).

## Phrase emphasis

~~~md
*italic* and **bold**
~~~

*italic* and **bold**

## Links

**Inline** (titles are options):

~~~md
Some [linked to](https://example.com/)
~~~

Some [linked to](https://example.com/)

**Reference-style labels** (titles are optional):

~~~md
An [example][id].
~~~

Then, anywhere else **in the same block**, define the link:

~~~md
[id]: https://example.com/ "Title"
~~~

[example]: https://example.com/ "Title"

An [example]]

:bulb: You can use whatever you want to in place of "id". Just ensure that they match.
{: .notice--info}

### Formatting Links

To emphasize links, add asterisks before and after the brackets and parentheses. To denote links as code, add backticks in the brackets.

~~~html
I love supporting the **[EFF](https://eff.org)**.\
This is the *[Markdown Guide](https://www.markdownguide.org)*.\
See the section on [`code`](#code).
~~~~

The rendered output looks like this

I love supporting the **[EFF](https://eff.org)**.\
This is the *[Markdown Guide](https://www.markdownguide.org)*.\
See the section on [`code`](#code).

## Superscript and Subscript

Bare in mind that Markdown doesn't support Superscript or Subscript natively, this is an html workaround. You might get an [error message](https://github.com/DavidAnson/markdownlint/blob/v0.25.1/doc/Rules.md#md033) depending on the editor you are using, I'm using [Visual Studio Code](https://code.visualstudio.com/docs/setup/linux) on [Pop!_OS](https://pop.system76.com) which has been my daily driver for a year or so.

### Superscript

~~~html
E=MC<sup>2</sup>
~~~

E=MC<sup>2</sup>

### Subscript

~~~ html
Plants need CO<sub>2</sub>
~~~

Plants need CO<sub>2</sub>

## Images

Inline (titles are optional):

~~~md
![alt text](../assets/img/posts/2022-08-18-Markdown-Cheat-Sheet/tux.png "Title")
~~~

![alt text](../assets/img/posts/2022-08-18-Markdown-Cheat-Sheet/tux.png "Title")

Reference-style:

~~~md
![alt text][id]
~~~

~~~md
[id]: ../assets/img/posts/2022-08-18-Markdown-Cheat-Sheet/tux.png "Title"
~~~

[tux]: ../assets/img/posts/2022-08-18-Markdown-Cheat-Sheet/tux.png "Title"
![alt text][tux]

## Headings

~~~md
## Heading 2

### Heading 3
~~~

## Heading 2

[The standard Lorem Ipsum passage](https://www.lipsum.com/), used since the 1500s
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."

### Heading 3

[The standard Lorem Ipsum passage](https://www.lipsum.com/), used since the 1500s
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."

## Lists

### Ordered, without paragraphs

~~~ md
1. Foo
2. Bar
~~~

1. Foo
2. Bar

### **Unordered**, with paragraphs

~~~md
* A list item.  
With multiple paragraphs.
* Bar
~~~

* A list item.  
With multiple paragraphs.
* Bar

### Nested

~~~md
* Abacus
  * absolute
* Bananas
    1. bitter
    2. bupkis
    3. burper
* Cunning
~~~

* Abacus
  * absolute
* Bananas
    1. bitter
    2. bupkis
    3. burper
* Cunning

## Blockquotes

~~~md
> Email-style angle brackets are used for blockquotes.
>> You can also nest them.
>>
> * You can quote a list.
> * Etc.

> To break the nested blockquote, add a space between lines.

Add another line to resume regular paragraph text.
~~~

> Email-style angle brackets are used for blockquotes.
>> You can also nest them.
>>
> * You can quote a list.
> * Etc.
> * Etc.
> To break the nested blockquote, add a space between lines.

Add another line to resume regular paragraph text.

## Extended Syntax

These elements extend the basic syntax by adding additional features. Not all Markdown applications support these elements, thankfully Jekyll does.

### Symbols

Markdown doesn’t provide special syntax for symbols. However, in most cases, you can copy and paste whatever symbol you want to use into your Markdown document. For example, if you need to display Pi (π), just find the symbol on a webpage and copy and paste it into your document. The symbol should appear as expected in the rendered output.

Alternatively, if your Markdown application supports HTML, you can use the HTML entity for whatever symbol you want to use. For example, if you want to display the copyright sign (©), you can copy and paste the HTML entity for copyright (&copy;) into your Markdown document.

Here’s a partial list of HTML entities for symbols:

~~~html
Copyright (©) — &copy;
Registered trademark (®) — &reg;
Trademark (™) — &trade;
Euro (€) — &euro;
Left arrow (←) — &larr;
Up arrow (↑) — &uarr;
Right arrow (→) — &rarr;
Down arrow (↓) — &darr;
Degree (°) — &#176;
Pi (π) — &#960;
~~~

For a complete list of available HTML entities, refer to Wikipedia’s page on [HTML entities](https://en.wikipedia.org/wiki/List_of_XML_and_HTML_character_entity_references).

### Tables

To add a table, use three or more hyphens to create each column’s header, and use pipes to separate each column. For compatibility, you should also add a pipe on either end of the row.

~~~md
| Syntax      | Description |
| ----------- | ----------- |
| Header      | Title       |
| Paragraph   | Text        |
~~~

The rendered output looks like this

| Syntax      | Description |
| ----------- | ----------- |
| Header      | Title       |
| Paragraph   | Text        |

Cell widths can vary, as shown below. The rendered output will look the same.

~~~md
| Syntax | Description |
| --- | ----------- |
| Header | Title |
| Paragraph | Text |
~~~

### Line Breaks Within Table Cells

You can separate paragraphs within a table cell by using one or more ``<br>`` HTML tags.

~~~md
| Syntax      | Description |
| ----------- | ----------- |
| Header      | Title |
| Paragraph   | First paragraph. <br><br> Second paragraph. |
~~~

The rendered output looks like this

| Syntax      | Description |
| ----------- | ----------- |
| Header      | Title |
| Paragraph   | First paragraph. <br><br> Second paragraph. |

### Fenced Code Blocks

The basic Markdown syntax allows you to create code blocks by indenting lines by four spaces or one tab. If you find that inconvenient, try using fenced code blocks. Depending on your Markdown processor or editor, you’ll use three backticks (```) or three tildes on the lines before and after the code block. The best part? You don’t have to indent any lines!

It's also good practice, to say what the code type is at the start of the block.

     ~~~json
     {
      "firstName": "John",
      "lastName": "Smith",
      "age": 25 
     }
     ~~~

The rendered output looks like this

~~~json
{
  "firstName": "John",
  "lastName": "Smith",
  "age": 25
}
~~~

### Table of Contents
Some Markdown applications like Markdeep can automatically generate a table of contents (also referred to as a toc) from your headings, but this isn’t a feature provided by all Markdown applications. However, if your Markdown application supports heading IDs, you can create a table of contents for your Markdown file using a list and some links.

~~~html
#### Table of Contents

* [Underline](#underline)
* [Indent](#indent)
* [Center](#center)
* [Color](#color)
~~~

The rendered output looks like this:

#### Table of Contents

* [Underline](#underline)
* [Indent](#indent)
* [Center](#center)
* [Color](#color)

### Code spans

~~~md
`<code>` spans are delimited by backticks.
You can include literal backticks like `` `this` ``.
~~~

`<code>` spans are delimited by backticks.
You can include literal backticks like `` `this` ``.

### Horizontal rules

Three or more dashes

~~~md
---
~~~

Horizontal rules
Three or more dashes

---

### Manual line breaks

End a line with two or more spaces

~~~md
Roses are red,  
Violets are blue.
~~~

Roses are red,  
Violets are blue.

### Text colors and fonts

The following might throw up a few [errors](https://github.com/DavidAnson/markdownlint/blob/v0.25.1/doc/Rules.md#md033) in your Markup editor depending on what you're useing, it's not stickly speaking pure Markup it contains html elements. But it will render correctly and look fine, as you can see below.

~~~html
In his beard lived three <span style="color:red">cardinals</span>.
In his beard lived three cardinals.
~~~

In his beard lived three <span style="color:red">cardinals</span>.
In his beard lived three cardinals.

~~~html
I am in <span style="font-family:Papyrus; font-size:4em;">LOVE!</span>
~~~

I am in <span style="font-family:Papyrus; font-size:4em;">LOVE!</span>

### Videos

If your Markdown application supports HTML, you should be able to embed a video in your Markdown file by copying and pasting the HTML code provided by a video website like YouTube or Vimeo. If your Markdown application doesn’t support HTML, you can’t embed a video, but you can come close by adding an image and a link to the video. You could do this with practically any video on any video service.

Since YouTube makes this easy, we’ll use them as an example. Take this video, for instance: ``https://youtu.be/KMixoNTvtrA``. The last part of the URL (KMixoNTvtrA) is the ID of the video. We can take that ID and put it in the following template

~~~html
[![Image alt text](https://img.youtube.com/vi/KMixoNTvtrA/0.jpg)](https://www.youtube.com/watch?v=KMixoNTvtrA)
~~~

YouTube automatically generates an image for every video (``https://img.youtube.com/vi/KMixoNTvtrA/0.jpg``), so we can use that and link the image to the video on YouTube. After we replace the image alt text and add the ID of the video, our example looks like this:

~~~html
[![I Wish I was a Girl - Wasted Youth](https://img.youtube.com/vi/IQ5yU0eQzig/0.jpg)](https://youtu.be/IQ5yU0eQzig
~~~

The rendered output looks like this

[![Motorhead - Ace of Spades - O2 Glasgow 2002](https://img.youtube.com/vi/IQ5yU0eQzig/0.jpg)](https://youtu.be/IQ5yU0eQzig)

## References

* The Markdown Reference is [here](https://www.markdownguide.org/cheat-sheet/)
* The [Jekyll-Markdown-Cheat-Sheet](https://itopaloglu83.github.io/Jekyll-Markdown-Cheat-Sheet/)
* The standard [Lorem Ipsum passage](https://www.lipsum.com/) and some history surrounding it
