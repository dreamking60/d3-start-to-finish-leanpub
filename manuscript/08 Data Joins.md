# Data joins

A fundamental concept in D3 is the **data join**.

A data join creates a correspondence between an **array of data** and a **selection** of HTML or SVG elements.

Joining an array to HTML/SVG elements means that:

* each array element has a **corresponding** HTML/SVG element
* each HTML/SVG element may be **positioned**, **sized** and **styled** according to the value(s) of its corresponding array element

Remember this example from the Introduction to D3 section:

Navigate to [https://codepen.io/createwithdata/pen/NWxjKVo](https://codepen.io/createwithdata/pen/NWxjKVo) to view the example in CodePen

When a new element is **added** to the array (click _Add array item_) a circle will be added to the page.

When an element is **removed** from the array (click _Remove array item_) the array element’s corresponding circle will be removed from the page.

Futhermore when the array values **change** (click _Update array items_) the circles change size accordingly.

## Creating a data join

The general pattern for creating a data join is:

```
d3.select(container)
.selectAll(element-type)
.data(array)
.join(element-type);
```

where:

* `container` is a CSS selector string that specifies an element that will **contain** the joined HTML/SVG elements
* `element-type` is a string describing the **type** of element you’re joining (e.g. ‘`div'` or `'circle'`)
* `array` is the name of the **array** you’re joining

The container is a single element that’ll act as the parent of the HTML/SVG elements that you’re joining.

Let’s look at a specific example. Suppose you’ve an array of values:

```
var myData = [10, 40, 30];
```

and the following SVG:

```
<svg>
<g class="chart">
</g>
</svg>
```

We’ll use the `g` element as the container and will join the array to `circle` elements.

The end result will look like:

```
<svg>
<g class="chart">
<circle></circle>
<circle></circle>
<circle></circle>
</g>
</svg>
```

The code for this is:

```
d3.select('g.chart')
.selectAll('circle')
.data(myData)
.join('circle');
```

The first line:

```
d3.select('g.chart')
```

makes a selection of a single HTML or SVG element that will be **the parent** of the joined HTML or SVG elements.

The second line:

```
    .selectAll('circle')
```

selects the HTML/SVG elements you’re joining. In our example we’re joining `circle` elements to the array so we select all `circle` elements. You always use `.selectAll` to make this selection.

> Even though there are no circles on the page you can still make a selection of circles. The resulting selection is empty but it’s still a valid selection. This is an aspect of D3 joins that can be hard to grasp so it can be confusing. Rest assured that once you get used to it it’ll make more sense.

The third line:

```
    .data(myData)
```

specifies the array you’re wanting to join. In this example, we’re joining the `myData` array.

The final line:

```
    .join('circle');
```

creates the join. You pass in the type of HTML or SVG element you’re wanting D3 to create. (In general the same element type will be passed into `.selectAll` and `.join`.)

When these four lines of code are executed `circle` elements are added or removed such that each array element has a corresponding `circle`.

Here’s a example in Codepen. (You won’t see any output because each circle has radius zero so will be invisible.)

Navigate to [https://codepen.io/createwithdata/pen/ZEQxyyJ](https://codepen.io/createwithdata/pen/ZEQxyyJ) to view the example in CodePen

However, if you right-click on the SVG element (which has been colored light grey) and choose Inspect (or similar) you’ll be able to see the page’s elements. Expand the `svg` and `g` elements and you should see the three circles that have been added by D3:

![](https://learn.createwithdata.com/wp-content/uploads/2020/10/data-join-1024x534.gif)

### Recap

A D3 data join creates a correspondence between an **array of values** and a **selection of HTML/SVG elements**.

The general pattern for creating a join is:

```
d3.select(container)
.selectAll(element-type)
.data(array)
.join(element-type);
```

The first line selects the container element.

The second line selects the HTML/SVG elements you’re joining.

The third line specifies the array you’re joining.

The last line specifies the type of HTML/SVG element you’re joining.

Each time these four lines are executed D3 adds (or removes) HTML/SVG elements such that **each array value has a corresponding HTML/SVG element**.

In the next section we’ll look at how the joined HTML/SVG elements can be modified using `.style`, `.attr`, `.classed` and `.text`.

## Data-driven updates

Once you’ve joined an array to a selection of HTML/SVG elements you can update the position, color, size etc. of each element using `.style`, `.attr`, `.classed` and `.text`. (These are the same four methods introduced in the [Updating a selection’s elements section](https://learn.createwithdata.com/books/d3-start-to-finish/sections/updating-a-selection/).)

Broadly speaking there are three approaches to updating each HTML/SVG element. You can:

* set the style, attribute or content to a **constant value**
* use the **value of the corresponding array element** to set the style, attribute or content
* use the **index** (i.e. the position within the array) to set the style, attribute or content

The first approach is similar to what we covered in the [Updating a selection’s elements section](https://learn.createwithdata.com/books/d3-start-to-finish/sections/updating-a-selection/).

The second approach is probably the most interesting. It allows you to set an element’s style, attribute or text content based on the **value of the corresponding array element** (or the ‘joined value’). This allows you to modify the selection’s elements in a **data-driven** fashion.

The third approach allows you to set an element’s style, attribute or text content based on the **array index** of the corresponding array element. This is typically used to evenly space HTML/SVG elements. For instance, you might use the array index to evenly space bars in a bar chart.

### Constant value update

The **constant value** approach is similar to what you learned in the [Updating a selection’s elements section](https://learn.createwithdata.com/books/d3-start-to-finish/sections/updating-a-selection/).

Suppose you’ve an array of values:

```
var myData = [10, 40, 30];
```

and the following SVG:

```
<g class="chart">
</g>
```

You can join the array to `circle` elements using:

```
d3.select('g.chart')
.selectAll('circle')
.data(myData)
.join('circle');
```

You can set the radius of each circle to a value of `50` by chaining a call to `.attr`:

```
d3.select('g.chart')
.selectAll('circle')
.data(myData)
.join('circle')
.attr('r', 50);
```

Let’s also set the x and y position of each circle to `100` and the fill of each circle to `#af90ca`:

```
d3.select('g.chart')
.selectAll('circle')
.data(myData)
.join('circle')
.attr('r', 50)
.attr('cx', 100)
.attr('cy', 100)
.style('fill', '#af90ca');
```

Here’s the example on CodePen:

Navigate to [https://codepen.io/createwithdata/pen/LYGBOGx](https://codepen.io/createwithdata/pen/LYGBOGx) to view the example in CodePen

(Although there are three circles, they are on top of one another because they have the same center point.)

As an exercise, try changing the `r`, `cx`, `cy` and `fill` values in the CodePen example.

### Joined value (or ‘data-driven’) update

The **joined value** of an HTML/SVG element is the value of the element’s corresponding array element.

In order to use each circle’s joined value to update its style and attributes you **pass a function into `.style`, `.attr`, `.classed`** or **`.text`**.

For each of the selection’s elements D3 **passes the** **joined value into the function as the first parameter**. By convention, the first parameter is named `d`.

The function’s **return value** is used to set the style, attribute or content.

For example you can set the radius of each circle so that it’s equal to the joined value using:

```
var myData = [10, 40, 30];
d3.select('g.chart')
.selectAll('circle')
.data(myData)
.join('circle')
.attr('r', function(d) {
return d;
}); 
```

This results in the first circle getting a radius of 10, the second circle 40 and so on.

This is known as a **data-driven update** and is a **cornerstone of D3**.

Let’s look at another example. You might want to set the circle radii to twice the joined value:

```
var myData = [10, 40, 30];
d3.select('g.chart')
.selectAll('circle')
.data(myData)
.join('circle')
.attr('r', function(d) {
return 2 * d;
});
```

For each circle in the selection D3 calls the function that’s been passed into `.attr` and sets the circle’s radius to the return value of the function.

Thus for the first circle, `d` is `10` so the function returns `20` and the circle’s radius is set to `20`.

Then for the second circle `d` is `40` so the function returns `80` and the circle’s radius is set to `80` and so on.

You can pass a function into any of **`.style`, `.attr`, `.classed`** or **`.text`**.

For example let’s use the joined value to set the circle fills:

```
var myData = [10, 40, 30];
d3.select('g.chart')
.selectAll('circle')
.data(myData)
.join('circle')
.attr('r', function(d) {
return 2 * d;
})
.style('fill', function(d) {
if(d >= 40) {
return 'red';
}
return '#aaa';
});
```

Now the circles will be colored red if the joined value is greater or equal to 40 otherwise they’ll be grey.

Here’s this example on CodePen:

Navigate to [https://codepen.io/createwithdata/pen/pogZdYj](https://codepen.io/createwithdata/pen/pogZdYj) to view the example in CodePen

The circles are on top of one other so you can’t see all the circles in this example. Ideally we’d space the circles evenly and this is where array index updates covered in the next section come in useful.

### Array index update

The third approach to updating each joined HTML/SVG element is to use the **index of the corresponding array element** to update the style, attribute or content.

When a function is passed into **`.style`, `.attr`, `.classed`** or **`.text`** the **second parameter** of the function is the array index of the joined value. By convention the second parameter is named `i`.

The first circle has an index of 0, the second circle 1 and so on.

A common use case is to evenly space each HTML/SVG element. (Bar charts consist of evenly spaced rectangles, for example.)

Let’s space our circles horizontally using `i`:

```
var myData = [10, 40, 30];
d3.select('g.chart')
.selectAll('circle')
.data(myData)
.join('circle')
.attr('r', function(d) {
return d;
})
.attr('cx', function(d, i) {
var circleSpacing = 100;
return i * circleSpacing;
});
```

This spaces the circles 100 pixels apart. (The first circle will have an x coordinate of `0`, the second `100` and so on.)

As usual, here’s the above example on CodePen:

Navigate to [https://codepen.io/createwithdata/pen/oNbMoOq](https://codepen.io/createwithdata/pen/oNbMoOq) to view the example in CodePen

Now the circles are spaced 100 pixels apart.

They are cut off because the `cy` attribute hasn’t been set and defaults to zero.

As an exercise, try changing the value of `circleSpacing`. Can you guess what will happen to the circle positions?

### Examples

Let’s put all three approaches together.

We’ll set:

* each circle’s y position to 100
* the radius to 1.25 times the joined value
* each circle’s x position to 100 times the index `i`
* each circle’s fill to `#aaa`

The code for this is:

```
var myData = [10, 40, 30];
d3.select('g.chart')
.selectAll('circle')
.data(myData)
.join('circle')
.attr('cy', 100)
.attr('r', function(d) {
return 1.25 * d;
})
.attr('cx', function(d, i) {
var circleSpacing = 100;
return i * circleSpacing;
})
.style('fill', '#aaa');
```

The above code is a very typical pattern in D3. Here it is on CodePen:

Navigate to [https://codepen.io/createwithdata/pen/ZEQxXwO](https://codepen.io/createwithdata/pen/ZEQxXwO) to view the example in CodePen

(The first circle is clipped because it’s x coordinate is 0.)

Here’s another example where a simple horizontal bar chart is created. The same array `myData` is used but `rect` elements are joined to the array rather than circles.

Here’s the SVG:

```
<g class="barchart">
</g>
```

Each `rect` will have a width that’s 4 times the joined value, a height of 45 and they’ll be vertically spaced 50 pixels apart:

```
var myData = [10, 40, 30];
d3.select('g.barchart')
.selectAll('rect')
.data(myData)
.join('rect')
.attr('width', function(d) {
return 4 * d;
})
.attr('height', 45)
.attr('y', function(d, i) {
var barSpacing = 50;
return i * circleSpacing;
})
.style('fill', '#aaa');
```

Navigate to [https://codepen.io/createwithdata/pen/ZEQxXNp](https://codepen.io/createwithdata/pen/ZEQxXNp) to view the example in CodePen

Try changing the array values and you’ll see the bar chart update accordingly. (Bear in mind that each time you modify code in CodePen, it automatically re-runs which is why you see the bars update.)

Hopefully you can begin to understand D3’s data-driven philosophy. Unlike libraries such as Chart.js where you choose a chart type (bar chart, line chart etc.) D3 let’s you choose HTML or SVG elements and manipulate them in a data-driven fashion.

You can still create standard charts using this approach but the real power is that it allows you to create **custom charts of your own design**. The only limit is your imagination!

In the next section we’ll delve a bit deeper and learn how to join **arrays of objects**.

## Joining arrays of objects

In the previous two sections we showed how to join an array of numbers to HTML/SVG elements.

However data is usually **multi-variable** so we’re usually dealing with arrays of objects such as:

```
[
{
name: "Paris",
indicator1: 9030,
indicator2: 13.45
},
{
name: "Tokyo",
indicator1: 3912,
indicator2: 45.41
},
{
name: "New York",
indicator1: 19481,
indicator2: 32.53
}
]
```

> It’s worth noting that `d3.csv` (which we met in the Requesting data with D3 section) converts CSV files into this format.

An array of objects can be joined to HTML/SVG elements in the same manner as an array of values. The difference is that the **joined value is an object** rather than a number.

Let’s look at an example. Suppose our SVG looks like:

```
<g class="cities">
</g>
```

and our data array is:

```
var myData = [
{
name: "Paris",
indicator1: 9030,
indicator2: 13.45
},
{
name: "Tokyo",
indicator1: 3912,
indicator2: 45.41
},
{
name: "New York",
indicator1: 19481,
indicator2: 32.53
}
];
```

We can create the join using a similar approach to before:

```
d3.select('g.cities')
.selectAll('circle')
.data(myData)
.join('circle');
```

Let’s add a call to `.attr` to update the circle radii:

```
d3.select('g.cities')
.selectAll('circle')
.data(myData)
.join('circle')
.attr('r', function(d) {
// return something here
});
```

Each array element is an object so when we pass a function into `.attr` the first parameter of the function (`d`) **is an object**. For example `d` might look like:

```
{
name: "Paris",
indicator1: 9030,
indicator2: 13.45
}
```

This means we’ll need to access the **object’s properties** (for example`d.name` or `d.indicator1`) instead of `d` alone.

Let’s use `indicator2` to set the radius of the circles:

```
d3.select('g.cities')
.selectAll('circle')
.data(myData)
.join('circle')
.attr('r', function(d) {
return d.indicator2;
});
```

Here’s the example on CodePen:

Navigate to [https://codepen.io/createwithdata/pen/MWKVNMx](https://codepen.io/createwithdata/pen/MWKVNMx) to view the example in CodePen

> In the above CodePen example we’re also updating `cx` and `cy`. Note also there’s a transform applied to the `g` element (look inside the HTML tab).

We can start to see the real power of D3 as we now have multiple variables which can drive the position, size and style of HTML/SVG elements.

For example we can create a simple scatter plot by setting the x coordinate of each circle relative to `indicator1` and the y coordinate relative to `indicator2`:

```
d3.select('g.cities')
.selectAll('circle')
.data(myData)
.join('circle')
.attr('cx', function(d) {
return 0.01 * d.indicator1;
})
.attr('cy', function(d) {
return 2 * d.indicator2;
})
.attr('r', function(d) {
return 5;
});
```

Navigate to [https://codepen.io/createwithdata/pen/vYLjBBx](https://codepen.io/createwithdata/pen/vYLjBBx) to view the example in CodePen

> Note that the above example creates an ‘upside down’ scatter plot because an SVG’s y-axis points downwards.

Here’s a final example where `myArray` is joined to `text` elements:

```
d3.select('g.cities')
.selectAll('text')
.data(myData)
.join('text')
.attr('x', function(d) {
return 0.01 * d.indicator1;
})
.attr('y', function(d) {
return 2 * d.indicator2;
})
.classed('high', function(d) {
return d.indicator1 > 10000;
})
.text(function(d) {
return d.name;
});
```

In this example we’re joining `myArray` to `text` elements. The `x` and `y` attributes are set in the same manner as the previous scatter plot.

We’re using `.classed` to set add a `high` class attribute if `indicator1` is greater than 10,000. We’re also using `.text` to set the content of the `text` element to the city name.

![](https://learn.createwithdata.com/wp-content/uploads/2020/08/image-33.png)

Here’s the example on CodePen:

Navigate to [https://codepen.io/createwithdata/pen/VwexZvP](https://codepen.io/createwithdata/pen/VwexZvP) to view the example in CodePen

### Summing up

This section has shown how to join an **array of objects** to HTML/SVG elements. We saw the only difference is that the **joined value is now an object** rather than a number.

This means that when a function is passed into `.style`, `.attr`, `.classed` or `.text` you have to use the object properties (such as `d.indicator1` and `d.name`) rather than `d` itself.

You’ve also seen that joining an array of objects gives you a wide range of possibilities for setting the style, attributes and content of HTML/SVG elements as you have multiple variables to play with.

We’ve covered a large amount of material over the past few sections and hopefully most of it made sense.

The next practical puts this material into practice and you’re going to start visualizing the energy data!