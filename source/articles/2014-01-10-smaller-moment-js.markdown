---
layout: post
title: Smaller Moment.js
date: 2014-01-10
---
[Moment.js](http://momentjs.com/) is a great JavaScript library that makes working with time a lot easier. I totally love it, but there is one problem: It is huge! That is because `moment-with-langs.js` includes all supported languages, and they support [a lot of them](https://github.com/moment/moment/tree/develop/lang)!

Now the good news is that you can build a custom version of Moment.js with just the languages you need. To do this, you need Git, [Node.js](http://nodejs.org/) and [Grunt](http://gruntjs.com/) - but you have these already, right?

Let's start by fetching the repository and installing dependencies:
```
git clone https://github.com/moment/moment.git
cd moment
git checkout master
npm install
```
Now simply run the below command with a comma seperated list of all languages you need (German and French in this example) - all languages in the `lang/` folder can be embedded.
```
grunt release --embed_languages=de,fr
```
Note that english is always included and there is not way to change that. When the command finishes, you'll find your custom version at `min/moment-with-customlangs.js`.

The resulting file size is around 70 KB with a single language - much better than the default 270 KB.
