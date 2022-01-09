---
title: This is a blog post.
description: This is a post on My Blog.
date: 2021-09-17
tags:
  - meta
layout: layouts/post.njk
reading-time: 180
---
As you can see, this is a post on my blog. A blog post, if you will.

Inspired by [one of my tweets](https://twitter.com/MaartenBicknese/status/1412849046184775680?s=20), it is time to start writing posts. A lot of "important" people on Twitter are raving about [Eleventy](https://www.11ty.dev), so to keep things simple and fresh, I jumped on it. Their starter projects include this blog setup.

I must admit that it aches not to go in-depth and change a lot of code. But to get this blog started, it's best to leave everything as is and start writing. I do have some plans for the future. Styling it with [Tailwind CSS](https://tailwindcss.com); Add reading time (which is about 3 minutes for this article); Create an about me page; and more.

Please like, subscribe, and listen to this word of my sponsor. Seriously, though, there should be a feed you can follow if you'd like to see upcoming content.

Now all that's left to do, is to get this site actually op on running on my family's domain.

p.s. I lied. I couldn't help but center the content. Initially, I set the width to `768px`, but the `p` elements already had a max-width. It made more sense to match these sizes.
```css
  header, main, footer {
    max-width: 37.5em;
    margin: 0 auto;
  }
```

## post mortem

After writing this, there was one last thing to do: deploy it. My family's web-hosting upload tool sadly didn't understand subdirectories. As a result, it ended in empty files for each directory.

Luckily there's good ol' FTP, and dinosaurs like me have FTP skills. Yet, the FTP toolset is weird and tiresome, so I tried connecting to the FTP server using Finder. The `Go > Connect to server` option is there, but it wouldn't take my credentials. So my first take was to verify the credentials, they checked out. Next option, my username included a special character. Some googling told me this might not play well with Finder. Yet trying the "root" account, which did not have any special characters, it became apparent that it was actually not the cause. At this point, I dropped the option of using Finder.

Next up, plain old FTP in the terminal. Fun story, it isn't installed on Mac by default, but only a single command away to get it installed.

```bash
  brew install inetutils
```

(if you don't have so already, please get yourself [Homebrew](https://brew.sh))

The old commands quickly returned to me and reminded me what a pain in the ass it is to use FTP. There is no recursive directory copying. Mighty Stack Overflow, tell me which tool is the best FTP substitute. Answer: ncftp. FTP on steroids, and it has recursive directory uploading! Blog uploaded, all done. NOPE!

I forgot to change the title; the word "Archive" preached high above my nice post. While uploading, I noticed it uploaded the articles from the base template. It looks like [Eleventy](https://www.11ty.dev) is suffering from some caching issues. Solution: remove the `_site` directory (and change the title). Upload attempt #2.

`ncftp` doesn't offer an option to fully remove a directory with all its subdirectories. I could have looked for another, dare I say better, tool. But the web-interface served me well this time. Remake the directory and upload the site.

```bash
  mkdir thoughts
  cd thoughts # don't forget this step, you'll upload everything in the wrong dir
  mput -r *
```

(free pro tip: use `ctrl + d` to quit about any CLI tool).

You can find all the changes I made in [this commit](https://github.com/mbicknese/thoughts/commit/8b49bb22211ef6a63e6baf9d682fbd09fcb7f363).
