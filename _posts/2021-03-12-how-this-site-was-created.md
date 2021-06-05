---
title:  "How this site was created"
date:   2021-03-12 
badges: 
 - type: info
   tag: jekyll
 - type: info
   tag: ssg
tags:
 - github-actions
 - docker
---

# Publishing a site using an SSG from GitHub Actions

After using `Wordpress` for several years (on-off), I decided to move to **SSG** *(Static Site Generator)*
I come to love `markdown`, and the ability to write my posts from `VS Code` directly.

So, here I will just give a high level overwiew of what takes to create a simple site using SSG.

<!--more-->

# Toolset

## docsy-jekyll

After reviewing some static site generators, [jekyll](https://jekyllrb.com/docs/) brought my attention due to its simplicity, and cute `Liquid` scripting language.  
Close came [docusaurus](https://docusaurus.io/) and [nanoc](https://nanoc.ws/)

If you are new to the world of site generators, this could be a good start:  
<https://about.gitlab.com/blog/2016/06/03/ssg-overview-gitlab-pages-part-1-dynamic-x-static/>

[docsy-jekyll](https://github.com/vsoch/docsy-jekyll) is a well done mixture of `jekyll` and `docsy` and nice-looking theme.

To start using `docsy-jekyll`, just go their GitHub repo, click on 'Use this Template'.
Read the [readme](https://github.com/vsoch/docsy-jekyll/blob/master/README.md) file, it comes with useful stuff.
 
## GitHub Actions

The CI/CD job should do 3 main tasks:

1. Checkout code

~~~yaml
steps:    
    - uses: actions/checkout@v2
~~~

2. Build the site

~~~yaml
 - name: Build site with docker-compose
      run: docker-compose up
~~~

Note that default `docker-compose.yaml` comes with the `serve` comand. 
So, change it to only `build`:

~~~yaml
   #command: jekyll serve --no-watch --drafts --incremental
   command: jekyll build  
~~~

3. Publish to your hosting account

~~~yaml
 - name: ftp-action
      uses: sebastianpopp/ftp-action@v2.0.0
      with: 
        host: my-domain.com
        user: <ftp_user>@my-domain.com
        password: <ftp_user_password>
        localDir: ./_site
        remoteDir: .
~~~

> Note: Lately this stop working, so I switched to library: SamKirkland/FTP-Deploy-Action@4.0.0>

## Hosting account

I used my own domain hosting company to hosts the site.  
But, you can use also GitHub Pages to host it.

