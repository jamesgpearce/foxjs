# fox.js

fox.js allows you to use ASCII-art to create grid layouts in JavaScript.

Grid layouts are often an important part of creating rich internet applications,
and yet creating them is still quite difficult and verbose. The humble and
experimental goal of fox.js is to see whether ASCII art provides a helpful way
to design such layouts for applications easily and flexibly.

Oh, right. Table layouts are evil. Look away now if you want. I'll just say
that: a) this is on the client side, so semantics for SEO and so on become less
important; and b) I've plans to allow fox.js to support other layout approaches
like flex and grid anyway.

In the 21st century, you shouldn't care; but fox.js is less than 1k compressed.

## Usage

Include fox.js in your web app. It adds the global 'Fox' namespace. Currently
this has one function of note: Fox.makeTable().

This function takes two arguments. The first is an array of equally-lengthed
strings that constitute the ASCII-art of the layout you want. The second is a
configuration object of options that alters the way the function works.

By default, the function returns a DOM node for the table you've drawn. So,
the simplest usage of the function is something like:

    document.body.appendChild(
        Fox.makeTable([
            "+---+",
            "|A  |",
            "+---+",
        ])
    );

This adds a one row, one column table to the document's body, with the letter A
in it. See below for how to use the 'map' option to patch longer strings,
elements, or views into the layout.

Or course you also can do something more exotic, with row and cell spanning:

    document.body.appendChild(
        Fox.makeTable([
            "+---+---+---+",
            "|A  |B  |C  |",
            "|   +---+---+",
            "|   |D  |E  |",
            "|   +---+   |",
            "|   |F  |   |",
            "+---+---+---+",
        ])
    );

The rules to obey when you're drawing your table are as follows:

 - All rows must be the same length
 - Vertices of cells must be drawn with a plus symbol '+'
 - Horizontal edges of cells must be drawn with a hyphen, '-'
 - Vertical edges of cells must be drawn with a pipe symbol, '|'
 - Every cell must be at least one character high, and one character wide
 - Label cells with a single letter

Watch for wayward or missing + symbols. Unexpected behavior may ensue.

## Configuration

The configuration options (in the function's second object argument) are as
follows:

### output

The 'output' option takes the following possible values:

 - 'node' (default) - the function returns the table as a HTMLTableElement node
 - 'html' - the function returns the table's HTML as a string
 - 'raw' - the function returns the table as a raw JavaScript arrays & objects

For example, to prompt the HTML string of the table:

    alert(
        Fox.makeTable([
            "+---+",
            "|A  |",
            "+---+",
        ], {
            output: 'html'
        })
    );

The 'raw' output mode returns an array of table rows, which in turn are arrays
of cell objects, containing properties describing the cells' row and column
spans, content, and styling.

(If you wish to alter these objects and then resubmit them to Fox to have them
turned into an HTMLTableElement, you can use the Fox.toTableElement() function)

There's a JSON.stringify example in the examples page to show what this
structure looks like.

### map

The 'map' option allows you to patch strings, elements, or views into your table
layout. Pass in an object with properties of the same name as the letters you've
placed in your ASCII art. The values of these properties can be strings, DOM
elements, or any object that has an 'el' property (such as a Backbone.js view).

For example:

    document.body.appendChild(
        Fox.makeTable([
            "+---+---+",
            "|a  |b  |",
            "+---+---+",
            "|c  |d  |",
            "+---+---+",
        ], {
            map: {
                a: "The letter A",
                b: document.createElement('img'),
                c: {id:'myView', el:document.createElement('img')}
            }
        })
    );

### styles

Using an object in the same way as for the map above, you can also add specific
styles to the tables' cells. This allows you to have control over the layout's
dimensions and colors:

    document.body.appendChild(
        Fox.makeTable([
            "+-----------+---+",
            "|a          |b  |",
            "|---+-------+---+",
            "|c  |d          |",
            "|   |           |",
            "|   |           |",
            "|   +---+-------+",
            "|   |e  |f      |",
            "+---+---+-------+",
        ], {
            debug:true,
            map: {
                a: "Toolbar",
                b: "Menu",
                c: "Sidebar",
                d: "Canvas",
                e: "Button",
                f: "Button"
            },
            styles: {
                table: {'border-collapse':'collapse'},
                a: {width: '80%', background: '#ccc'},
                b: {width: '20%'},
                c: {width: '20%', background: '#777'},
                d: {height: '150px', width: '80%'},
                e: {width: '20%'},
                f: {width: '60%'}
            }
        })
    );

Use the string-based versions of CSS property names as the objects' properties.
For example, to specify a maximum width, use 'max-width' rather than 'maxWidth'.

### debug

As it says on the tin. False by default, but set to true to console.log() the
variables used while processing the table's art, as well as the outputted raw
object. Not really much use to anyone except me for when things don't work
properly.

## Browser support

The library is developed against Chrome, and I'm sure contemporary WebKit
browsers will be fine. The library doesn't do anything very clever except use
forEach for a few of its loops. So certainly JavaScript 1.6 or polyfills are
required for that.

Drop me a pull request if you're feeling nostalgic for less-abled browsers.

## Examples

Check out the index.html file in the project directory to see the library at
work with a few simple examples.