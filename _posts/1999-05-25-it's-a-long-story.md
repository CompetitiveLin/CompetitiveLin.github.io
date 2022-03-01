---
title: "It's a Long Story"
excerpt: "This is a user-defined post excerpt. It should be displayed in place of the post content in archive-index pages." # This is a higher priority.
date: 1999-05-25T10:00:00+800
last_modified_at: 2022-02-28T19:00:00+800
categories:
  - Jekyll Tutorial
tags:
  - Markdown
  - Html
  - YAML Front
header:
  overlay_image: /assets/images/unsplash-image-1.jpg
  # og_image: /assets/images/unsplash-og-mage-1.jpg
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
  actions:
    - label: "Learn more"
      url: "https://unsplash.com"

show_date: true # Display realse date of the article. Default FALSE
read_time: true # Display how much time of reading the article. Default TRUE
related: true   # Display related articles below or not. Default TRUE
share: true     # Display share icon.
comments: true  # Not working
# link: https://github.com
---

All children, except one, grow up. <!--more--> They soon know that they will grow up, and the way Wendy knew was this[^1]. One day when she was two years old she was playing in a garden, and she plucked another flower and ran with it to her mother. I suppose she must have looked rather delightful, for Mrs. Darling put her hand to her heart and cried, "Oh, why can't you remain like this for ever!" This was all that passed between them on the subject, but henceforth Wendy knew that she must grow up. You always know after you are two. Two is the beginning of the end. 
<!-- Excerpt_separator below works -->

```_config.yml_
excerpt_separator: "<!--more-->"
```

{% include toc %}    <!-- Helper -->

# Header image
A header image with an OpenGraph override.

```yaml
header:
  overlay_image: /assets/images/unsplash-image-1.jpg
  # og_image: /assets/images/unsplash-og-mage-1.jpg
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

### Ordered -- Unordered -- Ordered

1. ordered item
2. ordered item 
   * **unordered**
   * **unordered** 
     1. ordered item
     2. ordered item
3. ordered item
4. ordered item

### Ordered -- Unordered -- Unordered

1. ordered item
2. ordered item 
   * **unordered**
   * **unordered** 
     * unordered item
     * unordered item
3. ordered item
4. ordered item

### Unordered -- Ordered -- Unordered

* unordered item
* unordered item 
  1. ordered
  2. ordered 
     * unordered item
     * unordered item
* unordered item
* unordered item

### Unordered -- Unordered -- Ordered

* unordered item
* unordered item 
  * unordered
  * unordered 
    1. **ordered item**
    2. **ordered item**
* unordered item
* unordered item

### Task Lists

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
#### New Site Features

* You can now have cover images on blog pages
* Drafts will now auto-save while writing
{% endcapture %}{% endraw %}

<div class="notice">{% raw %}{{ notice-2 | markdownify }}{% endraw %}</div>
```

{% capture notice-2 %}
#### New Site Features

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






# Date modified
**This post has been updated and show a modified date.**

[^1]: Texture image courtesty of [Lovetextures](http://www.lovetextures.com/)