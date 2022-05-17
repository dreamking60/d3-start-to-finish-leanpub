# Introduction to D3

[D3](https://d3js.org/) is a JavaScript library that helps you build **custom**, **interactive** and **beautiful** data visualisations.

Rather than providing a library of chart types (which is what libraries like [Highcharts](https://www.highcharts.com/) and [Chart.js](https://www.chartjs.org/) do) it provides **building blocks** for creating custom (and standard) charts.

D3 primarily consists of:

* a mechanism for joining (or binding) arrays of data to HTML or SVG elements
* a vast number of modules that help build data visualisations

This section gives an overview of D3 joins and some of D3’s modules. Don’t worry too much if you don’t follow everything because we’ll cover a lot of this material in more depth throughout the book.

## Joining data with D3

One of the cornerstones of D3 is its **data joining** mechanism.

A data join creates a correspondence between an **array of data** and a **group of HTML or SVG elements**.

Specifically a data join means that:

* each array element has a **corresponding** HTML/SVG element
* each HTML/SVG element may be **positioned**, **sized** and **styled** according to the value(s) of its corresponding array element

As an example, run this Codepen:

Navigate to [https://codepen.io/createwithdata/pen/NWxjKVo](https://codepen.io/createwithdata/pen/NWxjKVo) to view the example in CodePen

In this example there’s an **array of numbers** that’s been joined to **SVG circles**.

Each array element **corresponds** to a circle. Furthermore each circle is **sized** according to its corresponding array value.

**Adding** a new array element (when _Add array item_ is clicked) causes a new circle to be created.

**Removing** an array element (when _Remove array item_ is clicked) causes the corresponding circle to be removed.

**Changing** an array value (when _Update array items_ is clicked) causes the circles to change size accordingly.

> Note this is a one-way process. Adding, removing or modifying array elements will result in corresponding circles being added, removed or changed. However, adding, removing or modifying the circles elements will not have an affect on the array.

The data join ensures that the circles ‘mirror’ the array.

This is the basis for creating data visualisations using D3. Array elements are represented by shapes. The shapes may be sized, positioned and styled according to the corresponding array value.

Imagine the above circles are swapped with rectangles and the rectangle height is determined by the array values. Can you see that this forms a very basic bar chart?

Navigate to [https://codepen.io/createwithdata/pen/YzwVzGL](https://codepen.io/createwithdata/pen/YzwVzGL) to view the example in CodePen

D3 doesn’t consider the above to be a bar chart. Instead it sees as a **correspondence between an array of data and rectangular shapes**.

> This approach is also known as the Grammar of Graphics and [books](https://www.springer.com/gp/book/9780387245447) have been written on the subject.

If you’re a web developer you might already use React, Vue or Angular. You’re right in thinking that they achieve a similar thing (but in a different manner).

We’ll cover D3 joins in more detail later in this book. For now just remember that one of D3’s cornerstones is its ability to join arrays to HTML/SVG elements. This results in the HTML/SVG elements mirroring the array elements.

## D3 modules

D3 has a large number of modules that help you build interactive data visualisations. Let’s look at some of the more commonly used ones.

### Data requests

D3’s **fetch module** makes loading comma-separated value (CSV) and JSON files straightfoward.

Comma-separated value (CSV) files are text files that represent data in a tabular format. For example:

```
name,indicator-1,indicator-2
Paris,4030,13.45
Tokyo,3912,45.41
New York,19481,32.53
```

CSV files are often produced by exporting data from spreadsheets and databases.

JSON files are text files with a format closely related to JavaScript objects and arrays. For example:

```
[
{
"name": "Paris",
"indicator-1": 4030,
"indicator-2": 13.45
},
{
"name": "Tokyo",
"indicator-1": 3912,
"indicator-2": 45.41
},
{
"name": "New York",
"indicator-1": 19481,
"indicator-2": 32.53
}
]
```

A commonly used D3 request is `d3.csv` which requests a CSV file from a given URL. When the CSV file arrives, D3 converts it into an array of objects.

For example, suppose there’s a CSV file located at `data/cities.csv`:

```
name,indicator-1,indicator-2
Paris,4030,13.45
Tokyo,3912,45.41
New York,19481,32.53
```

You can request this data using:

```
d3.csv('data/cities.csv')
```

D3 then converts the incoming CSV file into an array of objects:

```
[
{
"name": "Paris",
"indicator-1": "4030",
"indicator-2": "13.45"
},
{
"name": "Tokyo",
"indicator-1": "3912",
"indicator-2": "45.41"
},
{
"name": "New York",
"indicator-1": "19481",
"indicator-2": "32.53"
}
]
```

Each **object** corresponds to a **row** from the CSV file. For example, the first object corresponds to the first row of data (Paris).

This makes working with CSV (and similar) data very simple. We’ll take a closer look at data requests in the next section.

### Scale functions

We saw previously that a common pattern in D3 is the data join. For example, given the data:

```
var myData = [ 1, 4, 3 ];
```

we might want to create an SVG rectangle for each element and set the width of the rectangle proportionally to the value.

Rarely will the data correspond exactly to screen coordinates (our bars would only be 1, 4 and 3 pixels long!) so we usually need to apply some scaling.

We could just multiply each value by a factor but this can make the code harder to understand. Instead the usual approach is to create a scale function. For example:

```
function barWidthScale(value) {
return value * 50;
}
barWidthScale(1); // returns 50
barWidthScale(4); // returns 200
```

D3 provides a system for creating scale functions that’s expressive (it communicates to anyone reading the code what the intention is) and saves you having to do some mathematics.

For example, suppose you have some data that represents scores out of 10. This means the data range is `[0, 10]`. And suppose you want the width of the bars to vary from `0` to `500`, this means the output range is `[0, 500]`. You can then create a scale function using:

```
var barWidthScale = d3.scaleLinear()
.domain([0, 10])
.range([0, 500]);
barWidthScale(1); // returns 50
barWidthScale(4); // returns 200
```

The above makes it clear what the scale function’s intent is: its input range is 0 to 10 and its output range is 0 to 500. This makes reading and maintaining the code easier.

Furthermore you can do things like clamp the scale so that if the input exceeds the input range, the function clamps the output to the output range. For example:

```
barWidthScale.clamp(true);
barWidthScale(10);  // returns 500
barWidthScale(100); // returns 500
```

You’ll learn more about scale functions later in this book and you’ll use them to size the circles in the Energy Explorer.

### Axes

Have you ever looked closely at a chart axis and wondered how to create one?

![](https://learn.createwithdata.com/wp-content/uploads/2020/06/image-4.png)

It’s probably not as easy as you might imagine! The axis will be different if it appears to the left, right, top or bottom of the chart. How many tick marks should there be? Do you want it to animate if the chart zooms in?

To save you figuring this out D3 provides an axis module. You pass a scale function and an HTML/SVG element into the axis module and D3 figures out how to draw the axis.

### Shapes

Imagine you were making a donut chart consisting of a few arc segments.

![](https://learn.createwithdata.com/wp-content/uploads/2020/06/image-5.png)

Each segment is represented by an SVG path element but figuring out the path string is non trivial. For example the SVG code for the large segment on the left is:

```
<path d="M12.53915742027986,95.05140537899537A4,4,0,0,1,8.936653576956331,99.59988063670296A100,100,0,1,1,-5.208315972309041,-99.86427511744422A4,4,0,0,1,-0.9999833334166643,-95.86970411274646L-0.9999833334166648,-53.76802178493789A4,4,0,0,1,-4.6296141976080225,-49.78520535642397A50,50,0,0,0,2.4424052633738556,49.94031093745256A4,4,0,0,1,6.597767670845549,53.3710557802093Z"></path>
```

Fortunately D3 has a range of helper functions that create SVG path strings. Suppose you want to create an arc segment with start angle 90, end angle 180, inner radius 50 and outer radius 100 you can use:

```
var arcGenerator = d3.arc();
arcGenerator({startAngle: 0.5 * Math.PI, endAngle: Math.PI, innerRadius: 50, outerRadius: 100});
// returns "M100,0A100,100,0,0,1,6.123233995736766e-15,100L3.061616997868383e-15,50A50,50,0,0,0,50,0Z"
```

(The angles are specified in radians, so 90 degrees corresponds to 0.5 \* π radians.)

This results in this path:

![](https://learn.createwithdata.com/wp-content/uploads/2020/06/image-6.png)

D3 provides generators for other shapes commonly found in data visualisations. You can find out more at [D3 in Depth](https://www.d3indepth.com/shapes/).

### Transitions

Animations between different chart states add flair to a data visualisation but they also achieve **object constancy**. Object constancy is a technique that helps someone viewing a chart keep track of points of interest, even if they move to new positions.

For example look at the chart below. Choose a circle then click _Update data_. Did you see where your circle moved? Probably not.

Now switch on _Use transitions_. Click _Update data_ and you can now track each circle.

Can you see that animating the circles makes it easier for you to see the changes?

Navigate to [https://codepen.io/createwithdata/pen/pogPwOy](https://codepen.io/createwithdata/pen/pogPwOy) to view the example in CodePen

D3 has a powerful system for managing transitions. You can alter things such as the animation duration and the easing function (which lets you control how quickly things accelerate and decelerate). You can also add delays to each element in order to achieve staggered transitions.

Notice that you can start a new transition even if one is in progress. If you repeatedly click on ‘Update data’ you’ll see D3 is clever enough to start each new transition from the current positions.

Transitions are very well implemented in D3 and its one of the features that stands D3 apart. We’ll cover transitions in more detail later in this book and will apply them to the Energy Explorer.

### Summary

Hopefully this section has given you a flavour of what D3 is and what it can do. One of the key takeaways is that D3 provides modules to help you build data visualisations. It doesn’t provide ready built charts – use a library such as Chart.js for that.

This book covers a cross section of D3 functionality. It doesn’t cover everything, but it covers enough to get you started building your own custom charts.

## D3 versions

Since its inception in 2011, D3 has undergone a number of changes and is currently at version 6.

**This book uses version 6** which was launched in August 2020.

The only significant change, as far as this book is concerned, is to event handling.

Version 5 of D3 passes two arguments `d` and `i` to event handlers:

```
function handleClick(d, i) {
console.log('You clicked on', d);
}
```

Version 6 passes in three arguments, the first of which is the event object (created by the browser):

```
function handleClick(e, d, i) {
console.log('You clicked on', d);
}
```

This a breaking change – I found this out when upgrading the Energy Explorer to version 6! You can learn more about it [here](https://github.com/d3/d3/blob/master/CHANGES.md#breaking-changes).

In general if you’re starting a new project I recommend using version 5 or version 6, mainly because of the `.join` method (which you’ll learn about later) which was added to version 5.

I’ve chosen to use version 6 in this book just because it’s the most recent version.

Anyway… let’s start learning D3!