---
layout: post
title:  "Scrape UpWork skills with Nokogiri"
date:   2015-12-12 01:00:00
last_modified_at:  2015-12-12 01:00:00
excerpt: "How to scrape UpWork's skill with Nokogiri and Selenium"
categories: web_scraping
tags:  [ruby, nokogiri, selenium, scraping]
image:
  feature: up_work_logo.png
  topPosition: -100px
bgContrast: darker
bgGradientOpacity: darker
syntaxHighlighter: yes
sitemap:
    priority: 0.7
    changefreq: 'monthly'
    last_modified_at:  2015-12-12 01:00:00
---

Hello, dear visitors.

Today we will be scraping "programmers" skills from UpWork via Nokogiri and like a small bonus, we will be scraping skills via Selenium. Maybe it will be helpful for someone.

## Scraping Tools
What we need:

* <a href="https://www.ruby-lang.org/en/" rel="nofollow" target="_blank">Ruby</a>, I have awesome article about <a href="http://max-si-m.github.io/install-ruby-in-ubuntu/" target="_blank">installation Ruby environment in Ubuntu</a>
* <a href="http://bundler.io/" rel="nofollow" target="_blank">Bundler</a> for easy gem installing

Gems:

* <a href="https://github.com/sparklemotion/nokogiri" rel="nofollow" target="_blank">Nokogiri</a> gem.
* <a href="https://github.com/vertis/selenium-webdriver" rel="nofollow" target="_blank">Selenium-webdriver</a> gem, just for fun ;)


## Scraping logic

1. Open UpWork's page with <a href="https://www.upwork.com/i/freelancer-skills/" rel="nofollow" target="_blank">skills</a>
<img class="responsive-img" src="{{ site.baseurl_posts_img }}/up_work_skills/up_work_skills_page.png" title="UpWork skills page" alt="Page with UpWork's skills"/>
2. Find all `li` tags with class `skill-item`
<img class="responsive-img" src="{{ site.baseurl_posts_img }}/up_work_skills/up_work_skill_item.png" title="UpWork skill item" alt="Find skill item on skill page"/>
3. From each `li` tag, get `a` tag and get text from this tag (get text from link)
4. Repeat again for all available skill's pages

***Notice:*** We can click on each skill page link (in the top `#A B C D` etc), but I'm very lazy ;)

What I was found, after you click for next page(for example to `B`), your URL change to `https://www.upwork.com/i/freelancer-skills-b/`

if you click to next page(`C`) `https://www.upwork.com/i/freelancer-skills-c/` and for all page, we have the same situation.

We will open just URL with `https://www.upwork.com/i/freelancer-skills-{needed_page}/`

## Scraping with Nokogiri

Before of all, we need to make new "project" like this:

~~~ bash
~/web_scraping/up_work_skills$ tree
.
├── Gemfile
├── Gemfile.lock
├── parser.rb
├── README.md
└── selenium_parser.rb
~~~

Description of files:

* **Gemfile** - is a file we create which is used for describing gem dependencies for Ruby programs.
* **Gemfile.lock** - the file is where Bundler records the exact versions that were installed.
* **parser.rb** - our parser on Nokogiri
* **selenium_parser.rb** - the same parser, but with Selenium
* **README.md** - readme file for <a href="https://github.com/WScraping/up_work_skills" rel="nofollow" target="_blank">github repo </a>

1. First step: add dependencies for our scraper

Edit your `Gemfile`:

~~~ ruby
# gem's source, it can be changed
source 'https://rubygems.org'

# needed gems
gem 'nokogiri'
gem 'selenium-webdriver'
~~~

Save, and run

~~~ bash
bundle install
~~~

2. Let's scrape skills

Open your parser.rb, and let's do magic :)
Include our gems:

~~~ ruby
require 'rubygems'
require 'net/http'
require 'nokogiri'
~~~

### Open skill page

~~~ ruby
 uri = URI("https://www.upwork.com/i/freelancer-skills-#{page}/")
~~~

Create `Nokogiri` page with specific configuration:

  * noblanks - Remove blank nodes, for performance
  * nonet - Substitute entities
  * noerror - Suppress error reports

~~~ ruby
doc = Nokogiri::HTML(Net::HTTP.get(uri)) do |config|
  config.strict.nonet.noblanks.noerror
end
~~~

### Scraping logic, section 2: 

Nokogiri supports two type of finding:

  * CSS - this finding by dom selectors
  * XPath - specific language, we will use it on selenium parser

~~~ ruby
doc.css('.skill-item')
~~~

It return all needed `li` items in `Array`. Use it:

~~~ ruby
doc.css('.skill-item').each do |item|
  skill = item.css('a').text
end
~~~

What do we do? For each `li`, we find `a` selector and use `#text` for it. (`#text` is a Nokogiri method). And, for each interaction in skill variable, we have skill name. Of course, we will make a class for scraping. And skills name we can store in a class instance.

~~~ ruby
doc.css('.skill-item').each do |item|
  skill = item.css('a').text
  @data << skill if skill
end
~~~

Currently, we scrape one page, but we want to scrape all pages from UpWork.

### Let's go to next scraping logic (4)

Make `initialize` method, with param `pages`. What we do? In intialize method we pass array or range of letters, like: `['a','b','f','e','m']` or `('b'..'x')`.
After, store on `@pages` variable.

~~~ ruby
def initialize(pages)
  @pages = pages || ('a'..'z')
  @data = []
end
~~~

And customize our scrape method:

~~~ ruby
def scrape_pages
  @pages.each do |page|
    uri = URI("https://www.upwork.com/i/freelancer-skills-#{page}/")

    # and other things
    # ...
  end
end
~~~

## Scrape skills with Selenium

### What is Selenium?

<a href="http://www.seleniumhq.org/" rel="nofollow" target="_blank">Selenium</a> automates browsers. That's it! What you do with that power is entirely up to you. Primarily, it is for automating web applications for testing purposes but is certainly not limited to just that. Boring web-based administration tasks can (and should!) also be automated as well.

1. Create 'selenium' browser

For parsing we use firefox, but you can use different browser. List of <a href="http://www.seleniumhq.org/docs/01_introducing_selenium.jsp#supported-browsers-and-platforms" rel="nofollow" target="_blank">supported browsers and platforms</a>

~~~ ruby
@browser = Selenium::WebDriver.for :firefox
~~~

Selenium can find element via:

 * Id
 * Class
 * Name
 * Text
 * Tagname
 * Css Selector
 * XPath

But how we can find `li` elements with needed links? `XPath` this is specific request language to XML elements, you can read more information on <a href="https://en.wikipedia.org/wiki/XPath" rel="nofollow" target="_blank">Wikipedia</a>

~~~ ruby
links_xpath = "//*[contains(@class, 'skill-item')]/a"
links = @browser.find_elements(:xpath, links_xpath)
~~~

Description `XPath`:

  * `//` - this mean `descendant-or-self`
  * `*[...]` - anything with additional conditions of fetch
  * `contains(@class, 'skill-item')` - when item contain `class: skill-item`
  * `/a` - for each item we find child-node `a`

In value `links` we have `Array` of links. We need to get a text from each link.

~~~ ruby
links.map { |item| item.text }
# or
links.map(&:text)
~~~

The result is the same, but the second variant more readable.

# Summary

All code you can find on GitHub repository: <a href="https://github.com/WScraping/up_work_skills" rel="nofollow" target="_blank">WScraping/up_work_skills</a>

 The ask  in the comments, mistakes and the website that you want to scrape next.