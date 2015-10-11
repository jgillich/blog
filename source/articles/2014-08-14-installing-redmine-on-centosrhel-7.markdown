---
layout: post
title: How To Install Redmine on CentOS 7
date: 2014-08-14
---
The following describes the process to get the [Redmine](http://www.redmine.org/) project management application running on CentOS (RHEL) 7 with a MariaDB database.

Install dependencies:

    # yum install @development mariadb-server mariadb-devel ruby ruby-devel ImageMagick ImageMagick-devel rubygem-rake rubygem-bundler


Enable and start MariaDB:

    # systemctl enable mariadb
    # systemctl start mariadb


Configure database:

    # mysql
    > CREATE DATABASE redmine CHARACTER SET utf8;
    > CREATE USER 'redmine'@'localhost' IDENTIFIED BY 'my_password';
    > GRANT ALL PRIVILEGES ON redmine.* TO 'redmine'@'localhost';
    > exit


Create user to run redmine under:

    # adduser redmine


Download and extract redmine:

    # curl -O http://www.redmine.org/releases/redmine-2.5.2.tar.gz
    # tar xvf redmine-2.5.2.tar.gz
    # mv redmine-2.5.2/ /home/redmine/
    # mv /home/redmine/redmine-2.5.2 /home/redmine/redmine
    # chown -R redmine:redmine /home/redmine/redmine


Setup redmine:

    # su redmine
    $ cd ~/redmine

    $ cp config/database.yml.example config/database.yml
    $ vi config/database.yml # set user & password for production

    $ bundle install --without development test
    $ rake generate_secret_token
    $ RAILS_ENV=production rake db:migrate
    $ # load default data (optional):
    $ RAILS_ENV=production rake redmine:load_default_data

    $ mkdir -p tmp tmp/pdf public/plugin_assets
    $ chown -R redmine:redmine files log tmp public/plugin_assets
    $ chmod -R 755 files log tmp public/plugin_assets


To start redmine with Ruby's own webserver, run:

    $ ruby script/rails server webrick -e production


You can also add `-p PORTNUMBER` to set a port other than the default (3000).
If you want to access redmine over the network, you have to add a firewall rule:

    # firewall-cmd --add-port=3000/tcp --permanent
    # firewall-cmd --reload
