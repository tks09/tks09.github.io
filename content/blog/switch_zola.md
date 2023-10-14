---
title: "Switching from Hugo to Zola"
date: 2023-10-14T16:15:25+02:00
updated: 2023-10-14T22:15:00+02:00
taxonomies:
  tags: ["web", "dev"]
---

## General

Today I switched the blogs page from [Hugo](https://gohugo.io) to [Zola](https://getzola.org). 

While Hugo was a great experience, I always thought that it might be very over the top of what I want to do. This is not some kind of simple content management system for me, but I also plan to fiddle around a little bit with the static site internet, especially HTML and CSS.

So the benefits to Hugo I see are:
- Due to my job I'm familiar with the great [Tera](https://keats.github.io/tera/docs/#getting-started) templating engine.
- Zola is written in Rust 🦀! It's probably not that I would ever need to make changes, but if so, it would be much easier for me to contribute.
- Simplicity. Hugo is great. But hugo is also just large. Zend isn't.

## ToDo

This is a living document, so you can read my todo list here:

- [X] About Page
- [X] Blog Pages 
- [X] GitLab Integration (yes, this page is hosted on [GitLab Pages](https://gitlab.com/tsmt/tobischmitt.net))
- [X] Taxonomies
- [X] Top Navigation
- [X] Basic CSS
- [X] RSS feeds
- [X] Extra: Dark mode *(this was much easier than I thought!)*

## Summary

A few hours later and I'm done. It's basically crazy how few steps I needed to move my personal homepage from Hugo to Zola. The basic steps I took today:

- Save `content/` folder, delete all hugo files.
- Install and init a `zola` project.
- Realize that basically everything except `tags` work. Great 😮!
  - this might be due to the fact that I used basically no hugo features. If you have bigger and more extensive sites, this might be much more difficult for you!
- try out some templates and realize I don't like them.
- Read the [great documentation](https://www.getzola.org/documentation/templates/overview/) about templates.
- spend a few hours writing a basic setup

What should I say? I'm pretty happy. While I was developing the template, I tried to read a lot about how to write a simplistic website and ended up with a [great website about brutalist web design](https://brutalist-web.design/#). While I did not implement all points yet, I like how fast you can go forward if you apply simple principles and keep the [complexity of your projects](/blog/barely-complex-yet-primitive/) small.
