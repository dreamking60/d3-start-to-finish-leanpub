# Practical: Size the Circles

In this chapter we modify Energy Explorer to size each circle according to its **renewable energy** percentage. (This is the percentage of a country’s energy mix that comes from renewable energy.)

By the end of this practical Energy Explorer will look like:

![The energy data represented by circles, sized according to each country's renewable energy percentage](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-1.png)

## Overview

Open `step4` from the code download. The file structure is:

```text
step4
├── data
│   └── data.csv
├── index.html
└── js
    ├── lib
    │   └── d3.min.js
    └── main.js
```

Recall that in the previous practical we joined the energy mix array to circle elements.

In this practical we:

1. Convert the indicator values from strings into **numbers**.
2. Create a `scaleSqrt` **scale function** and use it to **set the radius** of each circle.

## Convert indicator values from strings into numbers

When `d3.csv` loads a CSV file it treats all values (including numbers) as strings. Therefore we need to convert each of the indicator values (`hydroelectric`, `nuclear`, `oilgascoal` and `renewable`) into numbers using `parseFloat`. We use a transformation function (as explained in the Requesting Data with D3 chapter):

{caption: "js/main.js", line-numbers: false}
```js
let data;

...

function dataIsReady(csv) {
    data = csv;
    update();
}

markua-start-insert
function transformRow(d) {
    return {
        name: d.name,
        id: d.id,
        hydroelectric: parseFloat(d.hydroelectric),
        nuclear: parseFloat(d.nuclear),
        oilgascoal: parseFloat(d.oilgascoal),
        renewable: parseFloat(d.renewable)
    };
}

d3.csv('data/data.csv', transformRow)
markua-end-insert
    .then(dataIsReady);
```

`transformRow` is a new function that converts the four indicator values into numbers using `parseFloat`. (If the value is missing, `parseFloat` returns `NaN` which stands for 'not a number'.)

For example, if the following is passed into `transformRow`:
```js
{
    hydroelectric: "53.2",
    id: "AGO",
    name: "Angola",
    nuclear: "",
    oilgascoal: "46.8",
    renewable: "0.0"
}
```
the following is returned:
```js
{
    hydroelectric: 53.2,
    id: "AGO",
    name: "Angola",
    nuclear: NaN,
    oilgascoal: 46.8,
    renewable: 0.0
}
```


## Create a sqrtScale function and set the circle radii

In `js/main.js` we make the following changes:

{caption: "js/main.js", line-numbers: false}
```js
let data;

markua-start-insert
let radiusScale = d3.scaleSqrt()
    .domain([0, 100])
    .range([0, 20]);
markua-end-insert

function update() {
    d3.select('#chart')
        .selectAll('circle')
        .data(data)
        .join('circle')
        .attr('cx', function(d, i) {
            return i * 10;
        })
        .attr('cy', 100)
markua-start-insert
        .attr('r', function(d) {
            return radiusScale(d.renewable);
        });
markua-end-insert
}

function dataIsReady(csv) {
    data = csv;
    update();
}

function transformRow(d) {
    return {
        name: d.name,
        id: d.id,
        hydroelectric: parseFloat(d.hydroelectric),
        nuclear: parseFloat(d.nuclear),
        oilgascoal: parseFloat(d.oilgascoal),
        renewable: parseFloat(d.renewable)
    };
}

d3.csv('data/data.csv', transformRow)
    .then(dataIsReady);
```

A `scaleSqrt` scale function is created with domain `[0, 100]` and range `[0, 20]` and assigned to `radiusScale`. The domain is set to `[0, 100]` because the data values vary between 0 and 100 and the range is set to `[0, 20]` so that the maximum circle radius is 20. We’ll set this to a more suitable value later in the book. We saw in the previous chapter that `scaleSqrt` is the correct scale to use when using circle area to represent a quantity.

We now pass a function into `.attr('r', ...)` so that the radius is set according to the joined value (instead of a constant value). For now we use the `renewable` property and apply the `radiusScale` scale function to it.

Therefore each circle will be sized such that its area is proportional to the `renewable` property.

Save `main.js` and load `step4` in your browser. The circles now look like:

![](https://learn.createwithdata.com/wp-content/uploads/2021/04/image-2.png)

Each circle represents a county and is sized according to its renewable energy percentage. We can already see that there’s a few countries that appear to have quite a high percentage and plenty others with a low percentage.

Later on in this book we’ll arrange the circles more effectively and add labels so we can see the individual countries more clearly.