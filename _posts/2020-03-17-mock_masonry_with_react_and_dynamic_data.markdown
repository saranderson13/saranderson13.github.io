---
layout: post
title:      "Mock Masonry with React & Dynamic Data"
date:       2020-03-17 04:15:19 +0000
permalink:  mock_masonry_with_react_and_dynamic_data
---


In a previous post I talked about how to mimic masonry with with static data via the click of a button, and [you may want to take a peak at that](https://saranderson13.github.io/how_to_mimic_masonry_with_css_grid_vanilla_js) before jumping into this post. I'm not going to go as much into detail here about what you need to do, but rather *when* you need to do it. In that tutorial, I went over an example I whipped up in JS Fiddle so people can play around with the code. Here I'll just be going over code snippets from an application I'm working on, which uses React. 

There are two key things to address when trying to modify anything related to dynamic data. 

1. That data needs to actually be there, as in loaded. 
2. And then somewhat related to that, if your data changes, or your view changes and the design would need to change based on a view change (such as a window resize), you need to be able to handle it at that time too. 

With React's lifecycle methods, this becomes easy to implement. 

Before moving on, here is a brief overview of the section of my application I'll be talking about. The page is essentially a bunch of lists. Users can make regular bulleted lists, checklists, and then edit those. Depending on the number of things on a list, they will be different heights. This is the end result we're going for:

![](https://i.ibb.co/C6vkcCX/Aesthetic-Lists.png)

Breaking down the component structure, I have a **ListsBodyContainer** which holds all of the **ListBox**es. Each **ListBox** contains a List Title (just a div, not component), and many **ListItemLine**s. Here is an example of what the HTML would look like, compiled, not from the various components in JSX

```
// THE SECTION IS EQUIVALENT TO THE ListsBodyContainer COMPONENT
<section id="bodyContainer" class="grid">

    // THIS DIV IS EQUIVALENT TO THE ListBox COMPONENT
    <div class="listBox">
		
        // THIS DIV IS NOT A COMPONENT
        <div class="listTitle">
            LIST TITLE
        </div>
				
        // THIS UL IS NOT A COMPONENT
        <ul className="listItems">
				    
            // EACH OF THESE LIST ITEMS IS A ListItemLine COMPONENT
            <li className="listItem">Example Item</li>
            <!-- More listItems etc -->
        </ul>
    </div>
</section>
```

And similar to the CSS from the static example, not much is needed:


```
#bodyContainer {
    /* COMPLETELY FOR STYLING PURPOSES */
    /* NONE OF THIS IS REQUIRED FOR MOCK MASONRY */
    box-sizing: inherit;
    display: block;
    position: absolute; top: 0px; left: 300px;
    width: calc(100% - 300px); height: inherit;
    padding: 20px;
    background-color: #FFF;
    overflow: auto;
}

/* ***THIS IS THE IMPORTANT BIT!*** */
#bodyContainer.grid {
    display: grid;
    grid-gap: 10px;
    grid-template-columns: repeat( auto-fill, minmax(250px, 1fr) );
}
    
.listBox {
    /* COMPLETELY FOR STYLING PURPOSES */
    /* NONE OF THIS IS REQUIRED FOR MOCK MASONRY */
    box-sizing: border-box;
    width: auto;
    display: block;
    padding: 10px;
    background-color: #FFF;
    border: 3px double rgb(233, 187, 187);
    box-shadow: 4px 4px 4px rgb(216, 216, 216);
}

/* THESE ARE INCLUDED FOR REFERENCE, BUT THEY ARE COMPLETELY FOR STYLING PURPOSES */
.listTitle { font-size: 20px; }
.listItems { padding-left: 20px; }
.listItem { font-size: 12px; }
```

**Note Above:** In the #bodyContainer, you see "display: block". Then in the #bodyContainer.grid, you see "display: grid". In my application, the #bodyContainer id was used on multiple pages, for different body components, all of which except for the Lists page needed it to be block display. The #bodyContainer.grid is a more specific selector, so it overrides the regular #bodyContainer styling.

Okay, now that all that is established and you have a better idea of the content I'm working with, lets jump into the React Lifecycle methods that make the mock masonry possible in my dynamic application. Below is all of the applicable code, and then I'll go through it in more detail. This is all in the ListsBodyContainer component. No additional code is needed in the nested components.

```
componentDidMount() {
    window.addEventListener('resize', this.resizeAllListBoxes);
}

componentDidUpdate() {
    this.resizeAllListBoxes()
}

resizeListBox = box => {
    const contentHeight = 40 + box.children[0].offsetHeight + box.children[1].offsetHeight
    const rowSpan = Math.ceil( contentHeight / 10 )
    box.style.gridRowEnd = "span " + rowSpan;
}

resizeAllListBoxes = () => {
    const allBoxes = Array.from(document.getElementsByClassName("listBox"));
    for( let x = 0; x < allBoxes.length; x++ ) {
        this.resizeListBox(allBoxes[x]);
    }
}

componentWillUnmount() {
    window.removeEventListener('resize', this.resizeAllListBoxes)
}
```

Lets start with the functions that do the actual resizing: #resizeListBox, and **#resizeAllListBoxes**. Intuitively, **#resizeAllListBoxes** collects all of the ListBox elements, and iterates over them, calling the **#resizeListBox** function for each. This function is identical to the function that resizes the boxes from my last tutorial, with one exception. There are multiple elements inside of a ListBox, so the height of both must be accounted for. In my case there are a static number of elements: the list title, and the actual list, so I opted to explicitly reference each element, as in box.children[0], and box.children[1]. If you had many elements, or an inconsistent number of elements, you would want to set up a loop of some kind to calculate the contentHeight. The 40 also being added in there is to account for padding. The end result is that a gridRowEnd style attribute is added to each ListBox component. I go into greater detail on how this function works in the static tutorial so I'm not going to go any deeper into it here.

Now on to the three lifecycle methods being utilized, because this is really where it all comes together. Remember now that all of these lifecycle methods are inside the ListsBodyContainer, which holds all of the lists. As soon as that component is mounted, (and **#componentDidMount()** is called...), I want to set an event listener to take care of re-styling when the window is resized. (This takes care of key thing #2 above.) I do NOT at this point want to do any actual sizing because at this point, my data is being fetched, and is not available. Technically I could call the **#resizeAllListBoxes** method here and it wouldn't break anything. The first line of that method just collects all the ListBoxes, so if it found none it would have nothing to iterate over. But there is no point in attempting to call it at this time because the data is never going to be there.

I don't want to attempt to resize any of my box elements until the component *receives* the updated props, from it's parent component, and is re-rendered. This is where **#componentDidUpdate()** comes into play. By calling the **#resizeAllListBoxes** function any time the component's data is updated, this accounts for key thing #1 from above in several different scenarios. The first time the page is loaded, it hits this method twice - the first time, just after the component mounts, but no data is available, and the second time once the fetch has been completed and the data is there. So as soon as the data gets there for the first time, before it is rendered, the row span is added to each list box. And then any other time the data is updated, such as if a list is modified, or a new list is added, or a list is deleted, it will hit the #componentDidUpdate() method *again* and resize the boxes accordingly.

At this point you have covered all of your bases as far as making sure the boxes are able to resize whenever their data or available space changes! And there is just one more thing to do. Because you set an event listener for the entire window, you need to make sure you remove it before navigating to another page. This is that last bit in the **#componentWillUnmount()** method. If you skip this step, your event listener will stay on the window and may have unintended consequences elswhere in your application.

And that should be it! With a relatively small amount of code, you've mimicked that gorgeous masonry look without needing the library. As I said in my previous post, if you need to do anything more complicated, there's no point in reinventing the wheel. But for simple, one-time application, this is a code-light way to achieve the same look.
