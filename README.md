<h2>Why use SVG?</h2>
<p>SVG is a great choice of web graphic when the following things matter to you:</p>

<ul>
  <li>You want the graphic to maintain clarity at all sizes</li>
  <li>You need a really small graphic (logo, icon, etc.)</li>
  <li>You need a graphic with flexible sizing and you don't want to create many versions of the same file to
      achieve this</li>
  <li>You want advanced graphic colors and animations</li>
</ul>

<p>My rule of thumb is to use an SVG when a basic png or jpg graphic just won't cut it (sizing, clarity, animations, etc.).</p>

<h3>5 ways to render SVGs in the browser</h3>
<p>So... How do you use SVGs?</p>

<ol>
  <li>1. HTML img element - easy to use and fully responsive (very common usage)</li>
  <li>2. HTML iframe element - generally, iframes should be for embedding external webpages (not SVGs), so at the risk of getting yelled at by someone more knowledgeable on this subject, I don't see any use-case for iframe + SVG</li>
  <li>3. HTML embed and object elements - I would not recommend either of these as they are outdated with better alternatives. There is one arguable use case for object though, which is a PDF embed.</li>
  <li>4. CSS url - great for setting backgrounds, custom bullet point icons, and site logos inside an a tag</li>
  <li>5. Inline SVG element - great for when you want full control of the SVG (scaling, colors, animations, etc.)</li>
</ol>

<p>Below is a Codepen with examples of each usage.</p>
<a href="https://codepen.io/zg_dev/pen/mdLPGLO">Codepen.io</a>

<p>Note: when inserting a raw SVG element into HTML, you must provide a namespace equal to xmlns=&quot;http://www.w3.org/2000/svg&quot;. This ensures that when different dialects of XML (what SVG is uses) are used within a single HTML document, there are no clashes between tags. For example, both MathML and the SVG spec support a <title> tag. Without this namespace, the browser wouldn't know which tag to use!</p>

<h3>How to control the size and scale of an SVG</h3>
<p>Scaling SVGs depends on how you render it in the DOM. While we saw many methods for doing this, for the remainder of this post, we'll be focusing on the inline and img usage of SVGs as they are the most practical and common ways to use SVG.</p>

<h4>Scaling an Image SVG</h4>
<p>If you want a fast and easy way to responsively scale an SVG, use an img tag and set a width on it.</p>

```
<!-- This SVG will scale to 200px and maintain its aspect ratio and clarity -->
<!-- Note: this assumes that either explicit dimensions or a viewBox attributes has been set on the referenced SVG (more on this soon) -->
<img src="some-graphic.svg" width="200px" />

<!-- This also works -->
<img src="some-graphic.svg" style="width: 200px;" />
```

<h5>Here are some considerations to this approach:</h5>

<ol>
  <li>By using img, you get the benefit of image caching</li>
  <li>By using img, you lose the ability to style and animate the SVG</li>
</ol>
<p>Note: the SVG file you reference must have either dimensions already set or the viewBox attribute set. If none of these are present, the SVG will be a fixed size and will not scale with the image size. We will talk more about these attributes in subsequent sections.</p>

<h3>What is an SVG "Viewport"?</h3>
<p>One of the most important concepts to understand with SVG is the viewport.</p>

<p>You can think of this as the "lens" or "portal" that you are viewing the actual SVG from. Below, I have created 2 SVG graphics and have put a gray border on the "viewport". The SVG is exactly the same in both, but as you can see, the viewport is 48px x 32px in the left image and 64px x 64px in the right image.</p>

<p>Even though the SVG is the same, the left image appears to be cropped because the "lens" / "portal" / "viewport" that we're looking through is smaller than the actual SVG.</p>

<p>Go ahead, drag the bottom right corner of both and see what happens.</p>

<p><a href="https://codesandbox.io/p/sandbox/compassionate-butterfly-6gfty5?file=%2Fsrc%2FApp.tsx&from-embed">CodeSandbox Example</a></p>

<p>Think of this like an airplane window. Does the sky still exist when the window is closed? Of course! You just have to open it wider to see the sky.</p>

<h4>An analogy to remember:</h4>
<ul>
  <li>Plane Window = SVG Viewport</li>
  <li>Sky = SVG contents</li>
</ol>

<p>Note: did you notice what happened to the SVG size when you changed the size of the viewport? It stayed the same... We'll talk about ways to scale the SVG as its viewport size changes later in this post.</p>

<h3>How to adjust the SVG viewport size</h3>
<p>There are several ways to set the viewport of an SVG:</p>

