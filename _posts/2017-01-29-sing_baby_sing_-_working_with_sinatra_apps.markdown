---
layout: post
title:  "Sing, baby, sing - working with Sinatra apps"
date:   2017-01-29 01:24:18 +0000
---


I have to admit, taking the Full Stack Web Development course at Flatiron School, I've thought a lot about the structure of the program. Recently, I had the opportunity to make a Sinatra app from scratch, building on a litany of previous lessons, and to appreciate the program structure. 

The project was an organic evolution of things I'd recently and not so recently learned, going all the way back to the beginning - good old basic Ruby methods like iteration. 

When designing the project, I enjoyed engaging with all that I had learned. The project is a CRUD(Create, Read, Update, Delete - named for the things you can do in it) app, meant to keep track of something. I chose podcasts and whether or not they'd been listened to by various users. I'm a podcast fiend, and I've got about a dozen I listen to regularly, though the app on my phone I use to listen to them while commuting around the NYC area(Podbean) is horrible at telling me when they've updated. 

The project tied and ties together various concepts, including: 
-Sinatra, what was described to me by a friend as 'Rails-lite' for Ruby, designed for quick apps.
-Active Record, a core part of what will soon be later Rails work, handling things like associations between objects(like podcasts and their users).
-Working with the intersection of HTML and scripting languages - namely Ruby, and incorporating Ruby code into HTML pages and how a user interacts with them. 
-REST routes, a matter of convention for how webpages are named according to their function.
-Authentication and validation, tracking and controlling what users who are logged in or not can and can't do. 

The app itself is no-frills in presentation, it does what it's meant to do: users can create, read, update, and delete podcasts they're following, including noting if they've listened to them. 

However, my big takeaway is what I've learned leading up to this point allowing me to spin an application out of thin air. To get to Sinatra, I had to learn Active Record, to get to that, ORM - how objects map and interact with each other, and SQL, for managing the database of users. Now, a lot of this ends up abstracted, done for me or made easier, in working with Sinatra. The SQL syntax is replaced by using a small SQL gem and Rake, which handles things like creating tables of users in a simpler way. Yet, since I knew the fundamentals behind what was being made easier, I found the project easier to handle. It's a case of learning the theory helping even when you come to know and practice the practice. That's what I mean by appreciating the program structure. I'm glad I learned all that stuff, even if SQL syntax still isn't something I can spout off the top of my head.

This is really my first web app of my own, and I immediately wanted to share it with other people. Now, a few design choices that I may improve upon prevented me from deploying it onto the web via. Heroku, including the use of SQLite3 for a database. I'm already thinking of ways to improve it that are beyond the scope of the lesson - an important part of the lesson is that only users who made something can edit or delete it, but in a group of users, it would make sense to have a couple different people who like and follow the same podcast, for example. That would be a different sort of relationship between podcasts and users, set up differently in the code of what it means to be a user and what it means to be a podcast, and how they're handled by the app. Of course, I'd like to make it prettier, but I can't undersell the excitement I had when it, well, worked. Thus the whole wanting to show it off to friends by putting it on the web thing. 

When running Sinatra in Shotgun - a Ruby gem that lets you host your app locally and play around with it - I'd often encounter a specific sort of error message. It goes "Sinatra doesn't know this ditty", and it signifies and tells you how to fix when the app user is directed down a path on the webpages that isn't set up. 

Well, that's my code. A ditty. A little song. A small, lightweight application that does what it says on the tin - you signup or login, and this lets you get CRUDding on a list of podcasts you follow. 

Not that it all came easy - the biggest hurdle I faced was in authentication and where a user would be directed to and able to go based on if they were logged in. I tried to focus on user experience, testing out my app in Shotgun, going through the pages, thinking about where a user would go, what they would need links to, and...where they shouldn't be able to go. It makes no sense for a user to be able to edit podcasts someone else has watched, especially if they're not logged in. I had to make choices about where to send them based on this - to the signup page if they weren't logged in, for example. 

But what if they had just logged out? I thought of a user in this predicament at a point in my code where the login page just included the form to log in. They might not want to log in, they might want to sign up for the site, they might've stumbled on a route in trying to visit parts of the site through simply entering routes in their address bar, and not have an account yet. So I added reciprocal login/signup links to the signup/login pages - if you're on one, you can hop to the other. It was a little thing, but I feel like it could spare some frustration and provide a better experience using the app. I put myself in the user's shoes a lot, or tried to, in designing the app, and while it's simple, I like to think it's user-friendly.
