---
layout: post
title:      "Using CSS to Format Images"
date:       2020-09-03 20:21:22 +0000
permalink:  using_css_to_format_images
---

Today I'm going to talk about a few simple ways that images can be formatted using CSS. I've created a [JS Fiddle](https://jsfiddle.net/saranderson13/6xkc2b70/), which I recommend using to follow along.

Sometimes, an image isn't perfect. It's the wrong size, or needs to be cropped, or maybe both. Or maybe one or both of those things need to be responsive to device and screen sizes. These days, it is rarely practical to have one image file that will be perfect - as is - for all possible design scenarios, and neither is it practical to make innumerable separate versions. So being able to format and modify images with CSS becomes imperative.

I'm not going into formatting for different media sizes today - just the very basics of getting an image to cooperate, with resizing, cropping, and centering. The basic format will be to nest an image tag within a container. 

Let's start out with our plain, unmodified image, and its CSS. Note that I *have* added some basic formatting to the container that holds all of the example images, just to make the layout uniform without having repetitive code within the individual container classes. By default, all `div` containers have some margin to separate them from their neighbors, as well as a border.

```
/* CSS */
.plainImageContainer {
  /* This is here just to give a background to the transparent gif image. */
  background-color: #deeaff;
}

- - - - - -

<!-- HTML -->
<!-- Unmodified Image -->
<div class="plainImageContainer">
  <img src="https://i.ibb.co/svwq4Lm/Dual-Ball-2-6s-225px.gif">
</div>
```

**In the JS Fiddle, this is the first, big image with the lavender background.** Somewhat expectedly, nothing is styled here at all. The container will assume the dimensions of the image.

The case is nearly the same for the second example, and the third: image resizing. The container requires no styling at all. By setting the height and/or width attributes specifically for your `img` tag, it will be resized. In fact, to keep the image as the same proportions, only *one* of those attributes needs to be styled. Setting the height and width separately can distort the image if they are set to dimensions that differ from the original image's aspect ratio. As with any non-vector image modificiation, drastic resizing or stretching may degrade the image and make it look pixelated.

```
/* CSS */
/* Image is resized */
.resizedImageContainer {
  background-color: #deeaff;
}

.resizedImageContainer img {
  width: 100px;
}


/* Image is resized but forced out of original aspect ratio */
.resizedDistortedImageContainer {
  background-color: #e6fcff
}

.resizedDistortedImageContainer img {
  width: 150px;
  height: 100px;
}

- - - - - -

<!-- HTML -->
<!-- Image is resized -->
<div class="resizedImageContainer">
  <img src="https://i.ibb.co/svwq4Lm/Dual-Ball-2-6s-225px.gif">
</div>

<!-- Image is resized with distorted dimensions -->
<div class="resizedDistortedImageContainer">
  <img src="https://i.ibb.co/svwq4Lm/Dual-Ball-2-6s-225px.gif">
</div>
```

**But what if there's a lot of extra stuff that needs to be cut out of the image?** Up until now, the container has seemed pretty useless. But it's essential for cropping. By setting the height and width attributes of a container, and hiding any part of an image that falls outside of it, cropping can be acheived.

```
/* CSS */
/* Image is cropped, but not resized or centered. */
.croppedImageContainer {
  width: 100px;
  height: 100px;
  overflow: hidden;
  background-color: #e6fff3;
}

- - - - - -

<!-- HTML -->
<!-- Image is cropped, but not resized or centered. -->
<div class="croppedImageContainer">
  <img src="https://i.ibb.co/svwq4Lm/Dual-Ball-2-6s-225px.gif">
</div>

```

**Admittedly, this is pretty useless unless the top-left corner of the image is the important part.** Sadly that is not always the case. It would be helpful if the image could be shifted around in that container... wouldn't it?

Yes. Yes it would. And it is possible. This is where both the container and the image tag start to work together. Using margins, in this case negative margins, the image can be moved around, relative to the container. It will always begin with the top-left corner of the image in the top-left corner of the container. By setting its left margin negative, it appears to shift the image to the left. By setting the top margin negative, it will appear to move the image up. And in this way you can shift it around until the part of your image that you want to focus on is in view.

```
/* CSS */
/* Image is cropped and centered, but not resized */
.croppedAndCenteredImageContainer {
  width: 100px;
  height: 100px;
  overflow: hidden;
  background-color: #fff6de;
}

.croppedAndCenteredImageContainer img {
  /* 63px is half of the image's height and width */
  margin: -63px 0px 0px -63px;
}

- - - - - -

<!-- HTML -->
<!-- Image is cropped and centered, but not resized -->
<div class="croppedAndCenteredImageContainer">
  <img src="https://i.ibb.co/svwq4Lm/Dual-Ball-2-6s-225px.gif">
</div>
```

**Awesome, but still not perfect.** Let's combine these things. We need our image to be cropped down, resized, AND re-focused. 

```
/* CSS */
/* Image looks like it's bouncing off the right side, */
/* and skimming the bottom of the container. */
.fancyContainer {
  width: 150px;
  height: 100px;
  overflow: hidden;
  background-color: #ffe5de;
}

.fancyContainer img {
  width: 162px;
  margin-top: -16px;
}

- - - - - -

<!-- HTML -->
<!-- Image looks like it's bouncing off the right side, -->
<!-- and skimming the bottom of the container. -->
<div class="fancyContainer">
  <img src="https://i.ibb.co/svwq4Lm/Dual-Ball-2-6s-225px.gif">
</div>
```

**And voila!** It will take some experimenting to get the positioning exactly right. But by wrapping the image in a container and setting just those few attributes, it's possible to tweak the appearance of images. To quickly review *where* all of those separate modifications are happening,
- **Cropping** Set the `height` and `width` of the container AND set the `overflow` to `hidden`.
- **Resizing** Set the `height` and/or `width` of the image. In this case I have set only the `width`, which keeps the image's original proportions.
- **Centering / Focussing** Set the `margin` of the image. Typically it will be necessary to set a negative margin for the top and/or left, because that will move the image up or to the left.


