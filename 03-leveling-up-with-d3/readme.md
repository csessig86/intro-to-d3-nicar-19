# Part 3: Leveling up with D3
_Portions of this class were inspired by the approach taken in the book Interactive Data Visualization for the Web: An Introduction to Designing with D3 by Scott Murray (2nd edition)._

## What are we doing here?
By now, you've learned the basics of D3. You've created a simple bar chart and have attached some numbers to it. You've learned about x- and y-positioning in an SVG. Now, for this part of the class, we're going to look at a more practical application of d3 -- extracting data from a dataset and visualizing it with a chart.

D3 can work with many different types of data formats, but the most common for charting is a .csv file. We'll use a small .csv file to create a bar chart showing the rise of renewable energy in Iowa. 

Once we've used the data to create a simple bar chart, then we'll get into some of the trickier aspects of d3 charting. We'll add an x- and y-axis and tackle making the chart responsive. Lastly, we'll add some tooltips to our newly-responsive chart!

## Attaching data to a bar chart
We're going to create a new bar chart. This one will eventually have a data from a .csv file attached to it. Navigate to the folder `03-leveling-up-with-d3/` (the same folder where this readme.md file is located) and create a new text file. Save it with a name such as `chart_final.html`. If you get lost, refer to `05-iowa-energy-bars.html`.

Copy and paste this basic HTML template into the page:
```html
<html>
    <head>
        <title>The rise of renewable energy in Iowa</title>
        <script type="text/javascript" src="d3v5.js"></script>
        <style type="text/css">
        </style>
    </head>
    <body>
        <script type="text/javascript">
        //javascript will go here
        </script>
    </body>
</html>
```

Now, we're going to read the `.csv` file using d3. To do that, we'll call the csv file in our javascript like so:
```javascript
<script type="text/javascript">
            d3.csv('iowa-renewable-energy.csv', function(data){
                console.log(data)
            });
</script>
```
Now peek into your console. You should see all your data, helpfully formatted for you by d3 into something easily readable. However, this isn't exactly what we want for our chart. We don't want the full dates, just the year, and we'll want the units to be in megawatts. To do this, paste this above where you called your csv:
```javascript
function transformData(data) {
    return data.map(function (row) {
        return {
            year: row.year.substring(0,4),
            megawatts: +row.net_generation,
        }
    })
}
```
`data.map` is a d3 function that will re-structure your data into an iterable object with key-value pairs. After we've restructured the data, we then format the data to only include two categories: years, which we've reformated from its original `YYYY-MM-DD` format, and megawatts. 

Now, let's bind the svg to the page and take a look at how it comes together. Below where your `transformData` function ends, paste:

```javascript
function makeChart(rawData) {
    var chartWidth = 500;
    var chartHeight = 200;
    var canvas = d3.select('body')
        .append('svg')
            .attr('id', 'container')
            .attr('width', chartWidth)
            .attr('height', chartHeight)
    var data = transformData(rawData);
    console.log(data);
}
```
We'll build the rest our basic chart inside this function. Right now, we've appended a blank SVG to the body of our page, with a height and width that we've specified. Lastly, we're running our `transformData` function to format our data for our chart in the way we want.

To see how these two functions come together, change the last line of your code from 
`d3.csv('iowa-renewable-energy.csv', function(data){ console.log(data) });` to `d3.csv('iowa-renewable-energy.csv').then(makeChart);`. If you look in the console, you should see the same dataset, but formatted in a different, more straightforward way.

Let's start making the chart! First, our x- and y-axes. Under `var data = transformData(rawData);` paste: 
```javascript
var xScale = d3.scaleBand()
    .domain(data.map(function(d) { 
        return d.year 
    }))
    .range([0, chartWidth])
    .padding(0.1);
var yScale = d3.scaleLinear()
    .domain([0, d3.max(data, function(d) { return d.megawatts })]).nice()
    .range([chartHeight, 0]);
```
This will set the units for each axes. The x-axis uses the *years* in the csv, and sets the width of the chart based on the variable we declared above. We can also set some padding between bars. The y-axis is set by the *megawatts*, and uses the height we've specified as a maximum value. If you're curious about what's going on in each function, you can always hop in the console -- for example, if you change your xScale function to: 

