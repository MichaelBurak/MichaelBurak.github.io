---
layout: post
title:  "CLI Data App - on Solutions"
date:   2017-01-07 16:38:28 +0000
---


It’s quite strange to start from nothing. 

Up until a few days ago, I’d never quite coded anything from a blank page. I’m enrolled in Flatiron School’s Online Full Stack Web Development program, and I knew I’d encounter it eventually. I wasn’t prepared for how hard yet edifying it would be to start from nothing. 

The project looked simple in its description. The key things the app had to do, I’d been prepared for by the cirriculum so far:

1. Scrape data off of a website: that is to say, get information from a website. 
2. Take this data, make objects out of it with attributes. Take a big chunk and make it into things, with attendant qualities. 
3. Design an interface in the command line for the user to interact with these objects. Let them look at a list of objects, and then a deeper, more detailed views. 

I’d done similar things, but putting it all together in coding, I’ve been finding is always more complex than the discrete concepts.

I’ll admit. 

I kinda freaked out. 

I spent a while prepping - here’s a little bit of my prep(https://github.com/MichaelBurak/oo-dry-run/blob/master/lib/scraper.rb). It’s the scraper, and I can look at that code, my comments telling - reminding - myself what did what, and trying to find methods to do what I was looking for. I wasn’t even using the site I ended up using. It was, as the file name notes, a dry run to experiment around. 

I realized first of all that I had no idea how to set up an app. I’d seen apps with environment files, I vaguely knew what they did, but setting mine up took a while. I ended up using a format I’d seen in an example of this project, a CLI gem called World’s Best Restaurants that provided a lot of help(https://github.com/dannyd4315/worlds-best-restaurants-cli-gem). 

I ended up setting up my environment to play around in by encapsulating the files I was working on with a module. Then, some fumbling around with bundler and getting the little gems that would make my program work, and I had a place to experiment. 

And it was hard. It really stressed me out. The process of scraping meant dealing with selectors, and finally I found a great tool in addition to Chrome’s development tools called Xpath Helper (https://chrome.google.com/webstore/detail/xpath-helper/hgimnogjllphhhkhlmebbmlgjoejdpjl?hl=en ), but even there, there was a lot of hunting and pecking for the right ways to get the right data. 

So there, step 1, sort of. I decided to go for it all the way and move past my dry run. 

I’m looking over my work process on GitHub for the more honed app, and you can too, here (https://github.com/MichaelBurak/onbeing-cli-app/commits/master). 

Setting up the framework of the files and so on was easy, but then I ran into a big issue. Step 2. The page I was using was coded in a way that seemed to stymie some of the approaches I had thought of to get the data. I thought ‘well, I’ll just go through the scraped data making objects, iterating, and each time something appropriate to be scraped into an attribute for that object, it would be added.’ This was the plain way of saying my first, and endlessly frustrating approach. I just simply couldn’t find a way to pull it off - I suspect in retrospect that I wasn’t scraping properly. 

Nokogiri creates, at a certain point, node sets. Node sets are scraped data that can be iterated over, but I couldn’t find a way to create a node set that would iterate over so that it would create a bunch of objects with the right qualities. The worst feeling was trying selector after selector to get a new node set, and trying to summon up a litany of objects with corresponding attributes, and getting…one, with all the data that would be discrete attributes assigned to it. It looked, to a relatively untrained eye, that the data I was grabbing, it simply wasn’t structured right to respond to that approach.

I became quite discouraged. And then…I figured it out! I’d talked with some fellow students about my ideas, and I’d thought of an array based approach, but dismissed it basically out of hand. Too kludgy, not ‘pretty’ enough. Eventually, I was sleepless, tossing and turning ideas in my head, and I thought ‘maybe I’ll just try it out in the morning. It’ll probably be bad.’ Whatever bad might have meant - slow, perhaps, or that it would look ugly - I wasn’t very hopeful.

The array based approach meant instead of scraping the page and iterating over it and grabbing attributes and creating objects as I went, I would scrape the page in a few different ways, making arrays of the attributes, and then make a method where it would keep creating objects and assigning the appropriately sequenced part of the right array to the right attribute. 

The breakthrough is here (https://github.com/MichaelBurak/onbeing-cli-app/blob/master/lib/podcast.rb) . To walk through it, there are four arrays set up for the Podcast objects. One contains all the podcast objects, and each time a new one is created, initialized, it goes in there. The other three correspond to three scrapers that link to the main scraping of the page. Each one takes the needed data and puts it in an array. Then, in the self.add_podcasts method, all the arrays are created, and objects are made, plugging in the corresponding part of the attribute arrays as their attribute.

This was the breakthrough, and while I’m still not jubilant about it, it works. The problem that seemed complex met a solution I’d previously dismissed. I was trying to be too smart for myself, I was trying to be fancy and not just make it work in a decent way. I thought I’d come up with, I don’t know, a single line of code that would just poof, magically be elegant and small and fix my problem, instead of going to what I knew on a certain level would work. 

From there, step 3 was downright fun. I had the data, I could make the objects, now I got to design an interface for the users to play around with what I’d grabbed for them? Sounds good by me. I even built in a small loop into the CLI - after examining the specifics of an episode of the podcast, you could go back to grab a list, instead of starting the program all again. It took a bit and was a simple piece of code using something - loops - I hadn’t utilized in a while - but I was rather happy when it worked.

If I learned a key, top thing from this project, it’s the importance of trying out solutions, including ones you might look at and initially dismiss, and just see how they work. Code requires a lot of experimentation, debugging, and so on. There’s nothing wrong with trying out something that doesn’t ‘sound perfect’. In fact, it might be perfect for what it needs to do, the kind of perfect I like.