```
<!-- Use the default (this will either be 350 x 150 or the full width of the container depending on the browser and render method) -->
<svg />

<!-- Set the width and height attributes -->
<svg width="200px" height="200px" />

<!-- Set CSS styles (will override the width and height attributes) -->
<svg style="width: 200px; height: 200px;" />
```

<h3>How SVG View Box works</h3>
<p>If the SVG viewport defines the size in pixels of that proverbial "window" / "portal" / "lens" that we are viewing the SVG from, then what does viewBox do?</p>

<p>It sounds awfully similar to "viewport" doesn't it? There are hundreds of tutorials about this stuff online and many of them try to visualize this concept. I personally find these explanations confusing and misdirected in some cases because what viewBox really does is two things, and both are mathematical by nature:</p>

<ol>
  <li>It mathematically links or "maps" the viewport pixels to internal "units"</li>
  <li>It defines the aspect ratio of the SVG</li>
</ol>
<p>The viewBox attribute accepts 4 parameters, defined as viewBox=&quot;min-x min-y width height&quot;:</p>
<ol>
  <li>min-x - the X origin coordinate of the view box</li>
  <li>min-y - the Y origin coordinate of the view box</li>
  <li>width - the width of the SVG (internal units)</li>
  <li>height - the height of the SVG (internal units)</li>
</ol>

<p>So what are these "internal units"? You might see them defined as "user space units" in official documentation, but I don't find this concept very intuitive. Let's look at the mathematical relationship between SVG viewport and SVG viewBox with a simple example:</p>

```
<svg width="200px" height="100px" viewBox="0 0 200 100" />
```

<p>This is what I call the "base case" because both the absolute size and the aspect ratio are the same between the viewport and the view box.</p>
<ul>
  <li>Viewport aspect ratio is 200px / 100px = 2:1</li>
  <li>viewBox aspect ratio is 200 units / 100 units = 2:1</li>
</ul>
<p>Let's start with min-x and min-y because these are relatively easy. If you created an X/Y coordinate system on your viewport, the min-x and min-y values say, "the SVG internal coordinate system will start at these values relative to the browser (viewport) coordinate system."</p>

<p>The more challenging values are width and height because they are a mapping of values from "browser space" (viewport width and height) to "user space" (internal SVG width and height). Remember, SVGs are "graphics made by math", so in order to infinitely scale their size, we need to have a "mapping" from non-infinitely-scalable browser pixels to infinitely scalable SVG "units".</p>

<p>To map pixels to "units", we simply use the formula:</p>
<blockquote>
viewport dimension / viewBox dimension = internal px / unit
</blockquote>

<p>In this case, the viewport width is 200px divided by the viewBox width of 200units = 1px / unit. So we can say that for every viewport pixel, there will be one internal SVG pixel, and therefore, our "zoom" level is 100%.</p>

<p>So what is this "base case" example telling us? Let's look at a simple ellipse SVG and walk through how each viewBox property is affecting the final result.</p>

View Box Example - Rectangle
Take a look at the following SVG:

```
<svg
  xmlns="http://www.w3.org/2000/svg"
  width="200px"
  height="100px"
  viewBox="0 0 200 100"
  style="border: 1px solid #64748b;"
>
  <rect x="50" y="25" width="100" height="50" style="fill: #cbd5e1" />
</svg>
```

Here's what that looks like:

Breaking this down:

Viewport dimensions are 200px x 100px (what we see in the browser, represented with the dark blue border)
1 width "unit" = 200px / 200 = 1px (viewport width / viewBox width)
1 height "unit" = 100px / 100 = 1px (viewport height / viewBox height)
Ellipse dimensions are 100 units x 50 units, or translated, 100px x 50px (designated by the radius attributes rx and ry)
Using our knowledge of viewBox, how could we make the rectangle occupy the full viewport (200px x 100px) without changing the internal height or width units?

First off, here's what we don't want to do:

I do not want to change the size of the viewport, so we won't change the width or height of the svg element
I should not alter the internal SVG (ellipse) dimensions because this is impractical with most SVG elements as they are far more complex than this one, and changing the internal values for each SVG element would be impossible on this large of a scale!
To get my intended result, there are two steps.

Step 1: Find the correct rectangle size

My target rectangle dimensions are 200px x 100px, so all I need to do is re-map the ratio between viewport dimensions and viewBox unit values! It is important to keep the same aspect ratio (2:1).

