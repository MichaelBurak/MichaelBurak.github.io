---
layout: post
title:  "Getting dirty with ActiveModel"
date:   2017-06-18 10:43:55 -0400
---


Something very important to any Rails application is one's models. The ways they associate, methods that belong to them, how they communicate in the great circle of life that is the request cycle of the MVC framework of Rails...and their attributes. 

I've been working on a Rails app designed to model making characters for a role-playing game and leveling them up through events that happen to them. The actual leveling up evolved over time in the application. As the characters were meant to level up and increase in attributes every time they crossed a threshold of 100 xp, the original implementation of leveling up was pretty simple:

```
def add_event_xp
    self.xp += planner.events.last.xp
    until self.xp < 99
      level_up
    end
    self.save
  end

  def level_up
    self.level += 1
    self.xp -= 100
    self.subtle += rand(1..10)
    self.powerful += rand(1..10)
    self.resistant += rand(1..10)
  end
end
```


This would add the xp gained from the last event in the character's planner, which holds the events that can happen to them, and level them up for every 100xp they had, leaving the remainder. Their attributes representing what the character was capable of - how subtle, powerful and resistant they were - would go up by a random number between one and ten. 

But...this didn't feel like enough. It was neat, but it didn't challenge my understanding of anything in Rails, and what I really secretly yearned for was this: when characters level up, they get a pool of 30 points to distribute among those 3 attributes, and can't raise them any higher. This would give the user choice, instead of a random increase in the attributes at hand. 

That's when I started searching around for ways to track attribute changes, to make a system that could check to see if attributes had changed by too much. I found the answer in [ActiveModel:Dirty](http://api.rubyonrails.org/classes/ActiveModel/Dirty.html), specifically in the changes method. 

I made some missteps at first - I really should have been using a counter, but instead I set each change in the individual attributes to a variable, then added them all up and threw up an error if they were above 30. Because changes returns a value of an array, something like [1900, 2000] if one increased an attribute from 1900 to 2000, my code would have to return the difference. This was simple, an example from my code: 

```
subtle_change = subtle[1] - subtle[0] 
...
if subtle_change + powerful_change + resistant_change > 30 
self.errors.add(:level, "Can't increase attributes by more than 30")
```

However, if changes were not executed on one of the three attributes, this look for the change would return nil, preventing the method from working. Instead, I went back to a simple counter:

```
def validate_leveling_up
    total = 0
    subtle = self.changes[:subtle]
    powerful = self.changes[:powerful]
    resistant = self.changes[:resistant]
    if self.subtle_changed?
      subtlechange = subtle[1] - subtle[0]
      total += subtle_change
    end
    if self.powerful_changed?
    powerful_change = powerful[1] - powerful[0]
      total += powerful_change
    end
    if self.resistant_changed?
    resistant_change = resistant[1] - resistant[0]
      total += resistant_change
    end
    if total > 30
      self.errors.add(:level, "Can't increase attributes by more than 30")
```
	
	This is when I learned more about validations than I was expecting. Specifically, that validations trigger when attributes are changed(not terribly new knowledge, but something I didn't fully expect the implications of), and that there's not a quick one line fix to limit a custom validation like validate_level_up to just one controller action. This lead to the very thorny situation that validating leveling up would trigger on the update action, including just editing a character. As I wanted users to be able to edit characters in a more free way, I had to find a workaround. 
	
This was the implementation that first worked - adding a new boolean attribute to signify if a user had just leveled up, and using it to trigger the validation:


```
ActiveRecord::Schema.define(version: 20170618125958) do

  create_table "characters", force: :cascade do |t|
    ...
		t.boolean  "just_leveled_up"
  end 

	
	Then I added these lines of code to signify that a user had just leveled up, and that a user was done leveling up.
	
	def add_event_xp
    self.xp += planner.events.last.xp
    until self.xp < 99
      levelup
      self.just_leveled_up = true
    end
    self.save
  end

And right at the end of the custom validation:

if total > 30
      self.errors.add(:level, "Can't increase attributes by more than 30")
      self.update_attribute(:just_leveled_up, nil)
```
			
Finally, I limited the validation to when :justleveledup was truthy and on the update action to make sure it was only triggering on an update(the action in my controller that updates a leveled character), and only that one in specific. 

`validate :validate_leveling_up, :on => :update, :if => :just_leveled_up?`

This makes the validation contingent, limited to the action I wanted it to act on through that being the only place in the code where :just_leveled_up? would trigger the validation, and dealt with the mess that was created by simply limiting the validation to update actions which would include editing the character. 

In conclusion, ActiveModel:Dirty allows for the tracking of attributes, and was essential to implementing a much-desired feature in my app. This simple leveling up system limited by the amount one can change attributes by could be expanded or modified in different ways, but provides an example of the power of ActiveModel:Dirty's tracking of changes, something I didn't know about and wasn't expecting to use before starting this project. 

Special thanks to Avi Flombaum for confirming I was on the right track in getting Dirty with Rails and providing some sample code that I used to figure out how to accomplish this functionality, especially that it would be a validator, as I hadn't worked much with custom validators before and I also learned a lot about validation in the process. 
