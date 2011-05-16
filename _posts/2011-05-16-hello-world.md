---
layout: post
title: Hello world!
---

[GitHub][] finally provides nerds best publishing platform ever:
[GitHub Pages][]. I, as a proud nerd, could not help, but join the
fun.

There was an unresolved tension about where to put my technical posts,
which would be appreciated by a very narrow set of my friends. I have
intuitive understanding of effort to outcome ratio which was not very
good for a self-written blog. On the other hand there was no blog
platform I liked, which would not be obfuscated with technical details,
unrelated to the very process of writing text.

Blog hosting like Livejournal or Blogger lack syntax highlighting and
markdown support; custom blog engine requires hosting and maintenance.
This was not that kind of important thing to occupy my attention for
maintenance efforts. What I learned through my IT career is that time
and attention are valuable and people tend to underestimate amount of
time it takes to complete even very simple things. (Postmortem to this
blog bootstrap showed that it took me 6 evenings to setup everything I
needed, self-hosted blog would imply adding yet another layer of
complexity.) So, when it turned out that [GitHub Pages][] combine pros
of both blog hosting and custom blog engine at the cost of minor some
manual fine-tuning, it was a bliss.

The minimal set of features I wanted my blog to have was:

* Markdown support,
* code syntax highlighting,
* RSS/Atom feed (routed through FeedBurner),
* comments
* Google Analytics support.

In order to achieve this functionality I had to do the following
things:

* write [custom index.html template](https://github.com/elephantum/elephantum.github.com/blob/master/index.html),
* write [posts.xml Atom format feed template](https://github.com/elephantum/elephantum.github.com/blob/master/posts.xml),
* setup FeedBurner account, route posts.xml through it,
* setup Disqus account, to turn on comments on static html-pages,
* setup Google Analytics account, embed counter to page template.

You can find all the things I did along with sources of this and
following blog posts in
[my GitHub repository](http://github.com/elephantum/elephantum.github.com/).

Just an idea: maybe it will make sense to create quickstart repo, which you can fork to
avoid repeating all the things to bootstrap [GitHub Pages][]
blog. Write me if you want one.

[GitHub]: http://github.com/
[GitHub Pages]: http://pages.github.com/