What is my new internal unit mapping?
width: 200px / 100units = 2px / unit (i.e. each internal unit is now 2 pixels rather than our previous 1 pixel)
height: 100px / 50units = 2px / unit
What are the new internal pixel values of the rectangle?
width: 100units * 2px/unit = 200px
height: 50units * 2px/unit = 100px
Step 2: Find the new X/Y coordinates

The rectangle is mapped 50 units offset from the left edge of the viewport, and 25 units offset from the top edge of the viewport. With our new mapped values, this means...

Left offset = 50units * 2px/unit = 100px
Top offset = 25units * 2px/unit = 50px
If we don't make any changes to the min-x and min-y values and only update the viewBox width and height values, here's what we get:

Remember, the SVG rect element is still there and correctly sized, you just can't see all of it because the viewport isn't oriented correctly.

What we want is for the rectangle's top left edge not to be offset by 100px and 50px, but by 0 pixels! Intuitively, you'd think that setting viewBox=&quot;0 0 100 50&quot; would achieve this, but that would only work if our rect element had values of x=0 y=0. To make our rectangle start at the 0,0 origin point of the viewport, we need to reverse the offsets that exist on rect (x=50 y=25) by adding these to our view box. So in summary, we end up with viewBox=&quot;50 25 100 50&quot;.

Here is the final code and result:

```
<svg
  xmlns="http://www.w3.org/2000/svg"
  width="200px"
  height="100px"
  viewBox="50 25 100 50"
  style="border: 1px solid #64748b;"
>
  <rect x="50" y="25" width="100" height="50" style="fill: #cbd5e1" />
</svg>
```

To bring this full circle, let's take another look at our SVG from earlier. This time, I've added slider controls so you can adjust the viewBox attribute on the SVG. Not only can we resize the SVG viewport (size in browser pixels), but now, we can also resize the SVG View Box.

Remember:

If you want the "portal" from which you're viewing the SVG to be larger/smaller in the web browser, adjust the viewport (drag the bottom right corner)
If you want the SVG to appear larger / smaller, adjust the viewBox width and height values
If you want the SVG position within the viewport to change, adjust the min-x and min-y values of viewBox
That's it! Have fun playing around with the demo and I'll see you in the next section.

Maintaining SVG aspect ratios
Building off the section above, let's look at a trickier example:

```
<svg
  viewBox="0 0 100 200"
  width="100"
  height="100"
  xmlns="http://www.w3.org/2000/svg"
>
  <circle r="50" cx="50" cy="50" />
</svg>
```

Notice anything interesting here?

Viewport width = 100px
Viewport height = 100px
View Box = 0 0 100 200
The viewBox dimensions (100px x 200units = 1:2) have a different aspect ratio than the viewport (what user sees in browser) dimensions (100px x 100units = 1:1). That means...

One internal SVG width "unit" equals 100px / 100units = 1px/unit
One internal SVG height "unit" equals 100px / 200units = 0.5px/unit
In other words, the height is going to appear smaller because we're allocating fewer pixels per unit to it. In the demo below, you can see this effect happening in the first SVG. What you'll also notice is that I've added an attribute called preserveAspectRatio which is specifically designed for scenarios like this where the viewBox aspect ratio is different than the viewport aspect ratio. I've pre-selected the none which shows what happens when aspect ratio is not preserved, but the browser default is the xMidYMid value, which you can select in the dropdown and see how the browser "intelligently" keeps this SVG looking good.

https://codepen.io/zg_dev/pen/GRdqdRK

What are the most common SVG elements?
The list below is a hand picked list of SVG elements that I think every developer should at least be familiar with. We'll walk through each with a few examples.

Styles - fill, stroke
Shapes - path, <circle>, <line>, <rect>
Structural - <svg>, <g>, <defs>, <use>
Text - <text>, <title>
Gradients - <linearGradient>, <stop>
Animations (basic) - <animate>
How to style SVG elements with presentational attributes
SVG elements can be styled very similarly to HTML elements. You can select them with CSS selectors and apply many of the built-in CSS styling properties.

The two properties that differ that you'll want to remember are fill and stroke. Typically, with HTML, you would use something like this to style a div element:

```
<div
  style="width: 200px; height: 20px; background-color: red; border: 2px solid black;"
/>
```

Which gives you this:

But if we try that same thing on an SVG rect, it doesn't work so well:

```
<svg width="200px" height="20px" xmlns="http://www.w3.org/2000/svg">
  <rect
    style="width: 200px; height: 20px; background-color: red; border: 2px solid black;"
  />
</svg>
```

That is because with SVG elements, we control the styling via presentational attributes, which can also be used in pure CSS. There are many presentational attributes to choose from, but the two that you'll want to focus on are stroke and fill. Let's try that example again:

