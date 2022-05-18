# Scale functions

Scale functions help you transform your **data values** into **visual values**.

For example, in Energy Explorer **data values** are percentage values, so they vary between 0 and 100.

**Visual values** are properties such as position, size and color. For example an x coordinate of `50px`, a height of `100px` or a color of `#aaa`.

Suppose you have an array of numbers representing percentage values:

```js
let data = [15, 76, 41, 67, 97];
```

and you want to create a bar chart where the maximum length of the bars is `500px`.

A scale function which takes a number between 0 and 100 as input and ouputs a value between 0 and 500 would help you here. D3 has a module dedicated to scale functions. It creates scale functions for you and lets you configure them. It saves you having to do a lot of mathematics!

D3 has around twelve scale types. Some accept **numbers** as input, others accept **strings**. Some output **numbers**, others ouput **colors**. In this book we cover two scale types `scaleLinear` and `scaleSqrt`.

`scaleLinear` is by far the most common D3 scale type and has several use cases. `scaleSqrt` is particularly useful when working with circles. We’ll use it to size circles in the Energy Explorer.

## scaleLinear

`d3.scaleLinear` creates linear scale functions. A linear scale takes a **number** as input and returns a **number**. Use the following to create a very simple linear scale function:

```
var myScale = d3.scaleLinear();
```

The return value of `d3.scaleLinear` is a function. Here we assigned the function to a variable `myScale`. By default this function takes an input value and returns the same value:

```js
myScale(0);  // returns 0
myScale(1);  // returns 1
myScale(50);  // returns 50
myScale(100);  // returns 100
```

Not very useful, but you can define the input and output ranges using `.domain` and `.range`, respectively. D3’s terminology uses **domain** to represent the input minimum and maximum and **range** to represent the output minimum and maximum. Let’s set the domain to between 0 and 100 and the range to between 0 and 1000:

```js
myScale.domain([0, 100]).range([0, 1000]);
```

(You pass an array with two values into `.domain` and `.range`. The first value is the minimum and the second value the maximum.)

Now look at the output values:

```js
myScale(0);  // returns 0
myScale(1);  // returns 10
myScale(50);  // returns 500
myScale(100);  // returns 1000
```

Our linear scale function `myScale` takes an input between 0 and 100 (the domain) and linearly maps it to an output between 0 and 1000 (the range).

D>If you plot the output against the input of a **linear** scale you get a straight line.

In effect our linear scale function `myScale` is:

```js
function myScale(value) {
  return 10 * value;
}
```

but D3 has generated the function according to the domain and range configuration. You can also pass colors into the range:

```js
myScale.range(['white', 'red']);
```

This results in:

```js
myScale(0);  // returns "rgb(255, 255, 255)"
myScale(1);  // returns "rgb(255, 252, 252)"
myScale(50);  // returns "rgb(255, 128, 128)"
myScale(100);  // returns "rgb(255, 0, 0)"
```

Now the scale is returning colors ranging from white (`rgb(255, 255, 255)`) to red (`rgb(255, 0, 0)`).

By default D3 scale functions extrapolate the output if you input values outside of the domain:

```js
myScale.domain([0, 100]).range([0, 1000]);
myScale(200);  // returns 2000
myScale(-100);  // returns -1000
```

However you can ‘clamp’ the scale function so that the output stays within the range:

```js
myScale.clamp(true);
myScale(200);  // returns 1000
myScale(-100);  // returns 0
```

You can try out `scaleLinear` at jsconsole.com by visiting [https://jsconsole.com](https://jsconsole.com).

First include D3 using jsconsole.com’s `:load` command:

```js
:load https://cdnjs.cloudflare.com/ajax/libs/d3/7.4.4/d3.min.js
```

Then type (or copy and paste) the following:

```js
var myScale = d3.scaleLinear();
myScale.domain([0, 100]).range([0, 1000]);
```

Now if you enter `myScale(0)` you should see `0` output. If you enter `myScale(100)` you should see `1000` output.

![Exploring `scaleLinear` in jsconsole](24a62193337572020db080c712f48d15.png)

## scaleSqrt

`d3.scaleSqrt` is similar to `d3.scaleLinear` except the **square root** of the input value is used. This is particularly useful when using circles to represent a quantity. It’s widely accepted when visualising data that the **area** of a circle (rather than the radius of a circle) should be proportional to a value. This potentially tricky calculation is taken care of by D3’s `scaleSqrt` scale. Suppose we have a value that varies between 0 and 1 and we wish to represent it with a circle with maximum radius 100 we create a square root scale using:

```js
let myScale = d3.scaleSqrt().domain([0, 1]).range([0, 100]);
myScale(0.5);  // returns 70.71067811865476
myScale(1);  // returns 100
```

D> Recalling circle mathematics, the area of a circle is `𝜋 * radius * radius`. Thus the area of a circle representing a value of 0.5 is `𝜋 * 70.7106 * 70.7106 = 15707.96` and the area of the circle representing 1 is `𝜋 * 100 * 100 = 31415.93`. The second circle is twice the area of the first circle.

The main thing to remember is that if you’re using circle area to represent a value, use `scaleSqrt`.

## Putting scale functions to use

Consider the following code which performs a data join and sets the circle radius according to the joined value:

```js
var myData = [10, 40, 30];

d3.select('g.chart')
  .selectAll('circle')
  .data(myData)
  .join('circle')
  .attr('r', function(d) {
    return d;
  });
```

This scales the circle **radius** proportionally to the joined value `d`. As we’ve just discussed this isn’t recommended – you should scale **area** proportionally to the joined value.

This is achieved by applying a `scaleSqrt` scale:

```js
let myData = [10, 40, 30];
let radiusScale = d3.scaleSqrt().domain([0, 50]).range([0, 50]);

d3.select('g.chart')
  .selectAll('circle')
  .data(myData)
  .join('circle')
  .attr('r', function(d) {
    return radiusScale(d);
  });
```

Notice that the function passed into `.attr` is now using `radiusScale`. Here’s a similar example in CodePen:

[https://codepen.io/createwithdata/pen/yLeqRLp](https://codepen.io/createwithdata/pen/yLeqRLp)

The second data value (40) is twice the first data value (20). This results in the second circle having twice the area of the first circle.

Now change `scaleSqrt` to `scaleLinear` in the CodePen example. When `scaleLinear` is used it’s fairly clear that the second circle’s area is much more than twice the first circle’s, even though its value is only 2 times bigger. We’ll use `scaleSqrt` to set the circle sizes in the Energy Explorer in the following practical.