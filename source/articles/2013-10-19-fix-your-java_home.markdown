---
layout: post
title: Fix your JAVA_HOME
date: 2013-10-19
---
Trying to run one of JetBrain's IDE's (WebStorm in my case), you might get this error message:

	ERROR: Cannot start WebStorm
    No JDK found. Please validate either WEBIDE_JDK, JDK_HOME or JAVA_HOME environment variable points to valid JDK installation.

It is crazy how long it took me to find a solution for this. Most answers on the web are talking about Oracle's proprietary Java; I wanted to run it using OpenJDK instead.

But thanks to [this](http://stackoverflow.com/a/11542973/941764) StackOverflow answer I was able to solve it. A pretty universal way to set $JAVA_HOME is this:

    JAVA_HOME=$(readlink -f /usr/bin/java | sed "s:bin/java::")

On Fedora, $JAVA_HOME would now look like this:

  	$ echo $JAVA_HOME
  	/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.60-2.4.2.7.fc19.x86_64/jre/

But this path will change all the time.
