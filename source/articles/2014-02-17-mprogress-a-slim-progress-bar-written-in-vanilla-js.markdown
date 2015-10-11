---
title: MProgress, a slim progress bar written in Vanilla JS
date: 2014-02-17
---
[MProgress ](https://github.com/jgillich/mprogress) is a small JavaScript library I've been working on last weekend. If you know [NProgress](https://github.com/rstacruz/nprogress), my project is a much smaller (70 lines vs. 300+ lines) clone that has no dependency on jQuery. To be fair, it also has fewer features and only works on more modern (ES5-compatible) browsers.

I have never done a lot of DOM stuff without jQuery so this has been quite interesting. I had to look up how to do really basic things like inserting an element, but I also realized that there are very few cases where I will ever want to prefer [Vanilla JS](http://vanilla-js.com/) over jQuery. For example, to remove a element from the DOM, you have to call `removeChild` on the parent:

    el.parentNode.removeChild(el);

Now the same code in jQuery:

    $(el).remove()

The latter is not only shorter, it also easier to understand.

Anyway, in this case it made a lot of sense to write vanilla JavaScript: You can load it, show a progress bar and then start loading the huge chunk of minified scripts that your application consists of. Users with a slow (mobile) connection will then see a progress bar that informs them the page is being loaded instead of just a blank page.

To find out more, I've [put a small page together](http://jgillich.github.io/mprogress/) that includes a live example of the library.
