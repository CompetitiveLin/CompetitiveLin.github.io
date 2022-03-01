---
title: &title It's a Long Story
excerpt: This is a user-defined post excerpt. It should be displayed in place of the post content in archive-index pages.
# tagline: This is a custom tagline content which overrides the *default* page excerpt.
date: 1999-05-25T10:00:00+800
last_modified_at: 2022-02-28T19:00:00+800

categories: ["Uncategorized"]
tags: Markdown Html YAML Grammar
header:
  overlay_image: /assets/images/unsplash-image-1.jpg
  # caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
  # actions:
  #   - label: "Learn more"
  #     url: "https://unsplash.com"

show_date: true # Display realse date of the article. Default FALSE
read_time: true # Display how much time of reading the article. Default TRUE
related: true   # Display related articles below or not. Default TRUE
share: true     # Display share icon. Default TRUE
comments: true  # Not working TO BE CONTINUED
toc: true
toc_sticky: true
toc_label: *title
# toc_icon: heart
---

# Header image
A header image with an OpenGraph override.

```yaml
header:
  overlay_image: /assets/images/unsplash-image-1.jpg
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
  actions:
    - label: "Learn more"
      url: "https://unsplash.com"
```


# Title should not overflow the content area. 
A few things to check for:

  * Non-breaking text in the title, content, and comments should have no adverse effects on layout or functionality.
  * Check the browser window / tab title.
  * If you are a theme developer, check that this text does not break anything.

The following CSS properties will help you support non-breaking text.

```css
-ms-word-wrap: break-word;
word-wrap: break-word;
```

# Nested and mixed lists
Nested and mixed lists are an interesting beast. It's a corner case to make sure that

* Lists within lists do not break the ordered list numbering order
* Your list styles go deep enough.

## Ordered -- Unordered -- Ordered

1. ordered item
2. ordered item 
   * **unordered**
   * **unordered** 
     1. ordered item
     2. ordered item
3. ordered item
4. ordered item

## Ordered -- Unordered -- Unordered

1. ordered item
2. ordered item 
   * **unordered**
   * **unordered** 
     * unordered item
     * unordered item
3. ordered item
4. ordered item

## Unordered -- Ordered -- Unordered

* unordered item
* unordered item 
  1. ordered
  2. ordered 
     * unordered item
     * unordered item
* unordered item
* unordered item

## Unordered -- Unordered -- Ordered

* unordered item
* unordered item 
  * unordered
  * unordered 
    1. **ordered item**
    2. **ordered item**
* unordered item
* unordered item

## Task Lists

- [x] Finish my changes
- [ ] Push my commits to GitHub
- [ ] Open a pull request

# Notice
A notice displays information that explains nearby content. Often used to call attention to a particular detail.

When using Kramdown `{: .notice}` can be added after a sentence to assign the `.notice` to the `<p></p>` element. 

