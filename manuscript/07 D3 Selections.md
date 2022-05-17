# D3 Selections

D3 selections are basically **arrays of HTML or SVG elements**. They let you **modify the style and attributes** of the HTML or SVG elements you’ve selected. For instance you can use D3 to select some SVG circles and change their color to red.

Selections are also used when **joining an array** to HTML or SVG elements. We’ll look at data joins in an upcoming section.

## Creating a selection

D3 has two functions for making selections: `d3.select` and `d3.selectAll`.

`d3.select` selects a single element while `d3.selectAll` selects multiple elements.

### d3.select

The syntax of `d3.select` is:

```
d3.select(selector);
```

where `selector` is a string containing a CSS selector string (for example `'#chart'`).

(If you’re not familiar with CSS selectors take a look at the [CSS section](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/sections/css/) in the Fundamentals of HTML, SVG, CSS and JavaScript for Data Visualisation book.)

`d3.select` returns an object which has a number of methods (such as `.attr` and `.style`) that modify the selected HTML/SVG element. (A method is a function that’s been assigned to a property of an object.)

`d3.select` selects the **first** element on the page that matches the selector string.

For example, if you have some SVG circles on the page:

```
<circle></circle>
<circle></circle>
<circle></circle>
```

the following statement makes a selection containing just the first circle:

```
d3.select('circle');
```

### d3.selectAll

`d3.selectAll` is similar to `d3.select` but it selects **all** matching elements on the page.

For example, if you have some SVG circles on the page:

```
<circle></circle>
<circle></circle>
<circle></circle>
```

the following statement makes a selection containing all three circles:

```
d3.selectAll('circle');
```

`d3.selectAll` returns an object which has a number of methods (such as `.attr` and `.style`) that modify all the selected HTML/SVG elements. The next section covers these methods in detail.

## Updating a selection’s elements

Once you’ve used `d3.select` or `d3.selectAll` to create a selection of HTML/SVG elements you can **modify the elements** using a number of methods.

Four of the most commonly used selection methods are `.style`, `.attr`, `.classed` and `.text`.

Here’s a summary of each method:

<table class=""><tbody><tr><td><code>.style</code></td><td>Adds CSS rules to the selection’s elements</td></tr><tr><td><code>.attr</code></td><td>Adds attributes to the selection’s elements</td></tr><tr><td><code>.classed</code></td><td>Adds a class attribute to the selection’s elements</td></tr><tr><td><code>.text</code></td><td>Sets the text content of the selection’s elements</td></tr></tbody></table>

