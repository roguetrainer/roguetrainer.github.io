---
published: false
title: Test
layout: post
---
@{
  Layout = "post";
  Title = "The Gamma: Simple code behind interactive articles";
  Tags = "type providers, data journalism, programming languages";
  Date = "2015-09-28T11:07:08.0598479-05:00";
  Description = "Computer programming may not be the new literacy, but it is finding its way into" +
    "many areas of modern society. In particular, data journalism turns traditional news reports " +
    "from a mix of text and images into something that is much closer to a computer program." +
    "By treating articles as programs, we can make data journalism more transparent, reproducible " +
    "and interactive. This is what I've been working on recently, so check out the prototype!";
  HasLargeImage = true;
  Image = "http://tomasp.net/blog/2015/thegamma/chart.png";
}

The Gamma: Simple code behind interactive articles
=================================================

<img src="http://tomasp.net/blog/2015/thegamma/dj.png" style="float:right;width:300px"
  title="Illustration from 'The Data Journalism Handbook'" />

Links ([data.gov](http://data.gov) or 
[data.gov.uk](http://data.gov.uk)). But raw data does not tell you much.




> <img src="http://tomasp.net/blog/2015/thegamma/sl.png" style="float:right;width:150px" />
>
> I did a talk about The Gamma project at the fantastic [Future Programming workshop](http://www.future-programming.org/programSL.html)
> at the [StrangeLoop conference](http://www.thestrangeloop.com/) last week (thanks for inviting me!)
> and there is a [recording of my 40 minute talk on YouTube](https://www.youtube.com/watch?v=cYoO2RvZn7Y&feature=youtu.be&a),
> so if you prefer to watch videos, check it out!

Are you a data journalist or data analyst? We're looking for early partners!
I joined the <a href="http://www.joinef.com" title="Nothing to do with Entity Framework, don't worry!">EF
programme</a> to work on this and if the project sounds like something you'd like to see happen,
<a href="mailto:tomas@tomasp.net">please get in touch</a> or share your contact details
on [The Gamma page](http://thegamma.net)!

SubSection
------------------------------

Bullets

 * **Reproducibility** - The report should be fully reporoducible. Serious newspapers cite their
   data sources, but reproducing report using just a citation involves a lot of manual work. 
   It should be possible to reproduce the results and _verify their correctness_ just by rerunning
   the code behind the report.
 
 * **Transparency** - Reproducibility is a good start, but we should also be able to change the
   report easily. I want to be able to change parameters and see how that affects the result. Does
   it still support the story? I want to see that the report is _not misleading_,  intentionally 
   (or unintentionally).

 * **Interactivity** - Finally, I think we should also enable novel user experience with reports.
   Newspaper are no longer (just) printed on paper where we need text and images. We should use
   the reproducibility and transparency behind reports to allow new user experiences. The reader
   should be able to explore the data and do some of the changes without being a programmer.



<div style="text-align:center;">
<img src="atom.png" style="max-width:500px;margin-left:auto;margin-right:auto;" />
</div>

The type provider imports all data sources from [the World Bank](http://data.worldbank.org) directly
into the type system. You type "." and see all the countries, then you type "." again and you can
choose one from the thousands of available indicators.
 
F# type providers are fantastic if you are programmer, but I think they also illustrate the kind
of experience that we could build for anyone who is working with data. Typing "." in the editor is
not _that different_ from clicking the "Search" button in Google and choosing an option in the
Atom auto-complete is not _that different_ from choosing an option in a drop-down on a web page.

Try The Gamma prototype
----------------------

I would not be writing this blog post if I didn't have anything to show! I put together a prototype
that shows some of the ideas. It is very basic, but it demonstrates the experience that I believe
all visualizations on the web should have in the future. The example uses the [World Bank data](http://data.worldbank.org)
and compares countries in the world based on their CO2 emissions:

> This is very much work in progress. I focused on building a demo that illustrates the 
> idea, but there are certainly issues. You can [report them on GitHub](https://github.com/tpetricek/TheGamma)
> and if the whole demo is down, ping me at [@@tomaspetricek](http://twitter.com/tomaspetricek)!

<div style="position:absolute;width:100%;z-index:-100">
<div style="position:relative;left:100%;width:260px;margin-top:25px;display:none;padding:10px;border:1px solid #d0d0d0;background:#f0f0f0" id="carbon-side">
</div>
</div>
<iframe src="http://thegamma.net/carbon-plain?iframe=true" style="overflow:hidden;height:300px;width:100%;border-style:none;margin:0px" seamless="seamless" id="carbon">
</iframe>
<style type="text/css">
 #carbon-side h2 { margin-top:0px; font-size:15pt; }
 #carbon-side p { font-size:9pt; }
 #carbon-side h2, #carbon-side p { color:#404040; }
</style>
<script type="text/javascript">
  var eventMethod = window.addEventListener ? "addEventListener" : "attachEvent";
  var messageEvent = eventMethod == "attachEvent" ? "onmessage" : "message";
  window[eventMethod](messageEvent, function(e) {
    if (e.data.action == "showtip") 
      document.getElementById('carbon-side').style.display = e.data.visible?'block':'none';
    if (e.data.action == "tip") 
      document.getElementById('carbon-side').innerHTML = e.data.html;
    if (e.data.action == "resize") 
      document.getElementById('carbon').style.height = (e.data.height + 0) + 'px';
  }, false);
</script>



The third example on the page (pie chart) is a bit more interesting, because it implements a simple
pre-processing. It takes top 6 countries and shows them together with all other countries. This is
an aspect that cannot be changed in "options", but you can see it in the "source" view:

    let sumRest =
      countries.skip(6).sum()
      
    let topAndRest =
      countries.take(6)
        .append("Other countries", sumRest)
        
    chart.pie(topAndRest).show()
    
I believe this kind of transformation is easy enough and with good tooling, we can make it 
accessible not just for programmers, but even for technically skilled journalists or anyone
who works on data analytics. Feel free to experiment with the prototype, though keep in mind that
the library is very much incomplete.

Article is a literate program
-----------------------------

The key idea that I think can change how data reporting is done is to treat _articles as programs_.
In the prototype, the source code for the CO2 report is simply a [Markdown document on 
GitHub](https://github.com/tpetricek/TheGamma/blob/master/web/demos/carbon.md).

Everything else is generated from the source. When you open the report in a web browser, you 
see a rendering that shows the text with the resulting charts (the code is executed). The more
interesting thing is that when you click on "options", The Gamma looks for specific patterns in the
source code and generates editors for them. The current implementation looks for two patterns.

 1. When there is a choice from one of several members, for example years in 
    `world.byYear.2010`, we generate drop-down with other members.
 2. When the code creates a list, for example `[ countries.USA; countries.UK ]` in the
    last example in the CO2 report, we generate a select element.

This is just a very basic thing that can be done when we have the full source code and there are
many other interesting features I'd like to add. If you see a number in text, say the GDP of USA
is 16.77 trillion USD, this does not tell you much - but if we know the source of the number and
how it is computed, we can automatically provide the context. What about other countries? And how 
has it been changing?  

Summary
-------

Saying that programming is the new literacy would be a (perhaps quite silly) overstatement, but
I do think that understanding information around us is becoming increasingly important. This 
applies to public information (e.g. open government data) but also to business data inside 
companies.

Journalists and data analysts help us by finding interesting information in data and presenting
them to us. But how can we make sure that the analysis is done correctly and does not show 
misleading information? And how can we build on top of it to explore the information further
and get better understanding?

This is exactly what I'm trying to do with The Gamma project and I would like to hear from anyone
interested! Get in touch at [tomas@tomasp.net](mailto:tomas@tomasp.net) or [@@tomaspetricek](http://twitter.com/tomaspetricek).
