---
title: StartSSL & Heartbleed
date: 2014-04-09
---
Until today, I used StartCom's [StartSSL](https://startssl.com/) for all of my domains. While their web interface is terrible, they are probably the only company that offers free certificates. But in reality, they are not really free (anymore).

You surely heard of the [Heartbleed bug](http://heartbleed.com/), which allowed anyone to get access to the private certificate keys, making encryption totally useless. Since I was affected by this as well, there were two things I had to do:

1. Update OpenSSL. By the time I heard about the vulnerability, FreeBSD did already provide an updated port. Easy.
2. Replace all certificates. Turns out that in order to generate a new certificate at StartSSL, the old either has to expire or you have to revoke it - which costs $25.

Considering almost *all* of their customers need a new certificate, their free certificate effectively costs $25 now. I guess a lot of their customers aren't willing to pay this fee and will rather risk leaked keys. That's just irresponsible - if you are not able to offer free certificates that are actually secure, then don't.
