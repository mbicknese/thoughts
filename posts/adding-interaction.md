---
title: Adding interaction
description: How I added a Twitter button for social interaction.
date: 2022-01-09
tags:
 - meta
layout: layouts/post.njk
reading-time: 120
---
Alright, my blog launched. But now, I'm missing interaction with possible readers. Time for a new feature, and here's the story:

> As a Maarten (that's me)  
> I want to get feedback from my audience  
> So I know whether people like the content as well as to engage in discussions about my topics.

So there's a couple of ways to go about this. After a quick brainstorm, I came up with: I could implement a comment section; add some tracking to see who visited; add social media buttons; leave my email address.

From these options, using social media sounds the easiest to implement. Plus, I don't need to manage another platform. Most of my technical talk takes place on Twitter, so let's start with that. Remember, small steps make progress.

The footer now states: "Written by Maarten Bicknese." Let's add a call to action right there. Something along the lines of "let me know what you think of this on [Twitter Icon]." This already raises the question of how to add the icon there. The first part is simple tho, the `<footer />` lives in `_includes/layouts/base.njk` (I remembered this. Otherwise, I would have looked it up by a find in files/folder for "<footer").

There's probably a resource from Twitter itself on linking to tweets.

Scrap that. We want it the other way around. This time Google for "How to create a share on Twitter link" (which should direct you to the [Twitter docs](https://developer.twitter.com/en/docs/twitter-for-websites/tweet-button/overview)). I went with plain HTML, no logic or styling.

```html
    <a 
      rel="external" 
      href="https://twitter.com/intent/tweet?text=I%20just%20read%20%40MaartenBicknese%27s%20blog"
    >
        let me know what you think!
    </a>
```
I took the code provided and added the `rel` attribute because that's good for SEO. Right? And using an online [URL Encoder](https://www.urlencoder.org) to set the text to "I just read @MaartenBicknese's blog." That text isn't as cool as it can be. Maybe we can improve on it by linking to the current post. Time to dive into the [11ty docs](https://www.11ty.dev/docs/). I clicked through the docs for a little but seeing I'm not into 11ty I had no clue what I was looking at. So time to fall back to the basics, `location.href` to the rescue. Oh right, Nunjucks doesn't support executing JavaScript straight away.

Screw this, google "11ty link to current page". Oh cool ah 'page' variable. Oh damn, it doesn't have a full URL to my page. Oh [but this should help](https://stackoverflow.com/questions/60309000/generating-full-urls-on-eleventy-and-netlify). Oh but it doesn't.

Maybe it's because I'm not so bright. Perhaps it's because I switched projects too often. But trying to get dynamic variables injected in a static context will not work. Time for manual JavaScript. I added the `twitter-cta` id to the link and the following JS one-liner.

```javascript
    <script>
        window['twitter-cta'].href += encodeURIComponent(location.href);
    </script>
```

And everything is well in Maarten land. See [this commit](https://github.com/mbicknese/[magic]) for this post's changes.
