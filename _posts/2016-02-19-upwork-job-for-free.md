---
layout: post
title:  "Make UpWork job for fun and free :) Parsing or Web Scraping"
date:   2016-02-19 20:00:00
last_modified_at:  2016-02-19 20:00:000
excerpt: "Yes, a lot of people tell me than I crazy. But it's fun for me and I like it. On this article we make simple parser and don't take any money"
categories: web_scraping
tags:  [upwork, fun]
image:
  feature: upwork-logo.png
  topPosition: -100px
bgContrast: darker
bgGradientOpacity: darker
syntaxHighlighter: yes
sitemap:
    priority: 0.7
    changefreq: 'monthly'
    last_modified_at:  2016-02-19 20:00:00
---

### UpWork

Today I found interesting task in *UpWork* it looks like:

<img class="responsive-img" src="{{ site.baseurl_posts_img }}/up_work_jobs/job_1.jpg" title="UpWork job for free" alt="UpWork job for free"/>
*(<a href='https://www.upwork.com/jobs/_~0138163c73e41f40a1/' rel='nofollow' target='_blank'>link to UpWork</a>)

We has two urls, with search:
<a href='http://goo.gl/sS3aJx' rel='nofollow' target='_blank'>http://goo.gl/sS3aJx</a>. 

and one example:
<a href='http://goo.gl/9vQyVi' rel='nofollow' target='_blank'>
http://goo.gl/9vQyVi</a>. 

<b>Github repo</b>:  <a href='https://github.com/WScraping/lansstyrelsen_parser' target='_blank'>WScraping/lansstyrelsen_parser</a>.

### Strategy

I make few seachs and found that all items start form 1 to a lot of ;)
We have item id and it will be very simple:

~~~ ruby
http://web05.lansstyrelsen.se/stift/StiftWeb/FoundationDetails.aspx?id=<id_number>
~~~

We'll check if we have 100 page with empty `Stiftelsenamn` value than we can suggest it's end of items.(For example, it's changable value)


### Code

Is very simple, I just add limit for pages I don't know how many it's exists

~~~ ruby
@limit = limit || 1_000_000_000
~~~

For my readers all methods is very simple but I want tell about this method:

~~~ ruby
  def scrape_data
    rows = @page.search("//tr[contains(@class,'Item')]")
    data = { }
    rows.each do |row|
      cells = row.search("td")
      data[cells.first.text.downcase.gsub(/\\|:/, '')] = cells.last.text
    end
    data
  end
~~~

We scrape all `tr` when we have class `Item` after in rows variable we have array with cells, first cell is title second value.

In result we have:

~~~ ruby
[{"stiftelsenamn"=>"Ryttmästare John Andréns stiftelse",
  "organisationsnummer"=>"848000-4343",
  "c/o adress"=>"Sparbanken Syd",
  "adress"=>"Box 252",
  "postnummer"=>"271 25",
  "ort"=>"YSTAD",
  "telefonnummer"=>"0411-82 20 00",
  "fax"=>"0411-191 66",
  "e-post"=>"\t\t\t\t\t\t\t\t\t\t",
  "webbsida"=>"\t\t\t\t\t\t\t\t\t\t",
  "kontaktperson"=>"",
  "stiftelsetyp"=>"\t\t\t\t\tAnnan stiftelse\t\t\t\t\t",
  "registreringsmyndighet"=>"Skåne län",
  "län"=>"Skåne län",
  "säte"=>"Ystad",
  "Ändamål"=>
   "Stiftelsens ändamål är att med den i min ägo befintliga boksamlingen såsom basis uppbygga för Ystads Stadsbibliotek vissa specialsamlingar, vilka genom tiderna skola kompletteras och fullständigas med hjälp av stiftelsens avkastning, och vilkas existens skola med tiden bliva en ackvisition icke blott för biblioteket självt utan även för staden Ystad. Specialsamlingarna skola utgöras av följande fyra; den engelske författaren Oscar Wilde, den engelske tecknaren Aubrey Beardslev, den engelske arkeologen T E Lawrence och den ryske dansören Vaslav Nijinsky. Huvudändamålet med min stiftelse skall vara att uppbygga en komplett Oscar Wilde-samling, som med tiden bör bliva unik i Sverige, kanske även Europa; det är härvidlag fullständighetssynpunkten, ingalunda värdesynpunkten, som är avgörande. Alla mina böcker ska förvaras i Ystads Stadsbibliotek i särskilt låsbara skåp med glasdörrar för att hindra damm och annan skada. Skåpen ska vara av enkel men konstnärlig modell och tillverkas av i Ystad bosatt skicklig möbelsnickare eller hantverkare samt förses med inskriptionen Tillhör Ryttmästare John Andréns stiftelse. Inköp till min boksamling ska göras av en inköpsnämnd bestående av tre medlemmar. En gång varje år helst i augusti månad med kräfttid och fullmåne ska inköpsnämnden på Ystads Saltsjöbad eller Hotell Continental gemensamt på stiftelsens bekostnad aväta \"A splendid dinner with delightful dishes\" varvid bör drickas en flaska gammalt portvin (Vintage) av det bästa märke, som för pengar kan erhållas i Ystad.",
  "År"=>"2014",
  "tillgångar"=>"15.773.687 kr"},
 {"stiftelsenamn"=>"Stiftelsen Hembygdsfonden",
  "organisationsnummer"=>"849200-8043",
  "c/o adress"=>"Bertil Gustafsson",
  "adress"=>"Tommared 11, Södergård",
  "postnummer"=>"310 20",
  "ort"=>"KNÄRED",
  "telefonnummer"=>"0430-43085",
  "fax"=>"",
  "e-post"=>"\t\t\t\t\t\t\t\t\t\t",
  "webbsida"=>"\t\t\t\t\t\t\t\t\t\t",
  "kontaktperson"=>"Bertil Gustafsson",
  "stiftelsetyp"=>"\t\t\t\t\tAnnan stiftelse\t\t\t\t\t",
  "registreringsmyndighet"=>"Västra Götalands län",
  "län"=>"Hallands län",
  "säte"=>"Laholm",
  "Ändamål"=>
   "Utdelas till den eller de bondsöner, som under föregående år övertagit fädernegården efter sin fader och förbinder sig att icke sälja den under de närmaste tjugo åren. Om någon bondson ej finnes för året som uppfyllt villkoren, kan en gift bonddotter inträda istället, när hon uppfyller kraven.",
  "År"=>"2014",
  "tillgångar"=>"3.532.940 kr"}]
~~~

I think it's pretty nice :) Good luck.


<b>Github repo</b>:  <a href='https://github.com/WScraping/lansstyrelsen_parser' target='_blank'>WScraping/lansstyrelsen_parser</a>