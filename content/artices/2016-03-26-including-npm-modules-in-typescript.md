---
title: Including npm modules in TypeScript
date: 2016-03-26
---

I've written quite a lot JavaScript and Node.js code over the last few years.
Unlike many, I think it's a great language and server platform. However, the
lack of static types does regularly introduce errors that are hard to find.
That's why I decided to use [TypeScript](http://typescriptlang.org/) for a new
project.

The website describes it as a *superset* of JavaScript, with typings being
optional. I was quite surprised to find out this is not true, at all. I started
by installing TypeScript, a webpack loader, and react with npm. Now, we should
be able to do this:

```
import * as React from "react";
```

```
ERROR in ./src/main.ts
(2,24): error TS2307: Cannot find module 'react'.
```

Uh, what? I just installed it, why can't the compiler find it? After doing a bit
of reading (the official docs are severely lacking in that regard, I have to
say), I found out that typings are actually not optional. There are two
solutions:

1. Install the `require` typings and load modules with `require()`. This works
   because the typings for `require` define it as returning `any`, therefore
   disabling all type checks for it.

2. Install the typings for `react`. This is the recommended approach, however
   the typings have to be created by hand, therefore they don't exist for all
   modules.

How do you install typings, you might ask. There is a project with the same
name that provides a package manager for them, similar to npm. Install it
via `npm install -g typings`, then you can install type definitions using
`typings install react --save --ambient`. `--save` stores them in the
`typings.json`, which is like your `package.json` but for typings. `--ambient`
is required for modules that export to the global namespace - I don't yet
know why it's required for `react`, only that it won't work without.

After you've installed them, you need to add one special line to the top of
your code:

```
/// <reference path='../typings/browser.d.ts'/>
```

The path is relative from your source file and tells TypeScript to load
the definitions that you've installed via `typings`. Note that `browser.d.ts`
is for browser projects, if you target Node.js, use `main.d.ts` instead.

Initially, I also had issues with multiple definitions. This is because
TypeScript, by default, loads all `.ts` files. What we usually want is just a
single file that includes the rest of our code. To fix this, we need to create
a `tsconfig.json` in our project root:

```
{
  "files": [
    "src/main.ts"
  ]
}
```

Now, finally, we are able to use React from TypeScript. I feel like there is a
lot that needs to be improved here, starting with the compiler error message.

Now, what if there are no typings for the module you'd like to use? As I
mentioned earlier, you can use `require()` to completely bypass type checks
by installing the typings for it and then doing:

```
import h = require('react-hyperscript-helpers');
```

Unfortunately, this is not possible with ES6 modules, so we don't get the
ability to destructure during import. I think that creating typings is what you
should be doing instead, that's why you're using TypeScript in the first
place, right?

The [Handbook](http://www.typescriptlang.org/docs/handbook/writing-definition-files.html)
tells you how to do it, here's just a quick example, my `react-hyperscript-helpers.d.ts`:

```
declare namespace ReactHyperscriptHelpers {
  function h1(text: string): any;
}

declare module "react-hyperscript-helpers" {
  export = ReactHyperscriptHelpers;
}
```

As you can see, it defines a single function `h1` that takes a `string` and
returns `any`. Now, we can do this:


```
/// <reference path='./react-hyperscript-helpers.d.ts'/>
import { h1 } from "react-hyperscript-helpers";

ReactDOM.render(
  h1('Hello, World!'),
  document.body
);
```

I don't think it ever took me longer to get a Hello World up and running.
Microsoft, please make this stuff easier to get started with.
