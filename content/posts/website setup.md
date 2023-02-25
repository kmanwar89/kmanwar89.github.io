---
title: "Website Setup"
description: "Everything is a learning opportunity"
date: 2023-02-24T22:22:08-05:00
draft: true
toc: true
images:
tags:
  - ["web_dev"]
---

## Intro 

In the olden days, website hosting involved:

* Getting a domain
* Paying for a hosting service (and bandwidth)
* Manually editing HTML/CSS files
* Keeping track of security patches, updates, hoping some script kiddie didn't DDoS you...
* etc.

Now, you can deploy a website via a GitHub workflow in *seconds*, with full encryption thanks to Let's Encrypt. It's honestly incredible, and shows how far we've come.

I am not a web developer by any means, but I've been playing around with HTML/CSS since the days of Yahoo! GeoCities (and my old website still exists on archive.org, but that URL is a secret I will take **to my grave**). As part of my self-hosting journey, I considered hosting this very website at home, fronted by a reverse proxy (I recommend [nginx proxy manager](https://nginxproxymanager.com/)), however I figured I would give GitHub pages a try.  So, this post is a dumping ground of thoughts/challenges I came across while figuring out this new-fangled way of website hosting.

## Finding Inspiration

While browsing /r/selfhosted one evening, I came across [a post](https://jacobcolvin.com/posts/2023/01/backups-for-k8s-and-beyond/) from another Redditor, and fell in love with the website theme setup. With his blessing, I copied over his repository and got to work.

Well, what I didn't anticipate was that in trying to emulate his website, I had to learn about:

* Interpreting how his code was setup
* Setting up and using GitHub workflows
* Learning the setup and syntax of the Hugo static website generator framework
* Refreshing long-unpracticed skills with Git, and learning about `submodules`

and several other things.  What a journey!

## Getting to work
### Figuring out Hugo pages, locally

Once the repository was cloned, and Hugo installed, I tried to render the page locally. After some tinkering, I discovered that I needed to use the following syntax:

`hugo server --bind=<ip of machine running hugo> --baseURL=http://<ip of machine running hugo> -p <custom port>`

When trying to render the page without the `--baseURL` flag, it was attempting to serve content from `localhost`, which doesn't work if you're accessing it remotely.

### Learning the Hugo syntax
Ok, I've got Hugo serving pages locally, great! Now, I had to dig into the syntax to figure out weird and obscure terms that Hugo uses: `taxonomies`, `front matter`, `archetypes`, etc.

Essentially, here's how I understand it:

* Install Hugo
* Find a theme you like
* This theme will contain a "template", essentially.  When you type `hugo new posts/<filename>`, it'll use the template defined in the `archetypes` to generate a pre-formatted post (similar to what I did before I began typing this). That template contains information on the `front matter`, as well as tells Hugo how the final page will be rendered
* Go through and customize the `config.toml` file with website-wide settings - language localization, date/timestamp formats, website base URL, etc.
* The flow is something like: