---
layout: post
title:  "Find user by IP or simple bot for Telegram with Ruby"
date:   2016-01-16 01:00:00
last_modified_at:  2016-01-16 01:00:00
excerpt: "Simple post about making Telegram bot with Ruby for finding user by IP."
categories: bots
tags:  [ruby, telegram, gems]
image:
  feature: telegram.png
  topPosition: -100px
bgContrast: darker
bgGradientOpacity: darker
syntaxHighlighter: yes
sitemap:
    priority: 0.7
    changefreq: 'monthly'
    last_modified_at:  2016-01-16 01:00:00
---

Hello, today we make simple **Telegram** bot on Ruby. We'll find user (or sites) by IP address and will use `ip_info` gem. Detail information about using gem you can read in <a
href='https://github.com/max-si-m/ip_info' target='_blank'>github page</a>

## Create Telegram bot

Before of all you must create new telegram bot. It's very simple, you
can read more about it <a href='https://core.telegram.org/bots' rel='nofollow' higth="300px" target='_blank'>in official documentation</a>. 

Simple instruction:

* Start dialog with @BotFather
* Use `/newbot` for creating new bot
* Choose name and login for bot
* Get API KEY for further actions

### Create and configure new bot

Write to `@BotFather`

<img class="responsive-img" src="{{ site.baseurl_posts_img }}/telegram/create_bot.png" title="Create new bot on Telegram" alt="New bot for Telegram"/>

I choose name `IpInfoBot`, but now we must choose login name.

<img class="responsive-img" src="{{ site.baseurl_posts_img }}/telegram/choose_name_for_bot.png" title="Choose name for Telegram bot" alt="Set name for Telegram bot"/>

After of all changes we have our API KEY and working bot.
<img class="responsive-img" src="{{ site.baseurl_posts_img }}/telegram/success_created_bot.png" title="Working Telegram bot" alt="Success created Telegram bot"/>

For our bot we make simple `Gemfile` and we'll use `Telegram-bot-ruby`
it's pretty awesome.

~~~ ruby
source 'https://rubygems.org'

gem 'telegram-bot-ruby'
gem 'ip_info'
~~~

Simple usage of this gem:

~~~ ruby
require 'rubygems'
require 'telegram/bot'
require 'ip_info'

token = '152628097:AAH8T0o6Aq9bKQCkS_fI3-36Gl5-XUADrOs'

Telegram::Bot::Client.run(token) do |bot|
  bot.listen do |message|
    case message.text
    when '/start'
      bot.api.send_message(chat_id: message.chat.id,
                          text: "Hello, #{message.from.first_name}")
    end
  end
end
~~~

If we'll send `/start` message for our bot we have response: `Hello, <your_name>`

> Notice: Always use `message.chat.id` for sending message, because your bot can used in group dialogs

I want make comands like `/city <ip_address>`. But I relly don't know how to get `<ip_address>` from message in right way.
For getting address form message we'll use regex. Make simple method: 

~~~ ruby
def get_address_form(message)
  message[/([\d.]+|(?:https?:\/\/)?(?:[\w]+\.)?[\w]+\.[\w]+)/]
end
~~~

But we have `/cite 000.000.000.000` message. Change our 'engine' of our bot:

~~~ ruby
token = '152628097:AAH8T0o6Aq9bKQCkS_fI3-36Gl5-XUADrOs'
@ip_info = IpInfo::API.new(api_key: ENV["IP_INFO_KEY"])

Telegram::Bot::Client.run(token) do |bot|
  bot.listen do |message|
    case message.text
    when '/start'
      bot.api.send_message(chat_id: message.chat.id,
                          text: "Hello, #{message.from.first_name}")
    when /\/city [\w\W]+/
      bot.api.send_message(chat_id: message.chat.id,
                          text: get_info(get_address_form(message.text), 'city') )
    when /\/country [\w\W]+/
      bot.api.send_message(chat_id: message.chat.id,
                          text: get_info(get_address_form(message.text), 'country') )
    end
  end
end
~~~

and make simple method for sending request to `ipifodb` site: 

~~~ ruby
def get_info(address, type)
  return 'Error: Address is invalid' unless address

  data = ''
  @ip_info.lookup(address, type: type).map do |e|
    data << e.first.to_s.gsub(/_/, ' ').capitalize + ': ' + e.last + "\n"
  end
  data
end
~~~

We convert result of using `ip_info` gem, because we have result:

~~~ ruby
> max-si-m.github.io git:(master)  irb
irb(main):001:0> require 'ip_info'
=> true
irb(main):002:0> ip_info = IpInfo::API.new(api_key: ENV['IP_INFO_KEY'])
=> #<IpInfo::API:0x007ff2ebc16250
@api_key="3be331437ac07643ed662ec1fad76ed57d125be3184204864f8d3029f5b8aaa2">
irb(main):003:0> ip_info.lookup('11.34.66.78', type: 'city', time_zone:
true)
=> {:status_code=>"OK", :status_message=>"", :ip_address=>"11.34.66.78",
:country_code=>"US", :country_name=>"United States",
:region_name=>"Ohio", :city_name=>"Columbus", :zip_code=>"43218",
:latitude=>"39.9664", :longitude=>"-83.0128", :time_zone=>"-05:00"}
~~~

Set comands for our bots:
<img class="responsive-img" src="{{ site.baseurl_posts_img }}/telegram/set_comands.png" title="Set comands for Telegram bot" alt="Add comands for Telegram bot"/>

And now we have fully working bot. Try in work our bot:

<b>Find user by IP</b>:
<img class="responsive-img" src="{{ site.baseurl_posts_img }}/telegram/get_user_by_ip.png" title="Find user by IP in Telegram bot" alt="Telegram bot can find user by IP"/>

Find info about site:
<img class="responsive-img" src="{{ site.baseurl_posts_img }}/telegram/get_info_about_site.png" title="Find information about site in Telegram bot" alt="Telegram bot can find information about site"/>

Find country of site:
<img class="responsive-img" src="{{ site.baseurl_posts_img }}/telegram/get_country_of_site.png" title="Find information about site in Telegram bot" alt="Telegram bot can find information about site"/>


## Summary

We make simple bot for **Telegram**, it's not hard but interesting. If you will try bot and it's not available, maybe I was delete it :)
If you have another way to get `address` from message send pull request or send message. 

Github Repo <a href='https://github.com/WScraping/ip_info_bot'>WScraping/ip_info_bot</a>

Telegram bot: **@ip_info_bot**