```javascript
var xScale = d3.scaleBand()
    .domain(data.map(function(d) { 
        return d.year 
        console.log(d.year);
    }))
    .range([0, chartWidth])
    .padding(0.1);
``` 
You'll see a list of every year in the dataset. Now let's draw the bars. After your `yScale` variable, paste:
```javascript
var bars = canvas.append('g')
    .attr('id', 'bars')
    .selectAll("rect")
    .data(data)
    .enter()
    .append("rect")
    .style("fill", "teal")
    .attr("x", function(d) { return xScale(d.year); })
    .attr("y", function(d) { return yScale(d.megawatts); })
    .attr("height", function(d) { return chartHeight - yScale(d.megawatts); console.log(yScale(d.megawatts))})
    .attr("width", xScale.bandwidth())
    .attr("data-year", function(d) { return d.year })
    .attr("data-megawatts", function(d) { return d.megawatts })
```

Now refresh your page and you should see something that looks just like a chart! You've already gone over the basics of creating bars using `rect` in d3, so let's take a look at how d3 takes the data we've input to create the x and y positions.

The x-values are all roughly 29-30 units apart, because they all need to be equally spaced along the axis. The y-values uses the axis function we created earlier to position the bars. The height is calculated by subtracting that value by the height of the overall div. 

Now that we've got the bones of our chart, let's draw and label our axes.

## Creating axes on a d3 chart

We'll be working off of the code in `06-iowa-energy-axes.html` if you need a reference.

First, we'll need to make space for the axes -- right now, our chart is taking up all of the room on our canvas, so let's add some breathing space. 

In your `makeChart` function, add the following line after the `chartHeight` variable:
```script
var chartMargin = {top: 10, right: 10, bottom: 10, left: 20};
```
Now we'll need to change things up a bit. In our previous chart, we just made one `g` group and appended it to the HTML body. We'll need to create a couple to add our axes, so let's first make one container SVG to hold everything in. After your `chartMargin` variable, paste:

```javascript
var container = d3.select('body')
    .append('svg')
    .attr('id', 'container')
    .attr('width', chartWidth + chartMargin.left + chartMargin.right)
    .attr('height', chartHeight + chartMargin.top + chartMargin.bottom);
```
Delete your existing `var canvas` and replace it with: 

```javascript
var canvas = container.append('g')
    .attr('id', 'canvas')
    .attr('transform', 'translate(' + chartMargin.left + ',' + chartMargin.top + ')');
```

If you refresh your page, you should see the same chart, but with some space around the sides. We've made room for our scales. 

<img src="https://github.com/csessig86/intro-to-d3-nicar-19/blob/master/03-leveling-up-with-d3/large-margins.png?raw=true" width="500px"/>

<img src="https://github.com/csessig86/intro-to-d3-nicar-19/blob/master/03-leveling-up-with-d3/margins.png?raw=true" width="500px"/>

Now let's add the text for our scales.

At the bottom of your `makeChart` function, paste:

```javascript
function xAxis(g) { 
    return g.attr("transform", 'translate(0,' + (chartHeight - chartMargin.bottom) + ')') // positions the scale at the         (almost) bottom of the canvas.
    .call(d3.axisBottom(xScale).tickSizeOuter(0)) // (0) prevents ticks being drawn on the far left and right of the axis, gives it a cleaner look
}
function yAxis(g) { 
return g.attr("transform", 'translate(' + chartMargin.left + ',0)') // positions scale on the left side of chart, flush with the edge of the chart
    .call(d3.axisLeft(yScale))
}
canvas.append('g').call(xAxis); // draw the scales
canvas.append('g').call(yAxis);
```
D3's charting functions make it easy to draw a scale with evenly spaced ticks on the x- and y-axis, which we've done here. But if you refresh your page, you'll see things don't look quite right.

