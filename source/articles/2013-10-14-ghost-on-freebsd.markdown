---
layout: post
title: Ghost on FreeBSD
date: 2013-10-14
---
[Ghost](http://ghost.org) has been released to the public today. Finally! I could not try it earlier because I wasn't one of the Kickstarter backers. When I discovered the project, it was already way over their initial funding goal so I saw no reason to fund it.

If you never heard of it: It is a simple blogging software. Similar to Wordpress, but it looks better, has fewer features and it's written in Node.js instead of PHP.

After trying it out locally, I installed it in a jail on my FreeNAS box. The installation is not completely straightforward, here is what you have to do:

#### 1. Install the necessary dependencies

Here is a list of all dependencies that you need:

 * www/node
 * www/npm
 * databases/sqlite3

Install them from the ports collection or the package manager of your choice.

Ghost requires the node-sqlite3 package, and that one caused me some problems. To build and install it properly, we will need the node-gyp package from npm:

	npm install node-gyp -g

#### 2. Get Ghost and install more dependencies
Now we have to download and extract Ghost. Go to the [download page](http://ghost.org/download/) and copy the latest download link, I will not keep the one below up to date.

    fetch http://ghost.org/zip/ghost-0.3.2.zip
    mkdir ghost
    unzip -d ghost ghost-0.3.2.zip
    cd ghost


This step is different from the official instructions. We have to explicitly pass the path of our sqlite3 installation to `npm install` (all third-party software is installed to /usr/local by default on FreeBSD).

	npm install --production --sqlite=/usr/local

#### 3. Adjust Configuration

We are almost done! Before running it, you have to create and open the `config.js` with your favorite text editor.

    cp config.example.js config.js
    ee config.js

Then find the `production` section and set these properties to whatever you want:

* url
* host
* port

`url` is used for all frontend links, very useful if you run Ghost through a reverse proxy.

Now we are ready to run it using the `npm start` command from within the Ghost directory. If you plan to run it in production mode, execute `setenv NODE_ENV production` first.

#### 4. Gain Control

Forever is a nice tool that keeps your node processes running, well, forever. It's like the `service` command for Node.js. Thanks to npm, installation is easy:

	npm install -g forever

To manage the process, I wrote a little service script. Put this at `/usr/local/etc/rc.d/ghost` and make it executeable:

<script src="https://gist.github.com/jgillich/7045531.js"></script>

Before the above script will work, you also have to do this:

* Add the `ghost` user
* Give the user write access to the database: `chown -R ghost content/data/`
* Same for the logfile: `chmod 777 /var/log/ghost`
* Install security/sudo

Now you have a service that integrates very well and in a secure way with the rest of the system:

* Autostart on boot: Add `ghost_enable="YES"` to your `/etc/rc.conf`
* Start manually: `service ghost [one]start`
* Stop manually: `service ghost [one]stop`
* Logs are stored at `/var/log/ghost`

I will probably create a port that will automate the whole process soon (just need to learn how to do it first). And please note that the one above is the first service script I ever wrote, it may break your system! If you know how to improve it, please leave a comment.