(If you’re not familiar with CSS you can read up on it in the [CSS section](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/sections/css/) of the Fundamentals of HTML, SVG, CSS and JavaScript for Data Visualisation book. Likewise, you can familiarize yourself with HTML or SVG attributes by reading the [SVG section](https://learn.createwithdata.com/books/html-svg-css-and-javascript-for-data-visualisation/sections/svg/).)

Let’s look at each method in turn.

### .style

The `.style` method **sets a CSS style property** on each element in a selection.

The syntax of `.style` is:

```
s.style(property, value);
```

where `s` is a D3 selection.

`property` is a string representing the CSS property (such as `color`, `fill` or `font-size`).

`value` is the value you’re setting the property to and can be any valid CSS value such as `red`, `#ddd` or `12px`.

For example suppose you have the following SVG elements:

```
<circle r="10"></circle>
<circle r="20"></circle>
<circle r="30"></circle>
```

You can update the `fill` style of each circle using:

```
var s = d3.selectAll('circle');
s.style('fill', 'red');
```

This will change the fill color of each circle to `red`.

An alternative (and more common) format is:

```
d3.selectAll('circle')
.style('fill', 'red');
```

Each method call is on a separate line and indented. This is the usual way of formatting D3 code.

Under the hood, D3 adds inline CSS to the element(s). Inline CSS is where an attribute named `style` is added to the element:

```
<circle r="10" style="fill: red;"></circle>
<circle r="20" style="fill: red;"></circle>
<circle r="30" style="fill: red;"></circle>
```

If you need to add more than one style rule, you can **chain** `.style` calls:

```
d3.selectAll('circle')
.style('fill', 'red')
.style('stroke', '#333');
```

Method chaining is a technique where you can make multiple method calls by writing them one after the other. So instead of writing:

```
var s = d3.selectAll('circle');
s.style('fill', 'red');
s.style('stroke', '#333');
```

you write:

```
d3.selectAll('circle')
.style('fill', 'red')
.style('stroke', '#333');
```

Here’s a CodePen example that demonstrates `.style`:

Navigate to [https://codepen.io/createwithdata/pen/abvYowg](https://codepen.io/createwithdata/pen/abvYowg) to view the example in CodePen

As an exercise, try adding another `.style` call to change the `stroke` of the circles to `#333`.

### .attr

`.attr` **sets an attribute** on each element in a selection.

The syntax of `.attr` is:

```
s.attr(name, value);
```

where `s` is a D3 selection.

`name` is a string representing the attribute name (for example `id`, `cx` or `width`).

`value` is the value you’re setting the attribute to (for example `main-menu`, `10` or `100`).

For example suppose you have the following SVG elements:

```
<circle></circle>
<circle></circle>
<circle></circle>
```

You can update the `r` attribute of every circle using:

```
d3.selectAll('circle')
.attr('r', 50);
```

This will change the radius of each circle to 50:

```
<circle r="50"></circle>
<circle r="50"></circle>
<circle r="50"></circle>
```

As with `.style` (and all other selection methods) you can chain method calls:

```
d3.selectAll('circle')
.attr('cx', 200)
.attr('cy', 100)
.attr('r', 50);
```

Here’s a CodePen example that uses `.attr` to set all the circle radii to 30:

Navigate to [https://codepen.io/createwithdata/pen/GRZWPjM](https://codepen.io/createwithdata/pen/GRZWPjM) to view the example in CodePen

As an exercise, try using `.attr` to change the circles’ vertical position (use the attribute `cy`) to 30.

### .classed

You can **add and remove class attributes** to and from a selection’s elements using `.classed`.

The syntax of `.classed` is:

```
s.classed(className, value);
```

where `s` is a D3 selection.

`className` is the name of the class you’re adding or removing.

`value` indicates whether you’re adding or removing the class attribute. If `value` is `true` the class is added to the element. If `value` is `false` the class is removed from the element.

For example to add a class attribute named `highlighted` to each element in a selection use:

```
d3.selectAll('circle')
.classed('highlighted', true);
```

To remove a class attribute named `highlighted` use:

```
d3.selectAll('circle');
.classed('highlighted', false);
```

You can add more than one class attribute by separating the class names with a space:

```
d3.selectAll('circle');
.classed('item highlighted', true);
```

Under the hood `.classed` adds a class attribute to a selection’s element(s):

```
<circle class="item highlighted"></circle>
```

Here’s a CodePen example that uses `.classed` to add a class attribute named `highlighted` to each circle. The CSS contains a rule that sets the fill color for elements with class `highlighted`.

Navigate to [https://codepen.io/createwithdata/pen/WNwpLog](https://codepen.io/createwithdata/pen/WNwpLog) to view the example in CodePen

### .text

The `.text` method lets you **set the text content** of each element in a selection. It’s generally used on selections of HTML elements (such as `div`, `p` and `span`) and SVG `text` elements.

The syntax is:

```
s.text(value);
```

where `s` is a D3 selection and `value` is the string you’re setting the content to.

Suppose you have the following HTML:

```
<div></div>
<div></div>
```

You can set the text content of each `div` element using:

```
d3.selectAll('div')
.text('Some content');
```

This results in the following change to the HTML:

```
<div>Some content</div>
<div>Some content</div>
```

Here’s the example in CodePen:

Navigate to [https://codepen.io/createwithdata/pen/ExKWGWj](https://codepen.io/createwithdata/pen/ExKWGWj) to view the example in CodePen

## Multiple updates

The `.style`, `.attr`, `.classed` and `.text` methods may be chained together which allows you to set multiple style, attribute and classes in a single statement. For example:

```
d3.selectAll('circle')
.attr('cx', 100)
.attr('cy', 100)
.attr('r', 50)
.style('fill', 'red')
.classed('item', true);
```

### .select and .selectAll

The `.style`, .`attr`, `.classed` and `.text` methods can be used on selections created using `.select` and `.selectAll`.

All the above examples use `.selectAll`. If `.select` is used, only the first matching element will be selected.

Try changing `.selectAll` to `.select` in the CodePen examples and you should see that only the first element on the page is modified.

### Summary

This section introduced four methods (`.style`, `.attr`, `.classed` and `.text`) for updating the style, attributes and content of a selection’s elements.

## Chained selections

You can make a selection of elements **within another selection** by **chaining** calls to `.select` and `.selectAll`.

(We described method chaining in the [previous section](https://learn.createwithdata.com/books/d3-start-to-finish/sections/updating-a-selection).)

This concept is best explained by an example. Suppose you have the HTML:

```
<div class="first">
<p></p>
<p></p>
</div>
<div class="second">
<p></p>
<p></p>
</div>
```

You can select the `p` elements inside the first `div` by selecting the `div` and chaining a call to `selectAll`:

```
d3.select('div.first')
.selectAll('p');
```

Similarly you can select the `p` elements from within the second `div` using:

```
d3.select('div.second')
.selectAll('p');
```

Here’s a Codepen containing these examples:

Navigate to [https://codepen.io/createwithdata/pen/xxZYvBZ](https://codepen.io/createwithdata/pen/xxZYvBZ) to view the example in CodePen

You might be wondering whether you can achieve something similar using a nested CSS selector. For example, you might think:

```
d3.selectAll('div.second p');
```

is the same as:

```
d3.select('div.second')
.selectAll('p');
```

They’re similar but there is a difference: when selections are chained **the selection keeps a reference to the parent element**.

In the above example the selection keeps a reference of the parent element (`div.second`) of the selected `p` elements.

This will come in useful when joining data. For now, don’t worry too much about this, but just remember that selections can be chained.