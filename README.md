Flexbugs
========

This repo aims to be a community curated list of cross-browser flexbox issues and known workarounds for them. The goal is that if you're building a website using flexbox and something isn't working as you'd expect, you can find the solution here.

As the spec continues to evolve and vendors nail down their implementations, this repo will be updated with newly discovered issues and remove old issues as they're fixed or become obsolete.

## The bugs and their workarounds

1. [Minimum content sizing of flex items not honored](#1-minimum-content-sizing-of-flex-items-not-honored)
2. [Column flex items set to `align-items:center` overflow their container](#2-column-flex-items-set-to-align-itemscenter-overflow-their-container)
3. [`min-height` on a column flex container won't apply to its flex items](#3-min-height-on-a-column-flex-container-wont-apply-to-its-flex-items)
4. [`flex` shorthand declarations with unitless `flex-basis` values are ignored](#4-flex-shorthand-declarations-with-unitless-flex-basis-values-are-ignored)
5. [Column flex items don't always preserve intrinsic aspect ratios](#5-column-flex-items-dont-always-preserve-intrinsic-aspect-ratios)
6. [The default `flex` value has changed](#6-the-default-flex-value-has-changed)


### 1. Minimum content sizing of flex items not honored

<table>
  <tr>
    <th align="left">Demos</th>
    <th align="left">Browsers affected</th>
    <th align="left">Tracking bugs</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/MYbrrr">1.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/ByQJOQ">1.1.b</a> &mdash; <em>workaround</em><br>
      <a href="http://codepen.io/philipwalton/pen/ByQJqQ">1.2.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/wBopYg">1.2.b</a> &mdash; <em>workaround</em>
    </td>
    <td>
      Chrome<br>
      Opera<br>
      Safari
    </td>
    <td>
      <a href="https://code.google.com/p/chromium/issues/detail?id=426898">Chrome #426898</a><br>
      <a href="https://bugzilla.mozilla.org/show_bug.cgi?id=1043520">Firefox #1043520</a>
    </td>
  </tr>
</table>

When flex items are too big to fit inside their container, those items are instructed (by the flex layout algorithm) to shrink, proportionally, according to their `flex-shrink` property. But contrary to what most browsers allow, they're *not* supposed to shrink indefinitely. They must always be at least as big as their minimum height or width properties declare, and if no minimum height or width properties are set, their minimum size should be the default minimum size of their content.

According to the [current flexbox specification](http://www.w3.org/TR/css-flexbox/#flex-common):

> By default, flex items won’t shrink below their minimum content size (the length of the longest word or fixed-size element). To change this, set the min-width or min-height property.

#### Workaround

The flexbox spec defines an initial `flex-shrink` value of `1` but says items should not shrink below their default minimum content size. You can get pretty much this exact same behavior by using a `flex-shrink` value of `0` instead of the default `1`. If your element is already being sized based on its children, and it hasn't set a `width`, `height`, or `flex-basis` value, then setting `flex-shrink:0` will render it the same way&mdash;but it will avoid this bug.

### 2. Column flex items set to `align-items:center` overflow their container

<table>
  <tr>
    <th align="left">Demos</th>
    <th align="left">Browsers affected</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/xbRpMe">2.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/ogYpVv">2.1.b</a> &mdash; <em>workaround</em><br>
    </td>
    <td>
      Internet Explorer 10-11 (fixed in 12+)
    </td>
  </tr>
</table>

When using `align-items:center` on a flex container in the column direction, the contents of flex item, if too big, will overflow their container in IE 10-11.

#### Workaround

Most of the time, this can be fixed by simply setting `max-width:100%` on the flex item. If the flex item has a padding or border set, you'll also need to make sure to use `box-sizing:border-box` to account for that space. If the flex item has a margin, using `box-sizing` alone will not work, so you may need to use a container element with padding instead.

### 3. `min-height` on a column flex container won't apply to its flex items

<table>
  <tr>
    <th align="left">Demos</th>
    <th align="left">Browsers affected</th>
    <th align="left">Tracking bugs</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/RNoZJP">3.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/KwNvLo">3.1.b</a> &mdash; <em>workaround</em><br>
      <a href="http://codepen.io/philipwalton/pen/VYmbmj">3.2.a</a> &mdash; <em>bug</em><br>
    </td>
    <td>Internet Explorer 10-11 (fixed in 12+)</td>
    <td><a href="https://connect.microsoft.com/IE/feedback/details/802625/min-height-and-flexbox-flex-direction-column-dont-work-together-in-ie-10-11-preview">IE #802625</a></td>
  </tr>
</table>

In order for flex items to size and position themselves, they need to know how big their containers are. For example, if a flex item is supposed to be vertically centered, it needs to know how tall its parent is. The same is true when flex items are told to grow to fill the remaining empty space.

In IE 10-11, `min-height` declarations on flex containers in the column direction work to size the containers themselves, but their flex item children do not seem to know the size of their parents. They act as if no height has been set at all.

#### Workaround

By far the most common element to apply `min-height` to is the body element, and usually you're setting it to `100%` (or `100vh`). Since the body element will never have any content below it, and since having a vertical scroll bar appear when there's a lot of content on the page is usually the desired behavior, substituting `height` for `min-height` will almost always work as shown in demo [3.1.b](http://codepen.io/philipwalton/pen/KwNvLo).

There are cases, however, where no good workaround exists. Demo [3.2.a](http://codepen.io/philipwalton/pen/VYmbmj) shows a visual design where `min-height` is truly needed and a `height` substitution will not work. In such cases, you may need to rethink your design or resort to a [browser detection hack](http://stackoverflow.com/questions/20541306/how-to-write-a-css-hack-for-ie-11).

### 4. `flex` shorthand declarations with unitless `flex-basis` values are ignored

<table>
  <tr>
    <th align="left">Demos</th>
    <th align="left">Browsers affected</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/OPbQgO">3.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/ByQYZJ">3.1.b</a> &mdash; <em>workaround</em>
    </td>
    <td>Internet Explorer 10-11 (fixed in 12+)</td>
  </tr>
</table>

Prior to the release of IE 10, the [flexbox spec at the time](http://www.w3.org/TR/2012/WD-css3-flexbox-20120322/#flexibility) stated that a flexbox item's preferred size required a unit when using the `flex` shorthand:

>  If the &lt;preferred-size&gt; is ‘0’, it must be specified with a unit (like ‘0px’) to avoid ambiguity; unitless zero will either be interpreted as as one of the flexibilities, or is a syntax error.

This is no longer true in the spec, but IE 10-11 still treat it as true. If you use the declaration `flex: 1 0 0` in one of these browsers, it will be an error and the entire rule (including all the flexibility properties) will be ignored.

#### Workaround

When using the `flex` shorthand, always include a unit in the `flex-basis` portion. For example: `1 0 0%`.

**Important:** using a `flex` value of something like `1 0 0px` can still be a problem because many CSS minifiers will convert `0px` to `0`. To avoid this, make sure to use `0%` instead of `0px` since most minifiers won't touch percentage values for other reasons.

### 5. Column flex items don't always preserve intrinsic aspect ratios

<table>
  <tr>
    <th align="left">Demos</th>
    <th align="left">Browsers affected</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/LEbQON">5.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/wBoyry">5.1.b</a> &mdash; <em>workaround</em>
    </td>
    <td>Internet Explorer 10-11 (fixed in 12+)</td>
  </tr>
</table>

The [March 2014 spec](http://www.w3.org/TR/2014/WD-css-flexbox-1-20140325/#min-size-auto) has the following to say about how size determinations are made for flex items:

> On a flex item whose overflow is not visible, this [auto] keyword specifies as the minimum size the smaller of: (a) the min-content size, or (b) the computed width/height, if that value is definite.

Demo [5.1.a](http://codepen.io/philipwalton/pen/LEbQON) contains an image whose height is 200 pixels and whose width is 500 pixels. Its container, however, is only 300 pixels wide, so after the image is scaled to fit into that space, its computed height should only be 120 pixels. The text quoted above does not make it clear as to whether the flex item's min-content size should be based the image's actual height or scaled height.

The [most recent spec](http://dev.w3.org/csswg/css-flexbox/#min-size-auto) has resolved this ambiguity in favor of using sizes that will preserve an element's intrinsic aspect ratio.

#### Workaround

You can avoid this problem by adding a container element to house the element with the intrinsic aspect ratio. Since doing this causes the element with the intrinsic aspect ratio to no longer be a flex item, it will be sized normally.

### 6. The default `flex` value has changed

<table>
  <tr>
    <th align="left">Demos</th>
    <th align="left">Browsers affected</th>
  </tr>
  <tr valign="top">
    <td>
      <a href="http://codepen.io/philipwalton/pen/myOYqW">6.1.a</a> &mdash; <em>bug</em><br>
      <a href="http://codepen.io/philipwalton/pen/azBrLo">6.1.b</a> &mdash; <em>workaround</em>
    </td>
    <td>Internet Explorer 10 (fixed in 11+)</td>
  </tr>
</table>

When IE 10 was being developed, the [March 2012 spec](http://www.w3.org/TR/2012/WD-css3-flexbox-20120322/#flexibility) said the initial value for the `flex` property was `none`, which translates to `0 0 auto`. The [most recent spec](http://www.w3.org/TR/css3-flexbox/#flex-property) sets the initial `flex` value to the initial values of the individual flexibility properties, which corresponds to `initial` or `0 1 auto`. Notice that this means IE 10 uses a different initial `flex-shrink` value (technically it was called `neg-flex` in the spec at the time) than every other browser. Other browsers (including IE 11) use an initial value of `1` rather than `0`.

#### Workaround

If you have to support IE 10, the best solution is to *always* set an explicit `flex-shrink` or `flex` value on all of your flex items. Demo [6.1.a](http://codepen.io/philipwalton/pen/myOYqW) shows how not setting any flexibility properties causes an error.

## Acknowledgements

Flexbugs was created as a follow-up to the article [Normalizing Cross-Browser Flexbox Bugs](http://philipwalton.com/articles/normalizing-cross-browser-flexbox-bugs/). It's maintained by [@philwalton](https://twitter.com/philwalton) and [@gregwhitworth](https://twitter.com/gregwhitworth). If you have any questions or would like to get involved, please feel free to reach out to either of us on Twitter.

## Contributing

If you've discovered a flexbox bug or would like to submit a workaround, please open an issue or submit a pull request. Make sure to submit relevant test cases or screenshots and indicate which browsers are affected.
