---
title: Redirecting Ctrl+A with JavaScript
date: 2014-04-21
---
While building a simple paste service, I wanted to catch <kbd>Ctrl</kbd>+<kbd>A</kbd> and redirect it to only select a single element. This does exactly that:

	document.addEventListener('keydown', function (event) {
		if(event.ctrlKey && event.keyCode == 65) {
			var range = document.createRange();
			range.selectNode(document.getElementsByTagName('main')[0]);
			window.getSelection().addRange(range);
			event.preventDefault();
		}
	});

What it does:

* Attach a `keydown` handler to `document` to get all key presses
* Make sure <kbd>Ctrl</kbd>+<kbd>A</kbd> have been pressed (<kbd>A</kbd> is keyCode 65)
* Add a selection to an element (`<main>` in this case)
* Call `preventDefault` to make sure the event is ignored by the browser
