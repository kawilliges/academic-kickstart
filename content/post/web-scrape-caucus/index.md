+++
title = "2020 Iowa Democratic Caucuses results"
subtitle = "A quick and (in)elegant way to get some data"

# Add a summary to display on homepage (optional).
summary = "How one could go about getting the Iowa caucus results for use in further analysis, via some simple web-sleuthing"

date = 2020-02-07T11:16:35+01:00
draft = false

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ["Keith Williges"]

# Is this a featured post? (true/false)
featured = true

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["data scraping", "data cleaning", "election 2020", "R", "data cleaning"]
categories = []

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["deep-learning"]` references
#   `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
# projects = ["internal-project"]

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
[image]
  # Caption (optional)
  caption = ""

  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = ""
+++

## Obtaining Iowa Democratic Party caucus results

### The Iowa Democratic Party (IDP) has made caucus data freely available on their website, but there's no easy way to download the results to analyze, and the website doesn't lend itself to easy scraping. Here I go through a quick-and-dirty approach to getting the results you can see on the site into a format more easily usable by e.g. R or python

As you probably know by now, the IDP has been releasing caucus results over the past few days, and seem to be mostly finished. All hubbub aside, the results are released to their [website](http://results.thecaucuses.org), which seems to be dynamically generated and full of subtotal columns etc., with no easy way to scrape via a normal approach (or it may be my scraping ability is below average). Either way, I have found a *hypothetical* alternative route. I am not a lawyer, and could not find a clear yes/no answer as to if the data was freely available for anyone to use, so proceed at your own risk (See disclaimer at the bottom of this post)

The data has to be contained somewhere, and in this case it's relatively easy to find, when you check the page source (on chrome, right click on the site and choose View Source.) You'll see a wall of text, with a bunch of `<div>` tags etc., but you can see the resemblance between the raw source and resulting page. If you scroll down you can see where the data table is generated; going all the way to the bottom of that, you come to the end of the table, and find the following chunk:

```html
<script defer="" data-next-page="/" src="/_next/static/ZKjDWaVnmeHdtzPm_-zCR/pages/index.js"></script>
```

This looks promising. If you follow that link, you get another wall of indecipherable text, but skimming through eventually you find what seems to be each precinct's data entry, looking something like this:

```js
["Lee",2,947310,"FM5","Fort Madison 5","2020-02-04T21:00:25.739524",1.68,0,0,0,0,0,0,24,25,0.42,0,0,0,30,35,0.7,0,0,0,0,0,0,0,0,0,0,0,0,21,31,0.56,2,0,0,0,0,0,4,0,0,10,0,0],
```

Happily, this looks like something you can use, by rather easily converting into a .csv file:

1. Copy the contents of the index.js page you found, and paste into the text editor of your choice
2. Get rid of all the non-useful text before the individual data entries in the format of the above line. There is also a bit of code after the precinct data, which starts with: `[null]]')},ttDY:function(e,t,n){e.exports=n("+iuc")}`; you don't need anything that comes after this, so you can get rid of all that as well.
3. Now you have the data, but you'll need to format it to import it into the stats language of our choice, but that's also easy. Just find and replace `],` with a newline (`/n`), and then remove any remaining `[` or `]` characters.
4. You have one final problem; no headers. Thankfully, if you dig around in the source file a bit more, you find the following line, hidden away:
```js
Gt=["county","cd","precinct_id","short_precinct_name","long_precinct_name","timestamp","total_precinct_sde","delaney_first_expression","delaney_final_expression","delaney_sde","bennet_first_expression","bennet_final_expression","bennet_sde","biden_first_expression","biden_final_expression","biden_sde","bloomberg_first_expression","bloomberg_final_expression","bloomberg_sde","buttigieg_first_expression","buttigieg_final_expression","buttigieg_sde","gabbard_first_expression","gabbard_final_expression","gabbard_sde","klobuchar_first_expression","klobuchar_final_expression","klobuchar_sde","other_first_expression","other_final_expression","other_sde","patrick_first_expression","patrick_final_expression","patrick_sde","sanders_first_expression","sanders_final_expression","sanders_sde","steyer_first_expression","steyer_final_expression","steyer_sde","uncommitted_first_expression","uncommitted_final_expression","uncommitted_sde","warren_first_expression","warren_final_expression","warren_sde","yang_first_expression","yang_final_expression","yang_sde"]
```
This looks promising. Add it to the top row of your new .csv file (after removing the unnecessary quotes and `Gt=[` etc.), and there you go!

Now, import into your statistics editor of choice, and see what you get. Everything nicely organized and ready for some analysis, as you see fit.

#### Disclaimer: I am unsure of the legality of doing this. It seems reasonable that this is OK to do, especially considering the IDP's emphasis on transparency, but I could not find a Terms of Service for the website, nor could I find answers e.g.  "who can access the data" etc. on the IDP FAQ or elsewhere (their party constitution is not available online at the moment to check there). It should be ok, given that you'd just be converting a table on the website into a machine-readable format, without the use of web-scraping tools (no overloading the servers with repeated queries, just viewing the site's source code). That being said, proceed at your own risk; I'm not a lawyer.