<svg width="200px" height="20px" xmlns

```
<svg width="200px" height="20px" xmlns="http://www.w3.org/2000/svg">
  <rect
    style="width: 200px; height: 20px; stroke-width: 2; stroke: black; fill: red;"
  />
</svg>
```

Using the Style Tag
One handy way to define styles within an SVG is using a <style> tag, which is an SVG element that behaves similarly to an HTML style tag. We can convert the example above to a much cleaner version like this:

```
<svg width="200px" height="20px" xmlns="http://www.w3.org/2000/svg">
  <style>
    .rectangle {
      width: 200px;
      height: 20px;
      stroke-width: 2;
      stroke: black;
      fill: red;
    }
  </style>

  <rect class="rectangle" />
</svg>
```

SVG Primitives
Shape elements are the "primitives" of SVG. For all shape possibilities, see the shape reference and the graphic reference.

What is an SVG Path?
While you will generally not use path by hand (that's what Adobe Illustrator / Inkscape / Figma, etc. are for), it is important to understand what it does.

You can think of a path as a "group of lines". They can be connected, but don't have to be. A path has a very simple attribute API that accepts a single attribute, d. The d attribute is a complex attribute that accepts an arbitrarily-sized list of "commands" that will "draw" the path to the screen. You can see the full list of commands here, but just to give you a taste, let's walk through a few examples.

https://codepen.io/zg_dev/pen/NWMbyYX

```
<!-- Example 1 -->

<path d="M10,10 L10,50 H80 V20 Z" style="fill: none; stroke: red;" />
```

This can be interpreted as:

M10,10 - Move to coordinates 10,10, relative to the 0,0 origin at the top left corner of the SVG viewport
L10,50 - Draw a line from the starting point of 10,10 to an ending point of 10,50
H80 - Draw a horizontal line from the prior ending point of 10,50 to an X coordinate of 80
V20 - Draw a vertical line from the prior ending point of 80,50 to a Y coordinate of 20
Z - close the path from the prior ending coordinate of 80,20 to the start coordinate of 10,10

```
<!-- Example 2 -->

<path d="M0,0 L50,50 M80,20 L40,10" style="fill: none; stroke: red;" />
```

This can be interpreted as:

M0,0 - start at the origin point of the SVG viewport
L50,50 - Draw a line from the starting point of 0,0 to an ending point of 50,50
M80,20 - without drawing anything, move back to a coordinate of 80,20
L40,10 - From the prior end point of 80,20, draw a line to 40,10
SVG Path vs Line
A line is an implementation of a path. The following two SVG elements will create the same result:

```
<!-- This path... -->
<path d="M0,80 L100,20" />

<!-- is equivalent to this line -->
<line x1="0" y1="80" x2="100" y2="20" />
```

SVG Path vs Rect
Likewise, you can also replicate a rect with a path.

```
<!-- This path... -->
<path d="M0,0 L100,0 L100,100 L0,100 Z" />

<!-- is equivalent to this rectangle-->
<rect width="100" height="100" />
```

SVG Path vs. Circle
While I could keep replicating all these built-in shapes with a basic path element, you'll start to see with more complex shapes like a circle that this quickly becomes tedious:

```
<!-- This path... -->
<path d="M 0,25 a 25,25 0 1,1 50,0 a 25,25 0 1,1 -50,0" />

<!-- is equivalent to this circle -->
<circle r="25" cx="25" cy="25" />
```

The point here is that all shape elements are just implementations of the primitive path element.

How to use SVG Structural Elements
You can think of these structural elements as "organizational tools" for your SVGs. I won't dive too deep here, but let's walk through one example that incorporates all the concepts you need to know in one

```
<svg width="200" height="200" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <g id="custom-elements" fill="skyblue" stroke-width="2" stroke="navy">
      <circle r="50" cx="50" cy="50" />
      <rect width="20" height="20" x="100" y="160" />
    </g>

    <g id="more-elements">
      <!-- A cleaner way to style a group -->
      <style>
        .red-circle {
          fill: red;
          stroke: black;
          stroke-width: 4;
        }
      </style>

      <circle r="10" cx="150" cy="100" class="red-circle" />
    </g>
  </defs>

  <!-- Without these, nothing is rendered to the browser -->
  <use href="#custom-elements" />
  <use href="#more-elements" />
</svg>
```

A couple of important notes with this example:

You can see we are defining via defs a "group" (g) of SVG elements, which does not render the elements to the browser. Alone, this SVG would not show anything. defs is purely an organizational tool that makes your SVGs more readable and accessible.
We then take the id=custom-elements and id=more-elements set on the g and <use href=&quot;#custom-elements&quot; /> them, which is the code that actually renders the SVG elements to the browser.
Notice how we've set several presentational attributes such as fill=&quot;skyblue&quot; to the entire g group, which then applies to all child elements of the group. This is an efficient way to style or animate multiple SVG elements at once and is one of the main appeals to using g to group SVG elements. You can also use <style> for cleaner stylings as I have done in the second g group.
Using Text With SVGs
Using text is fairly straightforward with SVG. The one thing to look out for is the font styles as they are adjusted using CSS and do not have many sizing controls.

In the example below, I've included title and desc elements which are used mainly for accessibility concerns (hover over the text, you'll see the title appear in a browser tooltip).

