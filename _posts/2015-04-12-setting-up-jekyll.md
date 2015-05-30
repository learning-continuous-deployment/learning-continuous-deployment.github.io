---
layout: post
title:  "Setting up a Blog on GitHub Pages"
date:   2015-04-12 13:49:00
categories: jekyll blog GitHub-Pages
banner_image: github-pages.jpg
comments: true
author_name: Marius
---
Hi, this is the first post in our blog and the very first post which I'm writing. We set up this blog to create a small documentation about our work, experiences and results which we will get when we build a continuous deployment chain with Git, Jenkins and Docker.

 <!--more-->

As we are using GitHub as hosting service for our Git repositories, we decided to use their offered service [GitHub Pages](https://pages.github.com) to create a Blog, with the advantage that we don't need an additional server to host this. GitHub provides for each repository the opportunity to build a website. For this, a new repository has to be created and named `NAME.github.io`, where *NAME* is your repository name (or username and organization name respectively when you want to create a website for yourself or for your organisation) on GitHub.

To build the actual Blog, we are using [Jekyll](http://jekyllrb.com). Jekyll is an open-source software to create static websites and it is supported by GitHub. The source-code of the Jekyll website is pushed into the GitHub repository and automatically build to a static website and published on the specified URL. To edit the blog, simply checkout the repository, edit the desired files, commit and push them back. That's it!

For the reason that we don't need a fancy website or blog, we decided to use a pre-build blog template for Jekyll. For example, many templates and themes can be found on [Jekyll Themes](http://jekyllthemes.org). We chose the [Gaya Jekyll Theme](https://github.com/gayanvirajith/gaya).

*The following description is related to the directory structure of this template.*

## Adding posts

Each blog post originates from a file in the `_posts` directory. The post itself is written in the markup language *markdown*. That's the same language as used by GitHub to display e.g. readme files.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext`. For example: `2015-04-12-setting-up-jekyll.md`.

The top of the file is a kind of a header (containing details of the post like title, date, keywords) following by the post itself. You can simply copy the example post below and change the attributes. `categories` and `banner_image` are optional fields.

The attribute `banner_image` defines the image which is shown on the top. Here, you have to specify the filename of the image. The image itself has to be located in the directory `/assets/images` of the repository. A good source for free images is [StockSnap](https://stocksnap.io).

### Example blog post

    ---
    layout: post
    title:  "Setting up a Blog on GitHub Pages"
    date:   2015-04-12 13:49:00
    categories: jekyll blog GitHub-Pages
    banner_image: github-pages.jpg
    comments: true
    author_name: Marius
    ---
    This is my post.
    
### Writing a post

As mentioned above, posts are written in *markdown*. If you are not familiar with it, you can take a look at this [cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) containing all important commands.

There are many online tools which provide a WYSIWGY editor. A good markdown editor is [Dillinger](http://dillinger.io). It has a preview window where you can see the interpreted markdown code.

A special feature of Jekyll is code highlighting. Check out the source code of this post to see how it's done.

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
{% endhighlight %}

When you're done writing the post, just commit and push the file into the repository and that's it.