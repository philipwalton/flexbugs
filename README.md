Flexbugs
========

This repo aims to be community curated list of cross-browser flexbox issues and known workarounds for them. The goal is that if you're building a website using flexbox and something isn't working as you'd expect, you can find the solution here.

As the spec continues to evolve and vendors nail down their implementations, this repo will be updated with newly discovered issues and remove old issues as their fixed or become obsolete.

# Known issues

1. [Minimum content sizing of flex items not honored](#)
2. [Column direction flex items set to `align-items:center` overflow their container](#)
3. [`min-height` on a flex container won't apply to its flex items](#)

## 1. Minimum content sizing of flex items not honored

<table>
  <tr>
    <th align="left">Demos</th>
    <th align="left">Browsers affected</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/full/MYbrrr">1.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/full/ByQJOQ">1.1.b</a> &mdash; <em>workaround</em><br>
      <a href="http://codepen.io/philipwalton/full/ByQJqQ">1.2.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/full/wBopYg">1.2.b</a> &mdash; <em>workaround</em>
    </td>
    <td>
      Chrome<br>
      Internet Explorer (10-11)<br>
      Opera<br>
      Safari
    </td>
  </tr>
</table>

When flex items are too big to fit inside their container, those items are instructed (by the flex layout algorithm) to shrink, proportionally, according to their `flex-shink` property. But contrary to what most browsers allow, they're *not* supposed to shrink indefinitely. They must always be at least as big as their minimum height or width properties declare, and if no minimum height or width properties are set, their minimum size should be the default minimum size of their content.

According to the [flexbox specification](http://www.w3.org/TR/css-flexbox/#flex-common):

> By default, flex items won’t shrink below their minimum content size (the length of the longest word or fixed-size element). To change this, set the min-width or min-height property.

### Workaround

The flexbox spec defines an initial `flex-shrink` value of `1` but says items should not shrink below their default minimum content size. You can get pretty much this exact same behavior by using a `flex-shrink` value of `0` instead of the default `1`. If your element is already being sized based on its children, and it hasn't set a `width`, `height`, or `flex-basis` value, then setting `flex-shrink:0` will render it the same way&mdash;but it will avoid this bug.

## 2. Column direction flex items set to `align-items:center` overflow their container

<table>
  <tr>
    <th align="left">Demos</th>
    <th align="left">Browsers affected</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/full/xbRpMe">2.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/full/ogYpVv">2.1.b</a> &mdash; <em>workaround</em><br>
    </td>
    <td>
      Internet Explorer (10-11)
    </td>
  </tr>
</table>

When using `align-items:center` on a flex container in the column direction, the contents of flex item, if too big on one line, will overflow their container in IE 10-11.

### Workaround

Most of the time, this can be fixed by simply setting `max-width:100%` on the flex item. If the flex item has a padding or border set, you'll also need to make sure to use `box-sizing:border-box` to account for that space. If the flex item has a margin, using `box-sizing` alone will not work, so you may need to use a container element with a padding instead.

## 3. `min-height` on a flex container won't apply to its flex items

<table>
  <tr>
    <th align="left">Demos</th>
    <th align="left">Browsers affected</th>
    <th align="left">Bug Reports</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/full/RNoZJP">3.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/full/KwNvLo">3.1.b</a> &mdash; <em>workaround</em><br>
      <a href="http://codepen.io/philipwalton/full/VYmbmj">3.2.a</a> &mdash; <em>bug</em><br>
    </td>
    <td>Internet Explorer 10-11</td>
    <td><a href="https://connect.microsoft.com/IE/feedback/details/802625/min-height-and-flexbox-flex-direction-column-dont-work-together-in-ie-10-11-preview">connect.microsoft.com</a></td>
  </tr>
</table>

In order for flex items to size and position themselves, they need to know how big their containers are. For example, if a flex item is supposed to be vertically centered, it needs to know how tall its parent is. The same is true when flex items are told to grow to fill the remaining empty space.

In IE 10-11, `min-height` declarations on flex containers in the column direction work to size the containers themselves, but their flex item children do not seem to know this information. They act as if no height has been set.

### Workaround

By far the most common element to apply `min-height` to is the body element, and usually you're setting it to `100%` (or `100vh`). Since the body element will never have contents below it, and since having a vertical scroll bar appear when there's a lot of content on the page is usually the desired behavior, substituting `height` for `min-height` will almost always work. Demo [3.1.b](#) shows the solution to [3.1.a](#).

There are cases, however, where no good workaround exists. Demo [3.2.a] shows a visual design where `min-height` is truly needed and a `height` substitution will not work. In such cases, you may need to rethink your design or resort to a [browser detection hack](http://stackoverflow.com/questions/20541306/how-to-write-a-css-hack-for-ie-11).

# About

Flexbugs was created as a follow-up to the article [Normalizing Cross-Browser Flexbox Issues](#). It's maintained by [@philwalton](https://twitter.com/philwalton) and [@gregwhitworth](https://twitter.com/gregwhitworth). If you have any questions or would like to get involved, please feel free to reach out to either of us on Twitter.

# Contributing

If you've discovered a flexbox bug or would like to submit a workaround, please open an issue or submit a pull request. Make sure to submit relevant test cases and screenshots and indicate which browsers are affected by the issue.
