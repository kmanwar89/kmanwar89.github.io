---
title: "Website Setup"
description: "Everything is a learning opportunity"
date: 2023-02-24T22:22:08-05:00
draft: false
toc: true
images:
tags:
  - ["web_dev"]
---

## TL;DR

Install Hugo, find a theme you like, customize it, push the repo to GitHub, create a Hugo workflow `.yml` file, set a custom domain, pray to a deity and hope it deploys on the first try.

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

### (Git)ing it onto Github

Now I've got a website locally serving content in near-real time, awesome. I want to get it on the web, which means I can publish it in one of two ways:

1. Use the `hugo` command, which will create a `public` folder containing the static site files OR
2. Use GitHub actions & workflows feature to automatically build the website each time I push a commit to Git.
    * Option 2 has the added benefit of hosting just the base Hugo files on GitHub - the building/serving is handled on the backend with GitHub's runners and infrastructure

Selecting the second option, I went down the journey of figuring out this new style of deploying to GitHub (which wasn't as straightforward as what the docs mention, let me tell ya...)

### GitHub and Actions and Workflows, oh my!

The next thing to figure out was GitHub workflows. Again, I'm not a developer, so I was trying to effectively reverse-engineer it to understand how things are setup. Here's what I uncovered:

* Once the Hugo theme is cloned and customized, and content is created, create a repository on GitHub with `username.github.io` as the repo name. Don't push anything just yet.
* Within this repo, make sure any other Hugo shortcodes used (like embed-pdf or the hugo theme) are included in a file named `.gitmodules`.
  * `.gitmodules` can be modified as such: `git submodule add <URL> <DESTINATION>`
  * Note: Other repos have different workflow files. This one iniitally took ~1.5 minutes to run, and now takes about 30 seconds. I haven't changed anything in my setup, so perhaps it's just the presence of the cache on the GitHub side that makes it faster, I'm not sure
  * I don't fully understand the syntax but it seems to follow similar syntax to a Dockerfile, in that it's telling GitHub what image to use for a container (runner), running the commands and where to source the files to compile (and where to output them).
* From GitHub, select Settings --> Pages --> Source: GitHub Actions.  There should be a spot to select "Hugo" as the flow type, which GitHub will help you create. This will create a new folder within the repo named `.github/workflows` and add a [.yml](https://github.com/kmanwar89/kmanwar89.github.io/blob/master/.github/workflows/hugo.yml) file there. I hyperlinked the one I used for Hugo, which was taken from the Hugo website documentation directly.
* Now, push the changes and watch the magic happen! The flow is basically:

{{<  mermaid >}}
flowchart TD
    
    a[Publish new content]-->b(GH action workflow triggered)-->c[Website is recompiled in near-real time]-->d[Changes published to website]
{{< /mermaid >}}

What's nifty about this is that since Hugo is a static site generator, this workflow allows it to publish the content (in the `public` folder) automatically using GH's backend, processes & services and serve it in near-real time! In addition, I get the added benefit of a version-controlled repository and easy backups/low chance of losing my website's source code if I have an *oopsies* moment.

## Putting it all together

There you have it - a quick & dirty post about the magic behind hosting this website. And if you ask me, it is *most certainly* magical. Here's to many (quick, version-controlled, near-real-time) updates in the future!