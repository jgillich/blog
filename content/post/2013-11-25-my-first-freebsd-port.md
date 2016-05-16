---
title: My First FreeBSD Port
date: 2013-11-25
---
I've recently created a FreeBSD port for Ghost. Overall, it was a pretty frustrating experience. The biggest issue was that there's just so few resources on the web about it.

Yes, the official documentation is pretty good. But on the other hand, it actually answered very few of my questions. One example: Ports contain a *plist* file that lists all files that belong to it. There is documentation about it and it also has a section about [dynamic plists](http://www.freebsd.org/doc/en_US.ISO8859-1/books/porters-handbook/plist-dynamic.html). It tells you when you can use a dynamic plist, but it's missing any instructions on how you actually implement one.

Since FreeBSD is not as popular as Linux, there are **a lot** less forum/blog posts and mailing list entries. You really can't blame FreeBSD for that, but it makes finding solutions a lot harder. That's also one of the reasons why I'm writing this.

But there is one thing that has helped me a lot: **Reading other ports**. You will gain a lot from that as most ports are written by experienced contributors. And if you struggle too much, there are a lot of helpful people hanging around on the forums and mailing lists.

### Challenges

There were a few challenges I had to face. Once I even fixed a issue by switching to a fresh jail and abandoning the old. So please - if you are a newbie, use a jail so you don't mess up your production system!

#### Extract to Subdirectory

The Ghost source code is a zip file that contains no sub folder. Doesn't sound like a big deal, right? Actually, it is. If the source code extracts directly into `${WRKDIR}`, it is pretty hard moving it into `${STAGEDIR}` without specifying every single file because the latter directory is inside the former.

But this can be solved by using a custom `do-extract`:

	do-extract:
        ${MKDIR} ${WRKSRC}
        ${LOCALBASE}/bin/unzip -d ${WRKSRC} ${DISTDIR}/${DISTFILES}

#### Set correct permissions

After extracting the files, they were set to `100`. Not very useful, but using `@mode 755` in the `plist` fixed it. You can also use `@owner somebody` and `@group somegroup` if you want to.

#### Dynamic plist

Implementing a dynamic plist wasn't that hard. You simply need to specify `PLIST= ${WRKDIR}/plist` and then generate this file in the `pre-install` target like this:

    cd ${WRKSRC}; ${FIND} . -type f | cut -c2- \
        | ${SED} 's|^|www/ghost|' \
        | ${SORT} -r >> ${PLIST}
    ${ECHO_CMD} "@comment directories" >> ${PLIST}
    cd ${WRKSRC}; ${FIND} . -type d | cut -c2- \
        | ${SED} 's|^|@dirrm www/ghost|' \
        | ${SORT} -r >> ${PLIST}

This uses find to get a list of files, corrects the path and appends them to the plist. That's all you have to do.

### Learn from me

You can find the full code of my port in the [Ghost-Config](https://github.com/TryGhost/Ghost-Config/tree/master/freebsd/ghost) repository. Feel free to check it out, study it and send me your improvements.