<img src="https://github.com/csessig86/intro-to-d3-nicar-19/blob/master/03-leveling-up-with-d3/imgs/bad-axes.png?raw=true" width ="450px" />

That's because we need to tell d3 to draw our chart with regard to the margins we specified. Let's do that now. Change your `xScale` and `yScale` variables to:

```javascript
var xScale = d3.scaleBand()
    .domain(data.map(function(d) { return d.year }))
    .range([chartMargin.left, chartWidth - chartMargin.right]) // sets the drawing starting point 20px to the left, and will make it 10px less wide.
    .padding(0.1);
var yScale = d3.scaleLinear()
    .domain([0, d3.max(data, function(d) { return d.megawatts })]).nice()
    .range([chartHeight - chartMargin.bottom, chartMargin.top]); // sets the drawing starting point 10px below where it originally was, making room for the x-axis.
```
Since we've resized the scales, we'll also need to change the height of our bars. In your `var bars` variable, change the `attr("height")` function to:

```javascript
.attr("height", function(d) { return (chartHeight - chartMargin.bottom) - yScale(d.megawatts); }) //shrinks the height of the bars by the specified margin.
```

Now, if you refresh you should see a properly spaced chart with nice x- and y-axes!

<img src="https://github.com/csessig86/intro-to-d3-nicar-19/blob/master/03-leveling-up-with-d3/imgs/scales.png?raw=true" width = "450px" />

Looks great! But what if we shrink our browser? It's time to make this chart responsive.

## Making d3 charts responsive

We'll be working off of `07-iowa-energy-responsive.html` if you need guidance. Let's get started!

There are many, many ways to make a d3 chart responsive, but we're going to go with our favorite: Redrawing the chart every time we resize the page. This method is straightforward and works well for small charts like this.

For the first time in this section, we're going to add some HTML and CSS to our page. In your empty `<style></style>` tags, add:

```CSS
#chartholder {
    max-width: 800px;
}
```

In the beginning of your `<body></body>` section, add:

```HTML
<div id='chartholder'> <!-- d3 will put the chart here --> </div>
```

Now, let's write a line that will tell us if we're looking at this chart on a desktop or phone-sized window. In at the top of your `makeChart` function, add:

```javascript
    var windowWidth = window.innerWidth;
    var isMobile = windowWidth <= 767 ? true : false;
```

If we type `console.log(isMobile)` in the function and refresh, we'll see if we are looking at a desktop or phone-sized screen.

Now, we'll need to fiddle with the size of our chart width and height. For our desktop view, we'll draw a wider, shallower chart, but we'll want a taller and more narrow chart on a mobile screen. Replace your `chartWidth` and `chartHeight` variables with:

```javascript
var chartWidth = document.getElementById("chartholder").clientWidth - chartMargin.left - chartMargin.right; // uses the div we created in HTML to set the width of the chart
var chartHeight = isMobile ? 400 : 200; // will be 200px tall on desktop and 400px on a phone.
```

Then, we'll change our `var container` variable to append the chart to the div we made in the HTML. Change the variable to:
```javascript
var container = d3.select('#chartholder')
    .append('svg')
    .attr('id', 'container')
    .attr('width', chartWidth + chartMargin.left + chartMargin.right)
    .attr('height', chartHeight + chartMargin.top + chartMargin.bottom);
```

Now, if you refresh you'll see a wider, shallow chart -- ideal for a desktop view!

<img src="https://github.com/csessig86/intro-to-d3-nicar-19/blob/master/03-leveling-up-with-d3/imgs/desktop.png?raw=true" width="500px"/>

But if you resize the page, you'll see our chart runs offscreen. Let's fix that. First, we'll remove `var data = transformData(rawData);` from our `makeChart` function. We'll call it in a minute.

Then, at the very bottom of your script, change your `d3.csv` script to:

```javascript
var data;
d3.csv('iowa-renewable-energy.csv').then(
    function(d) {
        data = transformData(d);
        makeChart();
        window.addEventListener('resize', makeChart);
    }
);
```
Make sure you declare `var data;` above the function! Now, when you resize...you'll see that it's just drawing the same chart over and over. We have one small step left to fix that. At the *very beginning* inside your `makeChart` function, add this line:

