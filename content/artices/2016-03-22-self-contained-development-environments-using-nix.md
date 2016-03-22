---
title: Self-contained development environments using Nix
date: 2016-03-22
---

Nix is a package manager that works a bit differently. It allows you to install
any version of any package alongside each other. Since you can't, for example,
have multiple `python` executables, `nix-shell` is used to give you a
environment with all the dependencies you need.

Let's say you have a Rust project. You create a `default.nix` in your project
directory:

```
with import <nixpkgs> { };

rustPlatform.buildRustPackage rec {
  name = "my-project-${version}";
  version = "0.1";
  src = ./.;
  buildInputs = [ openssl pkgconfig ];
  depsSha256 = "160ar8jfzhhrg5rk3rjq3sc5mmrakysynrpr4nfgqkbq952il2zk";
}
```

This defines a Rust package in the current directory, with the `openssl`
dependency. Note that `buildInputs` only lists native dependencies, your
crates are specified in your `Cargo.toml` as usual.

To build a package out of this, you can run `nix-build default.nix`
(or just `nix-build .`). However, this will always build the project from a
clean state, which we don't really want during development. So instead, we do
`nix-shell .`, which puts us in a new shell that not only has `openssl` and
`pkgconfig`, but also all dependencies of `rustPlatform`, like `rustc` and
`cargo`.

Now, what if we need a database? Well, you have to install that through the
usual channels - right? Wrong! This is where things get really interesting: Nix
has packages for pretty much all databases, and nix-shell allows us to run
custom commands when we enter a shell. This property is called `shellHook`:

```
rustPlatform.buildRustPackage rec {
  name = "my-project-${version}";
  // ...
  shellHook = ''
    ${couchdb}/bin/couchdb -a couchdb.ini &
  '';
}
```

This would start a CouchDB database every time we enter our development
environment. And if you're still using Make to run your build commands, consider
specifying them in your `shellHook` instead:

```
shellHook = ''
  function ci {
    cargo build
    cargo test
  }
'';
```

You can of course use Nix on your continuous integration platform, like Travis,
by setting the `script` to:

```
nix-shell default.nix --command ci
```

By using Nix, the environment on Travis is exactly the same as the one you use
locally. No longer will you have issues because Travis hasn't updated their
sqlite version in the last 5 years.
