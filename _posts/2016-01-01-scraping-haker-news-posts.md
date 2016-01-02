---
layout: post
title:  "Scrape Hacker News newest posts with Mechanize"
date:   2016-01-01 01:00:00
last_modified_at:  2016-01-01 01:00:00
excerpt: "Simple post about scraping Hacker News newest posts"
categories: web_scraping
tags:  [ruby, mechanize, scraping]
image:
  feature: hacker-news-posts.png
  topPosition: -100px
bgContrast: darker
bgGradientOpacity: darker
syntaxHighlighter: yes
sitemap:
    priority: 0.7
    changefreq: 'monthly'
    last_modified_at:  2016-01-01 01:00:00
---

First post in 2016 year! I am very happy!!
Today we will scraping newest posts from Hacker News. I think all know what is it.

HN has crazy markup, really. But it's not a problem for us.

<img class="responsive-img" src="{{ site.baseurl_posts_img }}/hacker_news/posts.png" title="Parse Hacker News newest posts" alt="Html markup for Hacker News posts"/>

What we have:
`<table>` and a lot of `tr` with needed data. In `<tr>` with class `athing` we have a title of posts(with site domain) it's red square. After in separate `tr` we have post detail(like: author, points etc) it's blue square.

## Scrape HN posts

Today we will use awesome gem  <a href="https://github.com/sparklemotion/mechanize" rel="nofollow" target="_blank">mechanize</a>

~~~ ruby
gem install mechanize
~~~

Make initial commit(<a href="https://github.com/WScraping/hacker_news/commit/5161dda08b308ea8eb84c843ee963f80e61320cd" rel="nofollow" target="_blank">5161dda</a>) it's not informative, just simple preparing.

Create mechanize instance, I use `user_agent` just for fun.

~~~ ruby
@agent = Mechanize.new do |agent|
  agent.user_agent_alias = 'Mac Safari'
end
~~~

Now we make method for parsing on a page and add `BASE_URL` constant.

~~~ ruby
BASE_URL = 'https://news.ycombinator.com'
~~~

~~~ ruby
def parse_links
  @page = @agent.get "#{BASE_URL}/newest"
  rows = @page.search("//tr[contains(@class,'athing') or "\
                      "contains(.,'point') and "\
                      "not(contains(.,'More'))]")

  rows.each_slice(2) do |row_pair|
    current_data = scrape_header_row(row_pair.first)
    current_data.merge!(scrape_detail_row(row_pair.last))

    break if @data.include?(current_data)
    @data << current_data
  end
end
~~~

Mechanize has an interesting methodology for working with page. After each action
like click on a link or submit form we always have a new page and should work with
changed page.

For opening/getting page mechanize use `#get` method. After in `@page` variable we have instance of `Mechanize::Page`

~~~ ruby
@page = @agent.get "#{BASE_URL}/newest"
~~~

After we should find needed rows. For finding mechamize use `#search` method.

~~~ ruby
rows = @page.search("//tr[contains(@class,'athing') or "\
                    "contains(.,'point') and "\
                    "not(contains(.,'More'))]")
~~~

We have needed data in different rows, I separate rows with different collor.

<img class="responsive-img" src="{{ site.baseurl_posts_img }}/hacker_news/post_numbers.png" title="Hacker News newest posts"/>

How work `#each_slice(2)`, simple example:

~~~ ruby
[1] pry(main)> [1,2,3,4,5,6,7,8,9,10].each_slice(2) do |item|
[1] pry(main)*   p item
[1] pry(main)* end
[1, 2]
[3, 4]
[5, 6]
[7, 8]
[9, 10]
~~~

We scrape first row via `regex`

~~~ ruby
def scrape_header_row(row)
  header = row.text.match(/\d+\.\s+(?<title>.+)\s\(?(?<site>[\w.]+)?\)?/)

  { title: header[:title], site: header[:site] }
end
~~~

Of course, we can use `row.at(....)` for finding title but we use it on the second method. Regex is more fun :)

<img class="responsive-img" src="{{ site.baseurl_posts_img }}/hacker_news/header_regex.png" title="Regex for hacker news" alt="Regex description"/>

For second row we use `#scrape_detail_row` method. Find discuss link, points, author name, post link and make full_url (just for fun).

~~~ ruby
def scrape_detail_row(row)
  discuss_tag = row.at(".//a[contains(.,'discuss') or "\
                        "contains(.,'comments') or "\
                        "contains(.,'comment')]")
  duscuss_link = discuss_tag[:href]

  {
    id: discuss_tag[:href][/\d+$/].to_i,
    point: row.at(".//span[@class='score']").text,
    user_name: row.at(".//a[contains(@href,'user?id=')]").text,
    link: duscuss_link,
    full_url: "#{BASE_URL}/#{duscuss_link}"
  }
end
~~~

Now we have all posts from one page, but we need parse all pages. For it, we must click on `More` link. Make simple method for it.

~~~ ruby
def view_more(pages = 1)
  page_num = 0
  loop do
    yield

    next_link = @page.link_with(text: 'More')
    break if page_num > pages || !next_link

    page_num += 1
    @page = next_link.click
  end
end
~~~

And update `#parse_links` method, add `pages` variable for number of parsed pages. And `limit_id` for limited parsed pages and posts.

~~~ ruby
def parse_links(limit_id = nil, pages = 1)
  @page = @agent.get "#{BASE_URL}/newest"
  view_more(pages) do
    #... The same things
    return @data if limit_id && current_data[:id] < limit_id
  end
end
~~~

It's all, it used very simple:

~~~ ruby
parser Parsers::HackerNews::NewLinks.new
parser.parse_links(nil, 5) # it mean: we parse only 5 pages without needed id
~~~

## Summary

We make a simple parser for **Hacker News**. If you have some question or proposition send pull request <a href="https://github.com/WScraping/hacker_news" rel="nofollow" target="_blank">WScraping/hacker_news</a>
