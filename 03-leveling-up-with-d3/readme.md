# Part 3: Leveling up with D3
_Portions of this class were inspired by the approach taken in the book Interactive Data Visualization for the Web: An Introduction to Designing with D3 by Scott Murray (2nd edition)._

## What are we doing here?
By now, you've learned the basics of D3. You've created a simple bar chart and have attached some numbers to it. For this part of the class, we're going to look at a more practical application of d3 -- taking data from a dataset and visualizing it with a chart.

D3 can work with many different types of data formats, but the most common for charting is a .csv file. We'll use a small .csv file to create a bar chart showing the rise of renewable energy in Iowa. 

Once we've used the data to create a simple bar chart, then we'll get into some of the trickier aspects of d3 charting. We'll add an x- and y-axis and tackle making the chart responsive. Lastly, we'll add some tooltips to our newly-responsive chart!

## Attaching data to a bar chart
We're going to create a new bar chart, different from the one you just made. This one will eventually have a data from a .csv file attached to it. Navigate to the folder `03-leveling-up-with-d3/` (the same folder where this readme.md file is located) and create a new text file. Save it with a name such as `chart2.html`. If you get lost, refer to `05-iowa-energy-bars.html`.

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
                return data.map(
                    function (row) {
                        return {
                            year: row.year.substring(0,4),
                            megawatts: +row.net_generation,
                        }
                    }
                )
            }
```
This will change rework your data into something more workable. Now, let's bind the svg to the page and take a look at how it comes together. Below where your `transformData` function ends, paste:

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
`d3.csv('iowa-renewable-energy.csv', function(data){ console.log(data) });` to `d3.csv('iowa-renewable-energy.csv').then(makeChart);`. If you look in the console, you should the same dataset, but formatted differently.

