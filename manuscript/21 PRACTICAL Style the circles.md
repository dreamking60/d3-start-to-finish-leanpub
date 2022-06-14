# Practical: Style the Circles

This practical is a fun one. We **style the circles** so that they represent each of the 4 energy types. The chart will look like:

![Energy Explorer with each circle styled according to the energy type](4498db5f451345a57c5e8690b15f4693.png)

Renewable energy is represented by a solid green circle, Oil, Gas & Coal by a grey outline, Hydroelectric as a dotted blue outline and Nuclear as a dotted orange outline.

## Overview

Open `d3-start-to-finish-code/step9`. The file structure is:

```text
step9
├── css
│   └── style.css
├── data
│   └── data.csv
├── index.html
└── js
    ├── config.js
    ├── layout.js
    ├── lib
    │   └── d3.min.js
    ├── main.js
    └── update.js
```

In this practical we:

1. Design a suitable colour scheme for the circles.
2. Style the circles in `css/style.css`.
3. Center the visualisation.
4. Change the background colour.

## Design a colour scheme

Each country is represent by four overlapping circles so we need a colour scheme that makes it easy to identify a circle’s energy type. Also, when designing data visualisations it’s recommended that a colour-blind friendly scheme is chosen. There are a few visual variables we can use such as:

* fill colour
* fill pattern
* stroke colour
* stroke width
* stroke style (dotted, dashed etc.)

**Fill** refers to the colour of the inside of the circle and **stroke** refers to the circle outline.

Let’s start with the renewable energy type. An obvious choice for this circle is **green**:

{width: 15%}
![Solid green circle for renewable energy](6cc95d1a7b2bf7d04816352145e94496.png)

Similarly, an obvious choice for Oil, Gas and Coal is **black** or **grey**. Black is probably too strong a colour, so let’s use grey. If we use a filled grey circle we have to make sure smaller circles are drawn in front of larger circles. An easier approach is to use an empty circle with a grey stroke:

{width: 15%}
![Grey outlined circle for oil, gas and coal](db13edf95e7a18db402c8953b7b7a180.png)

Hydroelectric refers to energy produced from water pressure (think of water behind a dam). **Blue**‘s an obvious choice for anything related to water so let’s choose blue for hydroelectric power:

{width: 15%}
![Blue outlined circle for hydroelectric](03f6f971423838cfe8e9aac726a51cfe.png)

Finally we have nuclear. The nuclear power symbol has a yellow background so yellow might be a good choice. A yellow outline won’t show up very well against a light background so perhaps **orange** works well:

{width: 15%}
![Orange outlined circle for nuclear](be83c4a8ec8f5d1508f1da165b0fed05.png)

We’re using **three colours** (green, blue and orange) which might not be colour blind safe so let’s also change the stroke style of the circles.

Let’s use a **short dashes** for the hydroelectric circles and **long dashes** for the nuclear circles:

{width: 15%}
![Use dashed outlines to further distinguish the circles](e735b05075d732ba6b61acbd55693f79.png)

It should now be possible to identify the energy type of each circle even if colour blind.

I don’t have a design background (I have an engineering background) so choosing colours isn’t my strongest skill. I often visit [coolors.co](https://coolors.co/) which lets you cycle through different colour schemes. I like the look of the colours that coolors generates and I usually keep cycling through until I spot a colour I like the look of. This is how I found the particular hues of green, blue and orange seen above.

D>If you'd like to learn more about designing data visualisation colour schemes a good place to start is Lisa Charlotte Muth's [A detailed guide to colors in data vis style guides](https://blog.datawrapper.de/colors-for-data-vis-style-guides/).

Here’s a table with the colours and styles we’ll use:

| Energy type | fill | stroke | stroke-dasharray |
| --- | --- | --- | --- |
| renewable | #7FB069 | none | none |
| oilgascoal | none | #BCB5B2 | none |
| hydroelectric | none | #5EB1BF | 3,1 |
| nuclear | none | #F6AE2D | 4,2 |

`stroke-dasharray` is a CSS property that defines a stroke’s dash style. The first number defines how long the dash is and the second number defines the size of the gap between the dashes.

## Style the circles

In `css/style.css`  add CSS rules for each of the four energy types:

{caption: "css/style.css"}
```css
markua-start-delete
circle {
    fill: none;
    stroke: #aaa;
}
markua-end-delete

markua-start-insert
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
markua-end-insert

.country .label {
    text-anchor: middle;
}
```

(Make sure the old rule for all `circle` elements is removed.)

In the previous practical we assigned a class attribute on each circle indicating which energy type it's representing. This allows us to select each circle type in `style.css` and style it accordingly.

## Center the visualisation

In `index.html` wrap the `svg` element in a `div` element with class attribute `wrapper`:

{caption: "index.html", line-numbers: false}
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Energy mix by Country 2015</title>
    <link rel="stylesheet" href="css/style.css">
  </head>

  <body>
markua-start-insert
    <div id="wrapper">
markua-end-insert
      <svg width="1200" height="1200">
        <g id="chart"></g>
      </svg>
markua-start-insert
    </div>
markua-end-insert

    <script src="js/lib/d3.min.js"></script>

    <script src="js/config.js"></script>
    <script src="js/layout.js"></script>
    <script src="js/update.js"></script>
    <script src="js/main.js"></script>
  </body>
</html>
```

The main reason for adding the `div` element is that further interface elements (such as a menu and a legend) will be added later on and it’s good practice to enclose them in a single wrapper element.

In `css/style.css` add a rule for the new `div` element:

{caption: "style.css", line-numbers: false}
```css
markua-start-insert
#wrapper {
    width: 1200px;
    margin: 0 auto;
}
markua-end-insert

circle.renewable {
    fill: #7FB069;
}

...
```

To center a `div` element you can set its width and set its margin to `0 auto`. This isn’t at all obvious if you don’t have much CSS experience but it’s common practice.

## Change the background colour

Finally in `style.css` set the background colour to off-white:

{caption: "style.css", line-numbers: false}
```css
markua-start-insert
body {
    background-color: #FFFFF7;
}
markua-end-insert

#wrapper {
    width: 1200px;
    margin: 0 auto;
}

circle.renewable {
    fill: #7FB069;
}

...
```

## Save and refresh

Save `index.html` and `style.css` and load `step9` in your browser. You should see that the circles are styled according to energy type, the visualisation is centered and the background is off-white:

![Energy Explorer with styled circles and off white background](11ab4a0dec90485bd7bf728013ab78e5.png)

## Summary

A major part of the visualisation is finished now. Each country is represented by four circles and you can now begin to compare the energy mixes of different countries.

I’m personally drawn to the solid green circles. The larger they are, the more renewables contribute to that country’s energy mix. Denmark’s a good example. Albania’s an interesting case as it looks like the vast majority of its energy comes from hydroelectric (blue outline). And not surprisingly for many countries, the biggest contributor is oil, gas and coal (grey outline).

In the next few chapters we look at adding an information popup that appears when a country is hovered over.