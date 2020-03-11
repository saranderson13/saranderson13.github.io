---
layout: post
title:      "How to Mimic Masonry with CSS Grid + Vanilla JS"
date:       2020-03-10 14:19:51 -0400
permalink:  how_to_mimic_masonry_with_css_grid_vanilla_js
---


We all know masonry. It's gorgeous, functional and a staple of tons of modern layouts. But, it is a library, and frequently (and most commonly) used in conjunction with JQuery. I personally am a fan of not getting more code heavy than necessary, so if I don't really need a library, I don't want to add one. Don't get me wrong - masonry can do loads of stuff that the technique I'm about to explain can't, or at the very least if you need any kind of functionality past just the appearance having things fall into place in the seemingly magical way masonry gets them to, it isn't worth reinventing the wheel. But the bottom line is I did find a way to get that sweet sweet masonry look with just CSS and vanilla javascript.

I figured this out while making a react application, so in a future post I'm going to go over the differences in implementation with React. There aren't many. If anything you can consider this tutorial a how-to on setting up with static data, and the next post to be setting up with dynamic data. Everything that is needed to imitate the masonry look is vanilla, and I'm going over a simple example that I whipped up in JS Fiddle which deals with static data. ([You can see the Fiddle example here and play around with it.](https://jsfiddle.net/h1dmqtjp/4/))

But once you have your data, it's as simple as calculating the height of the content inside of your box, and dynamically setting the number of rows the box should span based on the height it needs to clear. To do this you need to structure your HTML so that you have your content element nested inside of a container element. For example: 

```
<div class="grid>

     <div class="box">
          <div class="boxContent">
               Box 1
          </div>
     </div>

     (...several other box divs)
		 
</div>
```

With no dynamic styling, because they're in a grid, the boxes in each row appear to all be the same height. But the 'boxContent' divs have only the height neccessary to accommodate their content. 


![](https://imgur.com/UmCuMpT.png) 

![](https://imgur.com/UKpA8vH.png)


Now the task is to dynamically resize them. To do this in one fell swoop, you want to create a javascript function which grabs all of your box objects, iterates over them, calculates the size of their content, and then assigns them the correct number of rows to span.

```
function calculateBoxSize() {

     // Grab all of your boxes
     allBoxes = document.getElementsByClassName('box')
		 
     // Iterate over each of them. 
     // Turn them into an array first because they'll be in an HTMLCollection initially
     Array.from(allBoxes).forEach( box => {
		 
          // Get the height of your inner content
          // If you have more than one inner element you will need to add their heights together.
          const heightOfContent = box.querySelector('.boxContent').offsetHeight
					
          // Calculate how many rows the box should occupy.
          // Take the height of your content, and divide it by the desired height of your row ***
          const rowSpan = Math.ceil( heightOfContent / 20 )
					
          // Assign that row height to your box as a style attribute.
          box.style.gridRowEnd = "span " + rowSpan
     })
}
```

*** A bit more on that comment. Below this is all of the css you need. 

```
// The 'grid-template-columns' attribute below makes your columns adjust to fill up the available space
// I included a link to good explanation of navigating column width with CSS grid.

.grid {
    display: grid;
    grid-gap: 3px; // just for styling, not essential
    grid-template-columns: repeat( auto-fill, minmax(200px, 1fr) );
}

.box {
    width: auto;
    background-color: #b4edd6; // just for styling, not essential
}
```

Note that you NEVER need to set anything related to height or row height. I'm not even styling the 'boxContent' class at all! You don't need to set it for the 'boxContent' because the height defaults to 'auto', which will mean the element is only the height needed to accommodate its content. And you don't need to set it for 'box' because the height will default to whatever the height is determined to be from its row. When you see this line,

```const rowSpan = Math.ceil( heightOfContent / 20 )```

That "20" is actually where your row height is getting set. And it isn't really even getting set. You just get as many rows as you need, and the number of rows you need will depend on how many your boxes take up. So say I have a box that needs to accommodate 100px worth of content. Dividing that by 20 will mean that box should take up 5 rows. A box that is 120px would need 6 rows. The smaller the number you divide by, the more rows you will get, and the more varation in box size you can have. I used 20 and that seemed to work out well because it was similar to the height of a line of text. The Math.ceil() is there just to round it up so you don't inadvertently assign a non-integer to your rowspan, and you'd want to round up rather than down so that you don't cut any content off. Going off of our examples above, a box that is 105px would also need 6 rows.

And now, for those of you who didn't go mess around with the JS Fiddle, the final reveal:

![](https://imgur.com/ZflvL1C.png)

And that's it! Next time I'll be writing about a slightly more complex implementation, with a React example and dynamically loaded content. Oh, and here's that link I referenced above about the fr/column width explanation:

[An Introduction to the `fr` CSS unit](https://css-tricks.com/introduction-fr-css-unit/)




