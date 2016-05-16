---
title: This Week in CloudFM 1
date: 2016-05-10
---

or: How to implement XML parsing in just 500 lines of Rust.

A weekly blog about my progress on CloudFM, a offline-first, multi-backend music player.

Not the best start for a series like this, but last week my SDD died. Then I wasted
an entire evening trying to install OpenSuse Tumbleweed (something something SecureBoot).
Bottom line, I did some stuff, but not even close to what I wanted to achieve.

### What's new

* [hyperdav](https://github.com/jgillich/hyperdav) got all required functionality.
  I'm not particularly proud about the code, especially the response parsing using
  xml-rs is extremely verbose, even though like 90% of the body is ignored anyway.
  Maybe real xml support in serde will happen one day.

* WebDAV indexing is now implemented. This change broke some parts of the app, since
  the URI format has changed to now always include the backend id.

* All components are now dockerized. I want to do some form of automated deployment
  soon-ish. Not because it makes sense right now, but because playing with ops stuff
  is fun.

### What's next

In case my notebook decides to explode tomorrow, let's set the goals a bit lighter:

* Make the web app usable for everyday listening - same as last week
* Implement the UI to add ownCloud/Box.com backends, which will be
  stored as webdav behind the scenes
