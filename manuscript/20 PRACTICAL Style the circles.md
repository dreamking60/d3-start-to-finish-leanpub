# Practical: Style the circles

This section is a fun one. You’ll **style the circles** so that they represent each of the 4 energy types:

* renewables
* oil, gas and coal
* hydroelectric
* nuclear

The chart will look like:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-12-1024x361.png)

This book is primarily about building an interactive visualization using D3 but we’ll also have a brief look at the **design choices** surrounding the color scheme.

In this section:

* we’ll discuss how to **choose a color scheme** for the circles
* you’ll add **CSS rules** to style the circles

In your code editor open the `step9` directory of the `d3-start-to-finish` code.

### Designing a color scheme

Each country is represent by four overlapping circles so we need a color scheme that makes identifying a circle’s energy type simple.

Also, when designing data visualizations it’s recommended that a color scheme is chosen that works for the **color blind**.

There are a few visual variables we can use including:

* fill color
* fill pattern
* stroke color
* stroke width
* stroke style (dotted, dashed etc.)

**Fill** refers to the color of the inside of the circle and **stroke** refers to the circle outline.

Let’s start with the renewable energy type. An obvious choice for this circle is **green**:

![](https://learn.createwithdata.com/wp-content/uploads/2020/07/image-33.png)

Similarly, an obvious choice for Oil, Gas and Coal is **black** or **gray**. Black is probably too strong a color, so let’s use gray. If we use a filled gray circle we have to make sure smaller circles are drawn in front of larger circles. An easier approach is to use an empty circle with a gray stroke:

![](https://learn.createwithdata.com/wp-content/uploads/2020/07/image-34.png)

Hydroelectric refers to energy produced from water pressure (think of water behind a dam). **Blue**‘s an obvious choice for anything related to water so let’s choose blue for hydroelectric power:

![](https://learn.createwithdata.com/wp-content/uploads/2020/07/image-35.png)

Finally we have nuclear. The nuclear power symbol has a yellow background so yellow might be a good choice. A yellow outline won’t show up very well against a light background so perhaps **orange** works well:

![](https://learn.createwithdata.com/wp-content/uploads/2020/07/image-30.png)

We’re using **three colors** (green, blue and orange) which might not be color blind safe so let’s also change the stroke style of the circles.

Let’s use a **short dashes** for the hydroelectric circles and **long dashes** for the nuclear circles:

![](https://learn.createwithdata.com/wp-content/uploads/2020/07/image-31.png)

It should now be possible to identify the energy type of each circle even if color blind.

I don’t have a design background (I have an engineering background) so choosing colors isn’t my strongest skill. I often visit [coolors.co](https://coolors.co/) which lets you cycle through different color schemes. I like the look of the colors that coolors generates and I usually keep cycling through until I spot a color I like the look of.

This is how I found the particular hues of green, blue and orange seen above.

Here’s a table with the colors and styles we’ll use:

<table><tbody><tr><td><strong>Energy type</strong></td><td><strong>fill</strong></td><td><strong>stroke</strong></td><td><strong>stroke-dasharray</strong></td></tr><tr><td>renewable</td><td>#7FB069</td><td>none</td><td>none</td></tr><tr><td>oilgascoal</td><td>none</td><td>#BCB5B2</td><td>none</td></tr><tr><td>hydroelectric</td><td>none</td><td>#5EB1BF</td><td>3,1</td></tr><tr><td>nuclear</td><td>none</td><td>#F6AE2D</td><td>4,2</td></tr></tbody></table>

`stroke-dasharray` is a CSS property that defines a stroke’s dash style. The first number defines how long the dash is and the second number defines the size of the gap between the dashes.

### Add CSS rules to style the circles

In the previous section you added circles for each energy type. A class attribute was added to each circle naming the energy type of the circle:

```
<g class="country" transform="translate(471,264)">
<circle class="renewable" r="24"></circle>
<circle class="oilgascoal" r="17"></circle>
<circle class="hydroelectric" r="1"></circle>
<circle class="nuclear" r="0"></circle>
<text class="label" y="50">Denmark</text>
</g>
```

Circles representing renewable energy have class attribute of `renewable`, those representing Oil, Gas and Coal have a class attribute of `oilgascoal`. Hydroelectric circles have a class attribute of `hydroelectric` and Nuclear circles have a class attribute of `nuclear`.

This makes it straightforward to identify the circles of a particular type when you’re adding CSS rules.

Open `css/style.css` and remove the rule for circles:

{caption: "style.css", line-numbers: false}
```
.country .label {
text-anchor: middle;
}
/* remove the following: */
circle {
fill: none;
stroke: #aaa;
}
```

Now add rules for each of the energy types (in accordance with the style values in the table above):

{caption: "style.css", line-numbers: false}
```
circle.renewable {
fill: #7FB069;
}
circle.oilgascoal {
fill: none;
stroke: #BCB5B2;
}
circle.hydroelectric {
fill: none;
stroke: #5EB1BF;
stroke-dasharray: 3,1;
}
circle.nuclear {
fill: none;
stroke: #F6AE2D;
stroke-dasharray: 4,2;
}
.country .label {
text-anchor: middle;
}
```

> If `stroke` and `stroke-dasharray` is `none` there’s no need to add a declaration because `none` is the default value.

Save and refresh the browser (making sure it’s pointing at `step9`) and you should see:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-13-1024x352.png)

In the next section you’ll add some more CSS to center and set the background of the Energy Explorer.

## Practical: More styling

In this practical you’ll:

* **center the visualization** within the page
* change the **background color**

In your code editor open the `step9` directory of the `d3-start-to-finish` code. (It’ll already be open if you’ve just finished the previous practical.)

### Center the visualization

Start by opening `index.html` and wrap the `svg` element in a `div` element. Add a class attribute named `wrapper` to the `div` element:

{caption: "index.html", line-numbers: false}
```
<!DOCTYPE html>
<html lang="en">
<head>
...
</head>
<body>
<div id="wrapper">
<svg width="1200" height="1200">
<g id="chart"></g>
</svg>
</div>
...
</body>
</html>
```

The main reason for adding the `div` element is that you’ll be adding further interface elements (such as a menu and a legend) later on and it’s good practice to enclose them in a single wrapper element.

To center a `div` element you can set its width and set its margin to `0 auto`. This isn’t at all obvious if you don’t have much CSS experience but it’s common practice.

Open `css/style.css` and add a rule for the new `div` element:

{caption: "style.css", line-numbers: false}
```
#wrapper {
width: 1200px;
margin: 0 auto;
}
circle.renewable {
fill: #7FB069;
}
...
```

Save `index.html` and `css/style.css` and refresh the browser (making sure it’s pointing at `step9`) and the visualization should now be centred horizontally.

### Change the background color

Finally in `style.css` set the background color to off-white:

{caption: "style.css", line-numbers: false}
```
body {
background-color: #FFFFF7;
}
#wrapper {
width: 1200px;
margin: 0 auto;
}
...
```

Save `style.css` and refresh your browser and you should see that the background color is a subtle off white color:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-12-1024x361.png)

The complete code for this practical is in `step9-complete`.

### Summing up

A major part of the visualization is finished now. Each country is represented by 4 circles and you can now begin to compare the energy mixes of different countries.

I’m personally drawn to the solid green circles. The larger they are, the more renewables contribute to that country’s energy mix. Denmark’s a good example. Albania’s an interesting case as it looks like the vast majority of its energy comes from hydroelectric (blue outline). And not surprisingly for many countries, the biggest contributor is oil, gas and coal (gray outline).

In the next part of the book you’ll add an information popup that’ll appear when a country is hovered over.