# Part 2: Diving into D3

*Portions of this class were inspired by the approach taken in the book [Interactive Data Visualization for the Web: An Introduction to Designing with D3](https://alignedleft.com/work/d3-book-2e) by Scott Murray (2nd edition). We'll be using d3.js version 5.7.0.*

## What is D3?

You may have produced a chart in the past, using a tool such as Excel, python or R. D3, the backbone of some of the most impressive visualizations on the web today, is **not** a charting library. But it gives us some handy tools to let data become embedded in the browser, which can then render the pieces that build up to a chart.

Today, we're going to focus on a simple bar chart. We'll build one bar, then a few more, then add some dressing to create a production-level graphic.

To understand the process of D3, it helps to take a step back from javascript. We'll start by relying solely on **HTML** and **CSS** to craft a bar.

## Drawing a bar

Let's start our sketch. Navigate to the folder `02-diving-into-d3` (the same folder where this readme.md file is located) and create a new text file. Start by saving it with a name you can remember later, such as `chart1.html`.

Copy and paste this basic HTML outline into the page:

```html
<html>
    <head>
        <style type="text/css">
        <!-- style goes here -->
        </style>
    </head>
    <body>
        <!-- html elements go here -->
        <script type="text/javascript">
            <!-- javascript goes here -->
        </script>
    </body>
</html>
```

