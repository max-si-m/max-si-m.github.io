---
layout: post
title:  "Linkedin inviter - improve your social connections via JavaScript"
date:   2015-11-28 01:00:00
last_modified_at:  2015-12-05 01:00:00
excerpt: "How to make automatical inviter for Linkedin on JavaScript. Simple bot for Linkedin"
categories: bots
tags:  [js, authomation, inviting]
image:
  feature: social-networking.jpg
  topPosition: 0px
bgContrast: darker
bgGradientOpacity: darker
syntaxHighlighter: yes
sitemap:
    priority: 0.7
    changefreq: 'monthly'
    last_modified_at:  2015-12-05 01:00:00
---

# Linkedin inviter

Hello, dear users!

<a href="https://www.linkedin.com" target="_blank" rel="nofollow">Linkedin</a> - it's a large social network for IT professionals, with a lot of users. Today we make a very simple script for **automatically** send a connection request(or just add to your network). I don't know who will be read this post, but I oriented on programmers and recruiters who want increase your connections.

But, why it's helpful for you? I found few reasons:

* Social-networking, if you don't know what is it welcome to <a href="https://en.wikipedia.org/wiki/Social_networking_service" target="_blank" rel="nofollow">wikipedia</a>.
* Recruiters - I found work via Linkedin. This social network awesome place for finding new work.
* Interesting posts, yes! You really can find interesting information about new courses, vacancies, books and etc.

Really, If you open this article, I think you have an account on **Linkedin** and you know all features this social network.

## Preparing
Before all actions you need prepare your browser.
If you use Chromium(and all implementations like Google Chrome, Vivaldi and others) you can move to next section. But if you use Mozilla Firefox, I advise for you install awesome extension - <a href="https://addons.mozilla.org/en-US/firefox/addon/firebug/" target="_blank" rel="nofollow">FireBug</a>. It's awesome and more comfortable. After installing you must reload browser and when you will be have this small icon that open awesome features for you.

## Make inviter
The code is a very very simple and has a lot of comments.
If you have any questions you can ask in comments

~~~ javascript
var LinkedinInviter = function(){
  // result value
  var set = {
    count: 0
  };

  // helper function for scrolling
  set.scroll = function() {
    window.scrollTo(0, document.body.scrollHeight);
  }

  set.invite = function() {
    // get all buttons for connect
    var buttons = $(":contains('Connect')").closest("button");
    for(i = 0; i < buttons.length; i++) {
      buttons[i].click(); // and click each
      set.count++;
    }
  }

  // recursion function, linkedin page "People may you know"
  // has infinity scrolling and whe need doing this scroll :)
  set.fetchContacts = function(iter) {
    if(iter > set.counter) {
      set.invite();
      alert("I added: " + set.count + " connections.");
      return;
    }

    set.scroll();
    // set interval between sroling in 1,5 sec.
    setTimeout(function(){ set.fetchContacts(iter + 1); }, 1500);
  }

  set.start = function(counter) {
    current_counter = parseInt(counter)
    set.counter = current_counter > 0 ? current_counter : 50
    set.fetchContacts(0); // start fetching from zero
  }

  return set;
}()
~~~

## Using
This inviter is a very simple, what you must do:

* Open linkedin page <a href="https://www.linkedin.com/people/pymk?trk=hp-rr-pymk" rel="nofollow" target="_blank">"Page may you know"</a>
* Open developer console usually is an F12 button. Or just click right mouse button on the page and choose "Inspect element" or similar and select "Console". With FireBug the same situation.
* Copy and Past inviter code. (Ctrl + C and Ctrl + V or Cmd if you use Mac)
* Run the code

  ~~~ javascript
  // run inviter with 15 scrolling
  LinkedinInviter.start(15)
  ~~~

## The end
>**NOTICE**: Use it not often because you can take a ban :)
