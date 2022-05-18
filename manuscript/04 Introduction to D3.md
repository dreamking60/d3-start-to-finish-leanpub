# Introduction to D3

[D3](https://d3js.org/) is a JavaScript library that helps you build **custom**, **interactive** and **beautiful** data visualisations. Rather than providing a library of chart types (which is what libraries like [Highcharts](https://www.highcharts.com/) and [Chart.js](https://www.chartjs.org/) do) it provides **building blocks** for creating custom (and standard) charts.

D3 primarily consists of:

* a mechanism for joining (or binding) arrays of data to HTML or SVG elements
* a vast number of modules that help build data visualisations

This section gives an overview of D3 joins and some of D3’s modules. Don’t worry too much if you don’t follow everything because we’ll cover this material in depth later on.

## Joining data with D3

One of the cornerstones of D3 is its **data joining** mechanism. A data join creates a correspondence between an **array of data** and a **group of HTML or SVG elements**. Specifically a data join means that:

* each array element has a **corresponding** HTML/SVG element
* each HTML/SVG element may be **positioned**, **sized** and **styled** according to the value(s) of its corresponding array element

Here's an example where an **array of numbers** has been joined to **SVG circles**:

![An array of numbers joined to circle elements](2ac687d5174626438459b9121d04668c.png)

Each array element **corresponds** to a circle. Furthermore each circle is **sized** according to its corresponding array value.

**Adding** a new array element causes a new circle to be created:

![Adding a new array element results in a new circle](81b8df193431436a0bb5da597ad950dc.png)

**Removing** array element(s) causes the corresponding circle(s) to be removed:

![Removing array elements results in circle removal](37b7196286c2ee5d1daef1a287b8c7f2.png)

**Changing** array value(s) causes the circles to change size accordingly:

![Modifying array values results in resized circles](fe14440998cdc62b8374dcffdf332836.png)

(You can view an interactive version of this example on Codepen [here](https://codepen.io/createwithdata/pen/NWxjKVo).)

The data join ensures that the circles ‘mirror’ the array. This is the basis for creating data visualisations using D3. In effect array elements are represented by shapes (whether circles, lines, rectangles etc.). The shapes may be sized, positioned and styled according to the corresponding data.

We cover D3 joins in detail later in this book. For now just remember that one of D3’s cornerstones is its ability to join arrays to HTML/SVG elements. This results in the HTML/SVG elements mirroring the array elements.

## D3 modules

D3 has a large number of modules that help you build interactive data visualisations. Let’s look at some of the more commonly used ones.

### Data requests

D3’s **fetch module** makes loading comma-separated value (CSV) and JSON files straightfoward. Comma-separated value (CSV) files are text files that represent data in a tabular format. For example:

```text
name,indicator-1,indicator-2
Paris,4030,13.45
Tokyo,3912,45.41
New York,19481,32.53
```

JSON files are text files with a format closely related to JavaScript objects and arrays. For example:

```json
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

D>CSV files are often produced by exporting data from spreadsheets and databases while the JSON format is often used by APIs.

You can use a D3 function `d3.csv` to request a CSV file from a given URL. When the CSV file arrives, D3 converts it into an array of objects.

For example, suppose there’s a CSV file located at `data/cities.csv`:

{caption: 'data/cities.csv'}
```text
name,indicator-1,indicator-2
Paris,4030,13.45
Tokyo,3912,45.41
New York,19481,32.53
```

You can request this data using:

```js
d3.csv('data/cities.csv')
```

and D3 converts the CSV file into an array of objects:

```js
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

Each **object** corresponds to a **row** from the CSV file. For example, the first object corresponds to the first row of data (Paris). This makes working with CSV (and similar) data very simple. We’ll take a closer look at data requests in the next section.

### Scale functions

We've seen that a common pattern in D3 is the data join. For example, given the data:

```
var myData = [ 1, 4, 3 ];
```

we could create a rectangle for each element and set the length of the rectangle proportionally to the value. Rarely will the data correspond exactly to screen coordinates so we usually need to apply some scaling.

We could just multiply each value by a factor but this can make the code harder to understand. Instead the usual approach is to create a scale function. For example you could do this:

```js
function barLengthScale(value) {
  return value * 50;
}

barLengthScale(1); // returns 50
barLengthScale(4); // returns 200
```

However D3 provides a system for creating scale functions that saves you having to figure out the underlying mathematics. It's also expressive (it communicates to anyone reading the code what the intention is).

For example, suppose you have some data that represents scores out of 10. This means the data range is `[0, 10]`. And suppose you want the length of the bars to vary from `0` to `500`, this means the output range is `[0, 500]`.

You can create such a scale function using:

```js
var barLengthScale = d3.scaleLinear()
  .domain([0, 10])
  .range([0, 500]);

barLengthScale(1); // returns 50
barLengthScale(4); // returns 200
```

The above makes it clear what the scale function’s intent is: its input range is 0 to 10 and its output range is 0 to 500. This makes reading and maintaining the code easier. Furthermore you can do things like clamp the scale so that if the input exceeds the input domain, the function clamps the output to the output range. For example:

```js
barWidthScale.clamp(true);

barWidthScale(10);  // returns 500
barWidthScale(100); // returns 500
```

You’ll learn more about scale functions later in this book and you’ll use them to size the circles in Energy Explorer.

### Axes

Have you ever looked closely at a chart axis and wondered how to create one?

{width: 66%}
![Chart axis generated by D3](0fab8c3d83d949097430f98520bdcc54.png)

It’s probably not as easy as you might think! The axis will be different if it appears to the left, right, top or bottom of the chart. How many tick marks should there be? Do you want it to animate if the chart zooms in?

To save you figuring this out D3 provides an axis module. You pass a scale function and an HTML/SVG element into the axis module and D3 figures out how to draw the axis.

### Shapes

Imagine you were making a doughnut chart consisting of a few arc segments.

{width: 25%}
![Doughnut chart](03749f6158d4eeb0b907be55055b6739.png)

Each segment is represented by an SVG path element but figuring out the path string is non trivial. For example the SVG code for the large segment on the left is:

```html
<path d="M12.53915742027986,95.05140537899537A4,4,0,0,1,8.936653576956331,99.59988063670296A100,100,0,1,1,-5.208315972309041,-99.86427511744422A4,4,0,0,1,-0.9999833334166643,-95.86970411274646L-0.9999833334166648,-53.76802178493789A4,4,0,0,1,-4.6296141976080225,-49.78520535642397A50,50,0,0,0,2.4424052633738556,49.94031093745256A4,4,0,0,1,6.597767670845549,53.3710557802093Z"></path>
```

Fortunately D3 has a range of helper functions that create SVG path strings. Suppose you want to create an arc segment with start angle 90, end angle 180, inner radius 50 and outer radius 100 you can use:

```js
var arcGenerator = d3.arc();

arcGenerator({
	startAngle: 0.5 * Math.PI,
	endAngle: Math.PI,
	innerRadius: 50,
	outerRadius: 100
});
// returns "M100,0A100,100,0,0,1,6.123233995736766e-15,100L3.061616997868383e-15,50A50,50,0,0,0,50,0Z"
```

D>The angles are specified in radians, so 90 degrees corresponds to 0.5 * π radians.

This results in this path:

{width: 12.5%}
![An arc segment generated by D3](20f6ca6b1edb44534e019e6eccaaf4d4.png)

D3 provides generators for other shapes commonly found in data visualisations. You can find out more at [D3 in Depth](https://www.d3indepth.com/shapes/).

### Transitions

Animations between different chart states add flair to a data visualisation but they also achieve **object constancy**. Object constancy is a technique that helps someone viewing a chart keep track of points of interest, even if they move to new positions.

For example look at these two charts containing a number of circles:

![82c0a3dc64d341a8e88595c224249c46.png](82c0a3dc64d341a8e88595c224249c46.png)

The left chart is before the circles change position and the right chart is after the circles have changed position. It's impossible to tell which circle has moved where. However if the circles animate to their new positions you stand a chance of following a circle as it moves to its new position.

You can visit [https://codepen.io/createwithdata/pen/pogPwOy](https://codepen.io/createwithdata/pen/pogPwOy) to play with this example.

D3 has a powerful system for managing transitions. You can alter things such as the animation duration and the easing function (which lets you control how quickly things accelerate and decelerate). You can also add delays to each element in order to achieve staggered transitions.

Transitions are very well implemented in D3 and its one of the features that stands D3 apart. We cover transitions in more detail later and use them in the Energy Explorer.

### Summary

Hopefully this section has given you a flavour of what D3 is and what it can do. One of the key takeaways is that D3 provides modules to help you build data visualisations. It doesn’t provide ready built charts – use a library such as Chart.js for that.

This book covers a cross section of D3 functionality. It doesn’t cover everything, but it covers enough to get you started building your own custom charts.

## D3 version

Since its inception in 2011, D3 has undergone a number of changes and at the time of writing is at version 7. This book uses **version 7** which was launched in June 2021.