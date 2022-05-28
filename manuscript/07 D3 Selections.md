# D3 Selections

D3 selections are basically **arrays of HTML or SVG elements**. They let you **modify the style and attributes** of the HTML or SVG elements you’ve selected. For instance you can use D3 to select some SVG circles and change their colour to red. Selections are also used when **joining an array** to HTML or SVG elements (see the next chapter).

## Creating a selection

D3 has two functions for making selections: `d3.select` and `d3.selectAll`.

`d3.select` selects a single element while `d3.selectAll` selects multiple elements.

### d3.select

The syntax of `d3.select` is:

```js
d3.select(selector);
```

where `selector` is a string containing a CSS selector string (for example `'#chart'`).

D>If you’re not familiar with CSS selectors take a look at the CSS section in the [Fundamentals of HTML, SVG, CSS and JavaScript for Data Visualisation book](fundamentalsbook).

`d3.select` selects the **first** element on the page that matches the selector string. It returns an object which has a number of methods (such as `.attr` and `.style`) that modify the selected HTML/SVG element. (A method is a function that’s been assigned to a property of an object.)

For example, if you have some SVG circles on the page:

```html
<circle></circle>
<circle></circle>
<circle></circle>
```

the following statement makes a selection containing just the first circle:

```js
d3.select('circle');
```

You can assign a D3 selection to a variable. For example:

```js
let s = d3.select('circle');
```

### d3.selectAll

`d3.selectAll` is similar to `d3.select` but it selects **all** matching elements on the page. For example, if you have some SVG circles on the page:

```html
<circle></circle>
<circle></circle>
<circle></circle>
```

the following statement makes a selection containing all three circles:

```js
d3.selectAll('circle');
```

`d3.selectAll` returns an object which has a number of methods (such as `.attr` and `.style`) that modify all the selected HTML/SVG elements. The next section covers these methods in detail.

## Updating a selection’s elements

Once you’ve used `d3.select` or `d3.selectAll` to create a selection of HTML/SVG elements you can **modify the elements** using a number of methods. Four of the most commonly used selection methods are `.style`, `.attr`, `.classed` and `.text`.

| Method name | Description |
| --- | --- |
| `.style` | Adds CSS rules to the selection’s elements |
| `.attr` | Adds attributes to the selection’s elements |
| `.classed` | Adds a class attribute to the selection’s elements |
| `.text` | Sets the text content of the selection’s elements |

(If you’re not familiar with CSS, HTML or SVG you can read up on them in the [Fundamentals of HTML, SVG, CSS and JavaScript for Data Visualisation book](fundamentalsbook).)

### .style

The `.style` method **sets a CSS style property** on each element in a selection. The syntax is:

```js
s.style(property, value);
```

where `s` is a D3 selection.

`property` is a string representing the CSS property (such as `color`, `fill` or `font-size`) and `value` is the value you’re setting the property to and can be any valid CSS value such as `red`, `#ddd` or `12px`.

For example suppose you have the following SVG elements:

```html
<circle r="10"></circle>
<circle r="20"></circle>
<circle r="30"></circle>
```

You can update the `fill` style of each circle using:

```js
let s = d3.selectAll('circle');
s.style('fill', 'red');
```

This will change the fill colour of each circle to `red`. An alternative format that omits the variable declaration is:

```js
d3.selectAll('circle')
  .style('fill', 'red');
```

Each method call is on a separate line and indented. This is the usual way of formatting D3 code.

Under the hood, D3 adds inline CSS to the element(s). Inline CSS is where an attribute named `style` is added to the element:

```html
<circle r="10" style="fill: red;"></circle>
<circle r="20" style="fill: red;"></circle>
<circle r="30" style="fill: red;"></circle>
```

If you need to add more than one style rule, you can **chain** `.style` calls:

```js
d3.selectAll('circle')
  .style('fill', 'red')
  .style('stroke', '#333');
```

Method chaining is a technique where you can make multiple method calls by writing them one after the other. So instead of writing:

```js
var s = d3.selectAll('circle');
s.style('fill', 'red');
s.style('stroke', '#333');
```

you write:

```js
d3.selectAll('circle')
  .style('fill', 'red')
  .style('stroke', '#333');
```

Here’s a CodePen example that demonstrates `.style`:

