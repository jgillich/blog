---
title: This Week in CloudFM 2
date: 2016-06-06
---

A weekly blog about my progress on CloudFM, a offline-first, multi-backend music player.

I took a break from development because I've been sick and had some other things
going on, but now everything is back on track. Not too much happened since the last
post:

* Enabled multi user support in the app ([#19](https://github.com/cloudfm/cloudfm/issues/19)).
  Redux requires you to initialize the store before actually starting up the app,
  which means you have no idea what user is going to log in - but if there's no
  user, there's no database to load, either. I had to rewrite the PouchDB middleware
  in order to enable this (paired with redux-reset, which alllows one to
  replace the entire Redux store).

* Moved from Travis to CircleCI. It does some things better and parallel builds
  are speed up things a lot, but I don't love it. Using YAML keys for build commands
  - [now that syntax has WTF written all over it](https://github.com/cloudfm/cloudfm/blob/7fcf4943b28a84fb19a7e9f5394a0426c0bac596/circle.yml#L18).