**Changes in Service:** We just updated our [privacy policy](#) here to better service our customers. We recommend reviewing the changes.
{: .notice}

**Primary Notice:** Lorem ipsum dolor sit amet, consectetur adipiscing elit. Integer nec odio. [Praesent libero](#). Sed cursus ante dapibus diam. Sed nisi. Nulla quis sem at nibh elementum imperdiet.
{: .notice--primary}

**Info Notice:** Lorem ipsum dolor sit amet, [consectetur adipiscing elit](#). Integer nec odio. Praesent libero. Sed cursus ante dapibus diam. Sed nisi. Nulla quis sem at nibh elementum imperdiet.
{: .notice--info}

**Warning Notice:** Lorem ipsum dolor sit amet, consectetur adipiscing elit. [Integer nec odio](#). Praesent libero. Sed cursus ante dapibus diam. Sed nisi. Nulla quis sem at nibh elementum imperdiet.
{: .notice--warning}

**Danger Notice:** Lorem ipsum dolor sit amet, [consectetur adipiscing](#) elit. Integer nec odio. Praesent libero. Sed cursus ante dapibus diam. Sed nisi. Nulla quis sem at nibh elementum imperdiet.
{: .notice--danger}

**Success Notice:** Lorem ipsum dolor sit amet, consectetur adipiscing elit. Integer nec odio. Praesent libero. Sed cursus ante dapibus diam. Sed nisi. Nulla quis sem at [nibh elementum](#) imperdiet.
{: .notice--success}

Want to wrap several paragraphs or other elements in a notice? Using Liquid to capture the content and then filter it with `markdownify` is a good way to go.

```html
{% raw %}{% capture notice-2 %}
## New Site Features

* You can now have cover images on blog pages
* Drafts will now auto-save while writing
{% endcapture %}{% endraw %}

<div class="notice">{% raw %}{{ notice-2 | markdownify }}{% endraw %}</div>
```

{% capture notice-2 %}
## New Site Features

* You can now have cover images on blog pages
* Drafts will now auto-save while writing
{% endcapture %}

<div class="notice">
  {{ notice-2 | markdownify }}
</div>

Or you could skip the capture and stick with straight HTML.

```html
<div class="notice">
  <h4>Message</h4>
  <p>A basic message.</p>
</div>
```

<div class="notice">
  <h4>Message</h4>
  <p>A basic message.</p>
</div>


# Quote

> Only one thing is impossible for God: To find any sense in any copyright law on the planet.
  
> <cite><a href="http://www.brainyquote.com/quotes/quotes/m/marktwain163473.html">Mark Twain</a></cite>


# Link

This theme supports **link posts**, made famous by John Gruber. To use, just add `link: https://github.com` to the post's YAML front matter and you're done.

Some [link](#) can also be shown.


# Twitter embedded

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">🎨 Finally got around to adding all my <a href="https://twitter.com/procreateapp">@procreateapp</a> creations with time lapse videos <a href="https://t.co/1nNbkefC3L">https://t.co/1nNbkefC3L</a> <a href="https://t.co/gcNLJoJ0Gn">pic.twitter.com/gcNLJoJ0Gn</a></p>&mdash; Michael Rose (@mmistakes) <a href="https://twitter.com/mmistakes/status/662678050795094016">November 6, 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

This post tests Twitter Embeds.


# Video embedded

YouTube video embedded below.

<iframe width="640" height="360" src="https://www.youtube.com/embed/fBnAMUkNM2k" frameborder="0" allowfullscreen></iframe>

<center><b>ENGLISH SPEECH | MUNIBA MAZARI - We all are Perfectly Imperfect (English Subtitles)</b></center>

Here is the code:
```
<iframe width="640" height="360" src="https://www.youtube.com/embed/fBnAMUkNM2k" frameborder="0" allowfullscreen></iframe>
```


# Image embedded
[![foo](https://live.staticflickr.com/8361/8400335147_5fabaa504c_o.jpg)](https://flic.kr/p/dNiUYB)

<!-- ![avatar](http://baidu.com/pic/doge.png) -->


# Tables

| Header1 | Header2 | Header3 |
|:--------|:-------:|--------:|
| cell1   | cell2   | cell3   |
| cell4   | cell5   | cell6   |
|----
| cell1   | cell2   | cell3   |
| cell4   | cell5   | cell6   |
|=====
| Foot1   | Foot2   | Foot3
{: rules="groups"}


# Buttons

Make any link standout more when applying the `.btn` class.

```html
<a href="#" class="btn btn--success">Success Button</a>
```

<div markdown="0"><a href="#" class="btn">Primary Button</a></div>
<div markdown="0"><a href="#" class="btn btn--success">Success Button</a></div>
<div markdown="0"><a href="#" class="btn btn--warning">Warning Button</a></div>
<div markdown="0"><a href="#" class="btn btn--danger">Danger Button</a></div>
<div markdown="0"><a href="#" class="btn btn--info">Info Button</a></div>


# Image alignment

Welcome to image alignment! The best way to demonstrate the ebb and flow of the various image positioning options is to nestle them snuggly among an ocean of words. Grab a paddle and let's get started.

[![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/image-alignment-580x300.jpg){: .align-center}]({{ site.url }}{{ site.baseurl }}/assets/images/image-alignment-580x300.jpg)

The image above happens to be **centered**.

[![image-left]({{ site.url }}{{ site.baseurl }}/assets/images/image-alignment-150x150.jpg){: .align-left}]({{ site.url }}{{ site.baseurl }}/assets/images/image-alignment-150x150.jpg) The rest of this paragraph is filler for the sake of seeing the text wrap around the 150×150 image, which is **left aligned**.

As you can see there should be some space above, below, and to the right of the image. The text should not be creeping on the image. Creeping is just not right. Images need breathing room too. Let them speak like you words. Let them do their jobs without any hassle from the text. In about one more sentence here, we'll see that the text moves from the right of the image down below the image in seamless transition. Again, letting the do it's thing. Mission accomplished!

And now for a **massively large image**. It also has **no alignment**.

[![no-alignment]({{ site.url }}{{ site.baseurl }}/assets/images/image-alignment-1200x4002.jpg)]({{ site.url }}{{ site.baseurl }}/assets/images/image-alignment-1200x4002.jpg)

The image above, though 1200px wide, should not overflow the content area. It should remain contained with no visible disruption to the flow of content.

[![image-right]({{ site.url }}{{ site.baseurl }}/assets/images/image-alignment-300x200.jpg){: .align-right}]({{ site.url }}{{ site.baseurl }}/assets/images/image-alignment-300x200.jpg)

And now we're going to shift things to the **right align**. Again, there should be plenty of room above, below, and to the left of the image. Just look at him there --- Hey guy! Way to rock that right side. I don't care what the left aligned image says, you look great. Don't let anyone else tell you differently.

In just a bit here, you should see the text start to wrap below the right aligned image and settle in nicely. There should still be plenty of room and everything should be sitting pretty. Yeah --- Just like that. It never felt so good to be right.

And just when you thought we were done, we're going to do them all over again with captions!

<figure class="align-center">
  <a href="{{ site.url }}{{ site.baseurl }}/assets/images/image-alignment-580x300.jpg">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/image-alignment-580x300.jpg" alt="">
  </a>
  <figcaption>Look at 580 x 300 getting some love.</figcaption>
</figure> 

The figure above happens to be **centered**. The caption also has a link in it, just to see if it does anything funky.

<figure style="width: 150px" class="align-left">
  <a href="{{ site.url }}{{ site.baseurl }}/assets/images/image-alignment-150x150.jpg">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/image-alignment-150x150.jpg" alt="">
  </a>
  <figcaption>Itty-bitty caption.</figcaption>
</figure> 

The rest of this paragraph is filler for the sake of seeing the text wrap around the 150×150 image, which is **left aligned**.

As you can see there should be some space above, below, and to the right of the image. The text should not be creeping on the image. Creeping is just not right. Images need breathing room too. Let them speak like you words. Let them do their jobs without any hassle from the text. In about one more sentence here, we'll see that the text moves from the right of the image down below the image in seamless transition. Again, letting the do it's thing. Mission accomplished!

And now for a **massively large image**. It also has **no alignment**.

<figure style="width: 1200px">
  <a href="{{ site.url }}{{ site.baseurl }}/assets/images/image-alignment-1200x4002.jpg">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/image-alignment-1200x4002.jpg" alt="">
  </a>
  <figcaption>Massive image comment for your eyeballs.</figcaption>
</figure> 

The figure element above has an inline style of `width: 1200px` set which should break it outside of the normal content flow.

<figure style="width: 300px" class="align-right">
  <a href="{{ site.url }}{{ site.baseurl }}/assets/images/image-alignment-300x200.jpg">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/image-alignment-300x200.jpg" alt="">
  </a>
  <figcaption>Feels good to be right all the time.</figcaption>
</figure> 

And now we're going to shift things to the **right align**. Again, there should be plenty of room above, below, and to the left of the image. Just look at him there --- Hey guy! Way to rock that right side. I don't care what the left aligned image says, you look great. Don't let anyone else tell you differently.

In just a bit here, you should see the text start to wrap below the right aligned image and settle in nicely. There should still be plenty of room and everything should be sitting pretty. Yeah --- Just like that. It never felt so good to be right.

And that's a wrap, yo! You survived the tumultuous waters of alignment. Image alignment achievement unlocked!


# Date modified

**This post has been updated and show a modified date.**

[^1]: Texture image courtesty of [Lovetextures](http://www.lovetextures.com/)