```javascript
d3.select('svg#container').remove();
```

This will remove and redraw your chart every time to resize the page. Let's try it! You should see a smoothly resizing chart.

We're almost there. Last step: Adding tooltips.

## Tooltips in d3 charts
We'll be following `08-iowa-energy-tooltips.html` if you need a reference.

To make this tooltip, we'll make a small, absolutely positioned div that moves with our mouse. Right below `canvas.append('g').call(yAxis);`, we'll create a variable called `var tooltip`, and using d3, we'll attach a div to the body of the page like so:
               
```javascript
var tooltip = d3.select("body").append("div")
.style("position", "absolute")
.style("z-index", "10")
.style("visibility", "hidden")
.style("color", "white")
.style("padding", "8px")
.style("background-color", "rgba(0, 0, 0, 0.75)")
.style("border-radius", "6px")
.style("font", "12px sans-serif")
.text("tooltip");
```
If you refresh your page, you won't see anything different, because we've set the `visibility` to `hidden.` It's also empty -- we haven't put any text in it yet. Now, let's get it on the page. We won't want a tooltip on a phone, because they're hard to read and use on a mobile device. We'll first create an if statement, which we'll put under our tooltip variable:

```javascript
if (!isMobile) {
    //tooltip code will go here -- function will only run on a 768px screen or greater.
}
```
Now, in our if statement, we'll add some tooltip text using the data from our .csv:

```javascript
bars.on("mouseover", function(d) {
    tooltip.html('Year: ' + d.year + '<br />' + (d.megawatts) + ' megawatts');
    tooltip.style("visibility", "visible");
});
```
If we refresh, we'll see a small tooltip at the bottom of our chart when we hover over the bars!

<img src="https://github.com/csessig86/intro-to-d3-nicar-19/blob/master/03-leveling-up-with-d3/imgs/tooltip_first.png?raw=true" width="550px"/>

But this isn't exactly what we want. First, let's add some commas to those numbers. Paste this function *above* your `if (!isMobile)`statement: 
```javascript
function numberWithCommas(x) {
    return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
}
```

Then, inside your mouseover function, replace the `tooltip.html` line with:
```javascript
tooltip.html('Year: ' + d.year + '<br />' + numberWithCommas(d.megawatts) + ' megawatts');
```

If you refresh, you'll see that the numbers in your tooltip now have commas, but they're still at the bottom of the page. We want the tooltip to appear over the bars when we hover over them, and to disappear when our mouse is off the bars. 

Inside your `if (!isMobile)` statement and under the `bars.on("mouseover")` function, paste:
```javascript
.on("mousemove", function() {
    return tooltip.style("top", (d3.event.pageY - 10)+"px").style("left",(d3.event.pageX + 10)+"px");
})
.on("mouseout", function() {
    return tooltip.style("visibility", "hidden");
});
```

Your if statement should look like this in full:
```javascript
if (!isMobile) {
    bars.on("mouseover", function(d) {
        tooltip.html('Year: ' + d.year + '<br />' + (d.megawatts) + ' megawatts');
        tooltip.style("visibility", "visible");
    })
    .on("mousemove", function() {
        return tooltip.style("top", (d3.event.pageY - 10)+"px").style("left",(d3.event.pageX + 10)+"px"); // positions the tooltip slightly offset to where the mouse pointer is
    })
    .on("mouseout", function() {
        return tooltip.style("visibility", "hidden"); // hides the tooltip when you mouse off of the bars
    });
}
```
Refresh your page and you should see a beautiful tooltip moving with your mouse! You did it!
<img src="https://github.com/csessig86/intro-to-d3-nicar-19/blob/master/03-leveling-up-with-d3/imgs/tooltip_end.png?raw=true" width="550px"/>

## What's next?

Now you can play around with everything we just did -- you can mess around in the CSS, try adding and parsing your own data -- whatever you want! The d3 world is your oyster.
