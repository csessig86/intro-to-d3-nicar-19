# Part 2: Diving into D3

*Portions of this class were inspired by the approach taken in the book [Interactive Data Visualization for the Web: An Introduction to Designing with D3](https://alignedleft.com/work/d3-book-2e) by Scott Murray (2nd edition).*

## What are we doing here?

You may have produced a chart in the past, using a tool such as Excel, python or R. D3, the backbone of some of the most impressive visualizations on the web today, is **not** a charting library. But it gives us some handy tools to let data become embedded in the browser, which can then render the pieces that build up to a chart.

Today, we're going to focus on the fundamentals of D3.

Our data visualization of choice will be the bar chart. We'll build one bar, then a few more, and then add some dressing to create a production-level graphic. We are going to create these files from scratch. For reference, the `final-charts/` folder contains the end result of every part of this tutorial. Refer to it if you get lost.

To understand the process of D3, it helps to take a step back from javascript. We'll start by working with only HTML and CSS.

## Drawing a bar

Let's start a sketch. Navigate to the folder `02-diving-into-d3/` (the same folder where this readme.md file is located) and create a new text file. Start by saving it with a name such as `chart1.html`.

Copy and paste this basic HTML template into the page:

```html
<html>
    <head>
        <style type="text/css">
            <!-- CSS styles go here -->
        </style>
    </head>
    <body>
        <!-- HTML elements go here -->
        <script type="text/javascript">
            <!-- javascript goes here -->
        </script>
    </body>
</html>
```

A bar is just a rectangle, and the easiest way to draw a rectangle is with a `div` tag. Divs are a flexible HTML element. They can be used as containers to group sections of code, or be elements on their own. You can put one on the page by typing `<div></div>` in the body of the code. Save the text file, then open the page in your browser. It should be an empty page.

Let's tell the browser we want to make this div look like a rectangle. This is possible by adding styles to the element, such as a narrow width and a tall height for a vertically oriented bar. By default the bar will appear white, which means we won't see it on the white background. Let's add a color by modifying the div's background color property. HTML supports [more than 150 colors by named reference](https://htmlcolorcodes.com/color-names/). Pick your favorite. We are going to use teal.

Style declarations can go right on the initialization of the div. Here's what you should end up with:

```html
<div style="width:20px; height:75px; background-color:teal"></div>
```

Save the text file and refresh the browser. (Get used to doing this a lot.) Boom. You have a solitary bar.

### Styling with CSS

Declarations of style be written onto the opening of any HTML tag. But in long files, it becomes cumbersome to repeat style declarations over and over again in HTML files.

That's where Cascading Style Sheets, or CSS, comes in. CSS allows you to separate the writing of the styles from the writing of the HTML. This is handy because, for one, each is written in a completely different syntax. It also allows for more efficient and less repetitive code. By assigning names to certain HTML elements, we can then use CSS to add style to anything with a particular name.

Here's what we'll do to separate the style from the HTML in this example. Start by giving the div a name, or what HTML calls a **class**. We've gone with "bar" for the class. All you need to do is put `class="bar"` alongside the style declaration.

Now let's move the styling code into the `<style>` tag at the top of the file. We first have to refer to what we're styling. In this case, `div.bar` refers to the div with the class bar. Classes are referred to using a period (`.bar` in this case). HTML tags like `<div>` stand alone.

Then, add a couple of curly brackets with space in between after the declaration, and move the styles we wrote on the div into there. You should end up with this:

```css
div.bar {
    width: 20px;
    height: 75px;
    background-color: teal;
}
```

Now strip off the styles from the div, leaving only `<div class="bar"></div>`. Refresh the page. It should look identical to what we had before. Same bar, two different ways to get there.

## Drawing a bar with D3

Now we'll introduce another way to get the same bar by using D3.

Make a new file, something like `chart2.html`, and paste the template from above into it.

### Importing D3

We are going to include D3 as a separate javascript file, just like the `script.js` file from part one of this tutorial. We've gone ahead and added `d3v5.js` to this folder for ease of import. It contains version 5.7.0 of the library. Add it to your HTML file by putting the following line into the head of the page: `<script src="d3v5.js"></script>`

This is the full file, which comes in at about 500 kilobytes. If file sizes are a concern for you, the minified file contains identical code and takes up about half the space. You can even choose to import the web version of the [full](https://d3js.org/d3.v5.js) or [minified](https://d3js.org/d3.v5.min.js) file.

The full functionality of the D3 library is now available to us.

### Appending elements

D3 works by by injecting HTML code into the browser. To do this, you use D3 to point to an element, then specify what you want to **append** inside the element. It operates very literally.

We need to get a `div` with the class `bar` into the `<body>` tag. Here's how D3 does that:

```javascript
d3.select("body").append('div').attr('class','bar')
```

Plug that line of code into the space for javascript in the template, then open the browser to the HTML file. A blank page should appear. But crack open the inspector and check out the elements on the page.

![Inspector](mdfiles/inspector.png)

Here's what D3 did: It grabbed onto the `<body>` tag, added a `<div>` tag inside it and then added the attribute `class="bar"` to the div. The page is identical to our text file, except for the new div inserted at the bottom. It's important to remember: With an append, D3 will always add things to the *bottom* of the element you select, after the items that already exist. That's why our div went below the `<script>` tag.

But the bar doesn't have any style. So copy and paste the style declaration from the previous example, and place it in the code. Now you have the same bar as before.

## Drawing bars based on data

Our first example created a single bar. Now we'll use create multiple bars using some data.

Create a new file -- `chart3.html` -- with the same shell as before. Start by throwing the D3 into the head (`<script src="d3v5.js"></script>`) then go ahead and reuse the CSS from the previous example.

We'll start by creating an array in the script tag at the bottom. You can create any kind of array you want, with any numbers. We're going to use this:

```javascript
var dataset = [ 5, 10, 15, 20, 25 ];
```

Our goal is to create a bar chart, with heights corresponding to numbers in the array. Previously, we used CSS to define the height of a single bar. It follows that you could copy and paste that code repeatedly to create bars of different heights. D3 saves us a lot of time and typing.

The code it takes to do it may not makes sense at first. Plug this into your script tag before we break it down line by line.

```javascript
d3.select("body")
    .selectAll("div")
        .data(dataset)
        .enter()
        .append("div")
            .attr("class", "bar")
```

`d3.select("body")`: Just as before, we need to make a selection that identifies where we're going to place the elements we are about to create.

`.selectAll("div")`: This is selecting all div items that are within the body tag. You might think "There are no divs within the body" and you would be right. However, selecting items that are about to be created is a convention D3.

`.data(dataset)`: We are now making our small data set available to D3.

`.enter()`: Perhaps the most crucial step in this process. By using enter, we have now gained the ability to add items repeatedly the page. Anything we ask D3 to do after this will be repeated N times, where N is the number of items in the array (in our case, five times). It also preserves the *values* of the array for use later. This is what is D3 officially refers to as *binding* data to the DOM.

`.append("div")`: Here's where we append a div just as before. But now, this append step is going to be run five times. Run the inspector to see five divs now sitting in the page.

`.attr("class", "bar")`: Just as before, we need to add the `bar` class to the div so the styles written in the CSS can take effect. Except now, this is running on each of the five divs. This also raises a peculiarity of D3 that is worth remembering: Any maniuplation only takes effect on the *last* thing that has been selected or appended. Just like the `selectAll` only worked on the body, and the `append` worked on the just-created div, this class assignment will work on the newest divs.













