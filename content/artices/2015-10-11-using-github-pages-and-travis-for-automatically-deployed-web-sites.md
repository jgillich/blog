---
title: Using GitHub Pages and Travis for automatically deployed web sites
date: 2015-10-11
---

Due to some issues with upgrading my Ghost installation, and considering that I'm
not blogging that much anyway, I've decided to move this blog over to GitHub Pages
using the [Middleman](https://middlemanapp.com/) static site generator.

To write a blog post, all I have to do is to push it to GitHub. Travis then builds
it and pushes it to the `gh-pages` branch, from where GitHub serves it, and thanks
to Cloudflare I even get free SSL. Zero cost, zero maintenance required.

Now, on how to do that. Obviously you need a GitHub repository that contains your
sources. This method works with pretty much anything that can output a static site,
it is not limited to Middleman. Anyway, create your repository and add two files.

One, `.travis.yml`:

    language: ruby
    script: bundle exec middleman build
    sudo: false
    after_success: |
      export PATH=$HOME/.local/bin:$PATH &&
      [ $TRAVIS_BRANCH = master ] &&
      [ $TRAVIS_PULL_REQUEST = false ] &&
      pip install ghp-import --user `whoami` &&
      ghp-import -n build &&
      git push -fq https://${TOKEN}@github.com/${TRAVIS_REPO_SLUG}.git gh-pages
    env:
      global:
        - secure: YOUR_ENCRYPTED_GITHUB_TOKEN

This tells Travis to build the site and uses ghp-import to push the `build` folder
back to GitHub. For authentication, you need a [token from GitHub](https://github.com/settings/tokens),
then encrypt it using the Travis command line client (`gem install travis`) from
within your repository:

    travis encrypt TOKEN=YOUR_GITHUB_TOKEN

One more file you will need is a `CNAME` file. It tells GitHub pages that incoming requests
from your domain belong to that repository. All it contains is your domain, in my case:

    www.jakobgillich.com

Note that this has to be in the root path of your `gh-pages` branch, in the case
of Middleman you would put it under `source/CNAME`. Once you have created these two
files, you can enable Travis for your repository and your site should be automatically
deployed to GitHub Pages whenever you push to your repository.

To set up your domain, go to Cloudflare and add two DNS records to your domain:

    CNAME   @     jgillich.github.io
    CNAME   www   jgillich.github.io

You can also set SSL to Full, Cloudflare will then always communicate with GitHub
over SSL.

At this point, we are pretty much done, but one more thing.
Since we got free SSL from Cloudflare, there is no reason not to use it.
You can force HTTPS by going to Page Rules and checking
*Always use https* for the URL pattern `http://*jakobgillich.com/*`.
