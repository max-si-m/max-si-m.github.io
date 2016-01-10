---
layout: post
title:  "Scrape jobs from Hacker News with Mechanize"
date:   2016-01-06 01:00:00
last_modified_at:  2016-01-06 01:00:00
excerpt: "Simple post about scraping jobs from Hacker News via Mechanize gem"
categories: web_scraping
tags:  [ruby, mechanize, scraping]
image:
  feature: hacker-news-jobs.jpg
  topPosition: -100px
bgContrast: darker
bgGradientOpacity: darker
syntaxHighlighter: yes
sitemap:
    priority: 0.7
    changefreq: 'monthly'
    last_modified_at:  2016-01-06 01:00:00
---

Today we will continue parse Hacker News in this article we will parse **Jobs**.
If you don't know what we will doing read acticle about <a href='http://max-si-m.github.io/scraping-haker-news-posts/' target="_blank">scraping Hacker News newest posts with Mechanize</a>

## Scrape jobs

Before all of doing some actions we change <a href ='https://github.com/WScraping/hacker_news/blob/master/new_links_parser.rb' target="_blank">new_links_parser.rb</a>, make base class with helper methods. This class is very simple and small.

~~~ ruby
require 'rubygems'
require 'mechanize'

module Parsers
  module HackerNews
    class BaseParser
      BASE_URL = 'https://news.ycombinator.com'
      attr_accessor :agent, :data

      def initialize
        @agent = Mechanize.new do |agent|
          agent.user_agent_alias = 'Mac Safari'
        end
        @data = []
      end

      # Method for pagination, just clicking on More link
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
    end
  end
end
~~~

Jobs  parser is more simpler than parser new posts.

~~~ ruby
require_relative 'base_parser'

module Parsers
  module HackerNews
    class Jobs < BaseParser

      # Runs a parsing process
      # Params:
      # +limit_url+:: sring url value for end url address(may be nil)
      # +pages+:: number of pages for parsing (default: 1)
      def parse_links(limit_url = nil, pages = 1)
        @page = @agent.get "#{BASE_URL}/jobs"
        view_more(pages) do
          links = @page.search("//td[contains(@class,'title') and "\
                              "not(contains(.,'More'))]/a")

          links.each do |link|
            current_data = scrape_link_data(link)
            return @data if limit_url && current_data[:link] == limit_url

            break if @data.include?(current_data)
            @data << current_data
          end
        end
      end

      # Scrape data from link
      def scrape_link_data(link)
        { title: link.text, link: link[:href] }
      end
    end
  end
end
~~~

Result data:

~~~ ruby
[{:title=>"Flexport is hiring software engineers",
  :link=>"https://angel.co/flexport/jobs"},
 {:title=>
   "Streak hiring Senior Engineers to build large scale index of all business email",
  :link=>"https://www.streak.com/careers#SeniorBackend"},
 {:title=>
   "Automatic (YC S11) Is Hiring a Car-Loving Principal Server Engineer",
  :link=>"https://boards.greenhouse.io/automatic/jobs/63773#.VpAV-5MrKRs"},
 {:title=>"Zidisha (YC W14) spring microfinance internships (remote)",
  :link=>"https://www.zidisha.org/volunteer"},
 {:title=>"Airware (YC W13) is crunching drone data in SF",
  :link=>"item?id=10865630"},
 {:title=>
   "Benchling (YC S12) is hiring engineers to build the backbone of biotech",
  :link=>
   "https://jobs.lever.co/benchling/f916b4d9-59ea-4346-a4a0-daa18bf46fa4?lever-source=Hacker%20News"},
 #...........
 ]
~~~

If you have some proposition, ask in comments. And send Pull Request to repository: <a href='https://github.com/WScraping/hacker_news' target="_blank">WScraping/hacker_news</a>