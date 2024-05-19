+++
title = 'Improve UI/UX under Hugo'
date = 2024-05-19T21:49:51+08:00
draft = false
tags = ['hugo', 'web', 'UI', 'UX']
+++

In the last blog post, I shared how I set up my personal site with Hugo and
Vercel. In this blog post, I will share how I improve the UI/UX (user
interface/experience) of my site.

<!--more-->

The changes includes:

- [Changing the colour for each element](#change-the-colour-for-each-element);
- [Supporting dark mode](#support-dark-mode);
- [Collapsing the navigation bar on some pages for mobile devices](#collapse-the-navigation-bar-on-some-pages-for-mobile-devices);
- [Some minor changes](#some-minor-changes).

## Change the colour for each Element

My approach of making my own colour theme includes the following steps:

1. Pick a main colour, which establishes the overall look and feel of the site;
2. Set all other colours based on the main colour, e.g., the link colour, the
   text colour, the header colour, etc.;
3. View your pages as much as possible to see if the colours are consistent
   and harmonious (if not, make some minor adjust).

The first step is really crucial. It cannot be too saturated, nor too dark.
Also, it must reflect the purpose and your style of the site. Pick a colour
that you like, and that you think is suitable for your site.

Then, you can choose the colour of all other elements based on the main colour.
The core idea is to company the main colour with other colours that are
less highlighted. For example, the share button should not gain too much
attention, so in my case, I choose a light gray colour for it. Also, bear
in mind that the colour should serve its purpose. For example, the link colour
should be different from the text colour (or with an underline) to distinguish
it from the normal text.

## Support Dark Mode

Nowadays, a great scale of users prefer dark mode. It is important to support
dark mode for your site. If your site supports dark mode, users will feel
more comfortable and appreciate more, as opposed to a really bright screen.

Supporting the dark mode is by no means difficult. You can change the colour
of each element step by step, so it's easy to get start.

To set the colour under the dark mode, you should add some css code like this
(`prefers-color-scheme` indicates the css rule in this block will only be
applied in dark mode):

```css
@media (prefers-color-scheme: dark) {
  /* Change whatever element you want as you do for normal css */
}
```

However, although it is easy to support dark mode, there's still some
principles you should follow:

1. Under dark mode, the brightness of the colour should be lower than that
   under light mode;
2. The background colour should not be pure black;
3. The colour should be consistent with the light mode, so that your site
   has a unified style and better recognition;

The first rule is really important. Under the dark mode, the contrast
will be amplified if you keep the brightness difference between the
background and the text. If you keep the brightness difference, users
will feel uncomfortable.

For the second rule, a pure black background will further amplify the
contrast, which is not comfortable for the eyes. Hence, a lot of sites
use a dark gray colour as the background under dark mode.

For the third rule, a consistent style can yield a better recognition.
If the colour under dark mode is totally different from that under light
mode, users may feel confused. Also, we should not change the layout
under dark mode, as it may confuse users.

## Collapse the Navigation Bar on Some Pages for Mobile Devices

The base theme I used does not support a collapsible navigation bar. For
some pages, say blog posts, the navigation bar will occupy a lot of space,
but the information on the navigation bar is not that important. So I
need to add the collapsible feature to the navigation bar for narrow screens.

I split the work into three parts:

- Implement the static collapsed navigation bar;
- Add JavaScript code for toggling the navigation bar;
- Add the css for the animation.

### Implement the Static Collapsed Navigation Bar

First, I think over the layout of the navigation bar and the contents on
both the collapsed and expanded navigation bar. The collapsible navigation
bar is designed for the pages where the information on the navigation bar
does not really matter or takes up too much space. So I decide to show the
collapsed navigation bar for blog posts while should the full bar on index
page.

For the collapsible navigation bar, even the expanded content should still
remove some unnecessary information. For me, I remove the avatar (although
I do not have an avatar yet, but I'm preparing for the future).

After deciding the content, I implement the visible part. I add a downward
triangle to the right of the navigation bar on the top of the navigation bar.
When the user clicks on the triangle, the navigation bar would expand.
Then I change the top padding height.

On top of that, we need to collapse the content below the navigation bar.
For the contents below the navigation bar, I add a `div` with the id
`sidebar-container`. The follow code is to set the height of the `div` to `0px`
and show nothing for the overflow part (`48em` is the max width of top
navigation bar).

```css
@media (max-width: 47.999em) {
  #sidebar-container {
    height: 0px;
    overflow: hidden;
  }
}
```

### Add JavaScript Code for Toggling the Navigation Bar

Now, the navigation bar cannot be expanded. To toggle between collapsed and
expanded state, we need to add some JavaScript code. We need to register a
click event on the triangle, which toggles the height of the navigation bar.

The following code is an example of how to register such click event:

```js
$(document).ready(function(){
  // #sidebar-collapse-toggle is the id of the button
  $("#sidebar-collapse-toggle").click(function(e) {
    e.preventDefault();
    // #sidebar-container is the id of the collapsed content
    const containerStyle = document.querySelector('#sidebar-container').style;
    const children = document.querySelectorAll('#sidebar-container div');
    const toggle = document.querySelector('.sidebar-collapse-toggle-icon').style;

    // calculate the total height of the children
    var totalHeight = 0;
    children.forEach(child => {
      totalHeight += parseInt(child.offsetHeight) + 20; // 20 is the margin-bottom
    });

    // Initially, the height field is empty, so we need to regard it as 0px
    if (containerStyle.height === '0px' || containerStyle.height === '') {
        containerStyle.height = `${totalHeight}px`;
        toggle.transform = 'rotate(180deg)';
    } else {
      containerStyle.height = '0px';
      toggle.transform = 'rotate(0deg)';
    }
  });
});
```

The code above basically does the following:

1. Find all elements that we need later;
2. Calculate the total height of the children (in this case, all the contents
   should be `div`s);
3. Expand/collapse the navigation bar and rotate the triangle based on the
   current height of the navigation bar.

Moreover, if we change the width of the window, the navigation bar might
not be with the correct height. So we also need to add a resize event to
recalculate the height of the navigation bar.

```js
window.addEventListener('resize', function() {
  // If the sidebar-container is not shown, return
  // This is used in the case of a full navigation bar
  if (this.document.querySelector('#sidebar-container') === null) return;

  // #sidebar-container is the id of the collapsed content
  const containerStyle = document.querySelector('#sidebar-container').style;
  const children = document.querySelectorAll('#sidebar-container div');

  // calculate the total height of the children
  var totalHeight = 0;
  children.forEach(child => {
    totalHeight += parseInt(child.offsetHeight) + 20; // 20 is the margin-bottom
  });
  if (containerStyle.height !== '0px' && containerStyle.height !== '') {
    containerStyle.height = `${totalHeight}px`;
  }
});
```

### Add the CSS for the Animation

Till this step, most of the work has been done. The toggling is functional,
but it is not smooth. Everything happens in a sudden, which is not intuitive.
To make it better, we need to add some animation for the transition.

For the height of the navigation bar, we can add something like
`transition: height 0.4s` to the css. With the existing css code, the css
for `#sidebar-container` should be like this: (you may change the `0.4s` to
the duration you like)

```css
@media (max-width: 47.999em) {
  #sidebar-container {
    height: 0px;
    overflow: hidden;
    transition: height 0.4s;
  }
}
```

For the rotation of the triangle, we can achieve similarly. The following code
is the style I choose.

```css
.sidebar-wrap-toggle-icon {
  transition: transform 0.2s ease-in-out;
}
```

You may search on the Internet for more effects you like.

## Some Minor Changes

There are also some minor changes I made to the site:

- Changing the width of the sidebar to relative width of the window;
- Use prettier Sans Serif and monospace font for the site;
- Use smoother rounded corners for code blocks;
- Apply `text-align: justify` to the text to make contents line up
  to the left and right edges for wide windows.

## Conclusion

After all these changes, my site looks better than before. However, I'm sure
there are still some improvements can be made! There's never a best design in
terms of the user experience. Difficult as it is, the job we should do is not
that hard. The key is to be aware of the user experience. If you find anything
uncomfortable, think about how to improve it. In this way, you can make your
site better and better.