[https://codepen.io/createwithdata/pen/abvYowg](https://codepen.io/createwithdata/pen/abvYowg).

As an exercise, try adding another `.style` call to change the `stroke` of the circles to `#333`.

### .attr

`.attr` **sets an attribute** on each element in a selection. The syntax is:

```js
s.attr(name, value);
```

where `s` is a D3 selection.

`name` is a string representing the attribute name (for example `id`, `cx` or `width`) and `value` is the value you’re setting the attribute to (for example `main-menu`, `10` or `100`).

For example suppose you have the following SVG elements:

```html
<circle></circle>
<circle></circle>
<circle></circle>
```

You can update the `r` attribute of every circle using:

```js
d3.selectAll('circle')
  .attr('r', 50);
```

This will change the radius of each circle to 50:

```html
<circle r="50"></circle>
<circle r="50"></circle>
<circle r="50"></circle>
```

As with `.style` (and all other selection methods) you can chain method calls:

```js
d3.selectAll('circle')
  .attr('cx', 200)
  .attr('cy', 100)
  .attr('r', 50);
```

Here’s a CodePen example that uses `.attr` to set all the circle radii to 30:

[https://codepen.io/createwithdata/pen/GRZWPjM](https://codepen.io/createwithdata/pen/GRZWPjM)

As an exercise, try using `.attr` to change the circles’ vertical position (use the attribute `cy`) to 30.

### .classed

You can **add and remove class attributes** to/from a selection’s elements using `.classed`. The syntax is:

```js
s.classed(className, value);
```

where `s` is a D3 selection.

`className` is the name of the class you’re adding or removing and `value` indicates whether you’re adding or removing the class attribute. If `value` is `true` the class is added to the element. If `value` is `false` the class is removed from the element.

For example to add a class attribute named `highlighted` to each element in a selection use:

```js
d3.selectAll('circle')
  .classed('highlighted', true);
```

To remove a class attribute named `highlighted` use:

```js
d3.selectAll('circle');
  .classed('highlighted', false);
```

You can add more than one class attribute by separating the class names with a space:

```js
d3.selectAll('circle');
  .classed('item highlighted', true);
```

Under the hood `.classed` adds a class attribute to a selection’s element(s):

```html
<circle class="item highlighted"></circle>
```

Here’s a CodePen example that uses `.classed` to add a class attribute named `highlighted` to each circle:

[https://codepen.io/createwithdata/pen/WNwpLog](https://codepen.io/createwithdata/pen/WNwpLog)

The CSS contains a rule that sets the fill colour for elements with class `highlighted`.

### .text

The `.text` method lets you **set the text content** of each element in a selection. It’s generally used on selections of HTML elements (such as `div`, `p` and `span`) and SVG `text` elements. The syntax is:

```js
s.text(value);
```

where `s` is a D3 selection and `value` is the string you’re setting the content to.

Suppose you have the following HTML:

```html
<div></div>
<div></div>
```

You can set the text content of each `div` element using:

```html
d3.selectAll('div')
  .text('Some content');
```

This results in the following change to the HTML:

```html
<div>Some content</div>
<div>Some content</div>
```

Here’s an example in CodePen: [https://codepen.io/createwithdata/pen/ExKWGWj](https://codepen.io/createwithdata/pen/ExKWGWj)

## Multiple updates

The `.style`, `.attr`, `.classed` and `.text` methods may be chained together which allows you to set multiple style, attribute and classes in a single statement. For example:

```js
d3.selectAll('circle')
  .attr('cx', 100)
  .attr('cy', 100)
  .attr('r', 50)
  .style('fill', 'red')
  .classed('item', true);
```

### .select and .selectAll

The `.style`, .`attr`, `.classed` and `.text` methods can be used on selections created using `.select` and `.selectAll`. All the above examples use `.selectAll`. If `.select` is used, only the first matching element will be selected. Try changing `.selectAll` to `.select` in the CodePen examples and you should see that only the first element on the page is modified.

## Chained selections

You can make a selection of elements **within another selection** by **chaining** calls to `.select` and `.selectAll`. This concept is best explained by an example. Suppose you have the HTML:

```html
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

```js
d3.select('div.first')
  .selectAll('p');
```

Similarly you can select the `p` elements from within the second `div` using:

```js
d3.select('div.second')
  .selectAll('p');
```

Here’s a Codepen containing these examples:

[https://codepen.io/createwithdata/pen/xxZYvBZ](https://codepen.io/createwithdata/pen/xxZYvBZ)

You might be wondering whether you can achieve something similar using a nested CSS selector. For example, you might think:

```js
d3.selectAll('div.second p');
```

is equivalent to:

```js
d3.select('div.second')
  .selectAll('p');
```

They’re similar but there is a difference: when selections are chained **the selection keeps a record of the parent element**. In the previous code snippet the selection keeps a record of the parent element (`div.second`) of the selected `p` elements.

This will come in useful when joining data. For now, don’t worry too much about this, but just remember that selections can be chained.