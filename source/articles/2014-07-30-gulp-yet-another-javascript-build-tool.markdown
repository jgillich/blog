---
layout: post
title: Gulp, Yet Another JavaScript Build Tool
date: 2014-07-30
---
Grunt is unarguably the most popular build tool for web projects. I like a lot of things about it, but dislike a few others like the configuration syntax and its performance.

So I've finally tried [Gulp](gulpjs.com) and I'm impressed. Porting my Gruntfile was very easy because Gulp is just so simple to use, it took me maybe 30 minutes to port around 80 lines of Grunt configuration, resulting in 60 lines of gulp tasks. And it doesn't just run faster (build time down to 2 seconds from 3 seconds; much more important however is that the watcher reacts faster), it is also more readable.

There are a few differences of course; Gulp doesn't try to be the best tool for everything. Tasks run in parallel by default, which doesn't work very well when one of them wipes the build directory while another one writes into it. Workarounds are [available](https://github.com/OverZealous/run-sequence) though.

Still, if you know how streams in Node work, you almost know how to use Gulp. Grunt on the other hand is a complex beast that does everything you want, but requires you to have some patience while learning and running it.