https://codepen.io/zg_dev/pen/mdLOLpZ


How to apply gradients to SVGs
With SVG, you have fine-grained control when applying gradients to your elements. Using what we've learned from prior sections, here is an example of a real-world gradient use case.

```
<linearGradient id="myGradient" gradientTransform="rotate(90)">
  <stop offset="0%" stop-color="#4d7c0f" />
  <stop offset="50%" stop-color="#d9f99d" />
  <stop offset="100%" stop-color="#4d7c0f" />
</linearGradient>
```

With this gradient, we're using the stop element to define a gradient that starts at the top/bottom using the border color and linearly fades to a lighter hue variant as we reach the middle of the text.

Kind of reminds you of the early 2000s Microsoft PPT text right? I'm no designer, but you can see the possibilities with this simple example.

https://codepen.io/zg_dev/pen/dyeOKvy

How to animate an SVG
The most basic form of SVG animations is with the animate property. You can generally put this inside another SVG element and use the animateProperty + values to animate a certain property of that SVG. For example, we can animate the radius of a circle:

``
<svg width="400" height="200" xmlns="http://www.w3.org/2000/svg">
  <circle r="50" cx="50" cy="50"/>
    <!-- Animating the radius of the circle back and forth between 50 and 60 units indefinitely -->
    <animate attributeName="r" values="50;60;50" dur="5s" repeatCount="indefinite" />
  </circle>
</svg>
```

Not all attributes are eligible for animations, so be sure to check the attribute reference on MDN, which usually says something like Animatable: yes | no.

But that's just a basic animation. You can do so much more that I won't get into. As a quick example of what's possible, here's how you'd create an SVG path and animate another SVG element on that path:

https://codepen.io/zg_dev/pen/YzLpvvW

Common SVG Questions
What is the difference between Canvas and SVG Specs?
Take a look at the two rectangles below. Pretty similar looking right?

The first image is created with a <canvas /> element while the second is created with an <svg /> element. Check the dev tools!

Here is the code for each. First up, canvas (a basic React component):

```
// Canvas component

import { useEffect, useRef } from 'react';

export function CanvasExample() {
  const ref = useRef<HTMLCanvasElement | null>(null);

  useEffect(() => {
    const ctx = ref.current?.getContext('2d');

    ctx!.lineWidth = 2;
    ctx?.beginPath();
    ctx?.rect(0, 0, 300, 80);
    ctx?.stroke();
  }, []);

  return (
    <div className="flex justify-center w-full">
      <canvas width="300" height="80" ref={ref} />
    </div>
  );
}
```

And here is the svg:

```
<!-- SVG -->

<svg viewBox="0 0 300 80" xmlns="http://www.w3.org/2000/svg">
  <rect width="300" height="80" style={{ fill: 'transparent', strokeWidth: 2,
  stroke: 'rgb(0,0,0)' }} />
</svg>
```

When do I use SVG and when do I use Canvas?
As you can see from the examples above, the svg spec is very declarative (i.e. "hey browser, here is what I want, build it for me") while canvas is very imperative (i.e. "hey browser, here is a list of steps that I want you to take to build this").

In general, you can do way more with the canvas spec at the cost of accessibility and performance. So to answer this question more briefly than I should, you should generally use SVG as a default and fallback to using the canvas spec when SVG no longer can support the complexity of what you are doing (e.g. a console game).

Is SVG the same as HTML?
No, SVGs are created with XML, which is similar looking to HTML, but without pre-defined tags such as h1, a, or p tags, and the syntax is case-sensitive (unlike HTML). The user creates arbitrarily named tags and uses this to represent structured data such as SVGs.

In other words, an SVG is an implementation, or dialect of XML that defines arbitrary shapes that have been covered in this post (e.g. rect, g, circle, etc.). width="320" height="150" -->

