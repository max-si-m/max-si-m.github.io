---
layout: post
title:  "Install Ruby environment in Ubuntu"
date:   2015-12-06 14:00:00
last_modified_at:  2015-12-06 14:00:00
excerpt: "Simple instruction for installing Ruby environment in Ubuntu. We also install Ruby On Rails and PostgreSQL"
categories: programming
tags:  [ruby, ubuntu]
image:
  feature: ruby-logo.png
  topPosition: 0px
bgContrast: darker
bgGradientOpacity: darker
syntaxHighlighter: yes
sitemap:
    priority: 0.7
    changefreq: 'monthly'
    last_modified_at:  2015-12-06 14:00:00
---

Hello, dear users.
Now we will install ruby environment on our Ubuntu OS.

# Install Ruby

1. Before of all we need prepare our system

~~~ bash
sudo apt-get update
sudo apt-get install git-core curl libcurl4-openssl-dev libxslt1-dev zlib1g-dev  build-essential libssl-dev libreadline-dev  sqlite3 libxml2-dev   python-software-properties libsqlite3-dev
~~~

2. Install via <a href="https://github.com/sstephenson/rbenv" target="_blank" rel="nofollow">rbenv</a>

~~~ bash
cd ~/
git clone git://github.com/sstephenson/rbenv.git .rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
exec $SHELL
~~~

Currently, we installed tools for using multiple version of ruby. Next step, we will install **Ruby**.

3. Install Ruby

It can take some time, you can make tea or take beer (I advise tea :))

~~~ bash
git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
exec $SHELL
~~~

3.1 Installing Ruby in your system

Drink tea, again ;)

~~~ bash
rbenv install 2.2.2
rbenv global 2.2.2
~~~

Check installed version:

~~~ bash
ruby -v
~~~

And finally, we don't need documentation on our system, we always can find it on github :)

~~~bash
echo "gem: --no-ri --no-rdoc" > ~/.gemrc
~~~

The major reason why you need to install ruby is a Ruby On Rails framework.

# Install Ruby On Rails
Installing RoR is very simple, only few steps.

1. Install nodejs

~~~ bash
sudo apt-get install nodejs
~~~

2. Install RoR

~~~ bash
gem install rails
rbenv rehash
rails -v
~~~

3. Install bundle

~~~ bash
gem install bundler
~~~

Test, installing RoR:

~~~ bash
rails new test_app_name
cd new_app
rails server
~~~

After last command we run web server (and have output, like this). Now we can show our app in action, just go to page <a href="http://localhost:3000/" target="_blank" rel="nofollow">http://localhost:3000</a>

~~~ bash
max~/app$ rails server
=> Booting WEBrick
=> Rails 4.2.4 application starting in development on http://localhost:3000
=> Run `rails server -h` for more startup options
=> Ctrl-C to shutdown server
[2015-12-06 15:17:01] INFO  WEBrick 1.3.1
[2015-12-06 15:17:01] INFO  ruby 2.2.2 (2015-04-13) [x86_64-linux]
[2015-12-06 15:17:01] INFO  WEBrick::HTTPServer#start: pid=2586 port=3000
~~~

You must get page like this:
<img class="responsive-img" src="{{ site.baseurl_posts_img }}empty-rails-app.png" title="Empty RoR aplication" alt="Show how worked Ruby on Rails aplication"/>

# Install PostgreSQL

~~~ bash
sudo apt-get install postgresql libpq-dev
sudo su postgres -c psql
postgres=# CREATE ROLE <user_name> SUPERUSER LOGIN;
postgres=# \q
~~~

## Bonus
For fast installing all things in "fresh" OS, I recomend this script <a href="https://github.com/magic2k/ubuntu_laptop" target="_blank" rel="nofollow">ubuntu_laptop.sh</a>