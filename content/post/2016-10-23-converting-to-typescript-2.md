---
title: Converting a project to TypeScript 2.0
date: 2016-10-23
---

If there is one thing the TypeScript people should to improve, it's documentation.
I just went through a small oddysee trying to convert a project to TypeScript
2.0 and the new way of getting definition files. Hint: Look at how the Rust
project does documentation.

### Updating TypeScript

Go into your `package.json`, set TypeScript to version `2.something` and run
`npm install`. Done. Try to compile your project, it probably won't because the
compiler is a bit more strict. The error messages however should give you an
idea what needs to be changed, go and fix your code. Visual Studio Code uses
the globally installed TypeScript version, update that one as well
(`npm install -g` - yes, not update, don't ask why. I make this mistake every.
single. time.).

### Use @types

You can now install type definitions via npm, typings/tsd
are no longer needed. The syntax looks like this:

    npm install --save @types/react

After installing all typings that way, remove the reference path to the old
definitions, try to build and get a trillion errors. First, we have to tell
TypeScript that we're using node modules:

    // tsconfig.json
    {
      "compilerOptions": {
        "moduleResolution": "node"
      }
    }

Now, you might actually be able to compile. Unless you're using any global
non-standard functions like `require` or core-js shims. Remember that you
had to explicitly load typings using the reference path before? This is no
longer necessary, but this also means TypeScript has no idea what typings are
available. When you `import` something, they are loaded automatically, but if
something should always be loaded, you need to configure that:

    // tsconfig.json
    {
      "compilerOptions": {
        "types": [
          "node", "mocha", "core-js"
        ]
      }
    }

Done, now your project should work as usual and with one less tool required.
This wasn't actually hard, was it? It still took me around an hour to figure
out, which could've been prevented by a simple mention of these things in the
release announcement or elsewhere (search the title of this post, there is
zero documentation about this).