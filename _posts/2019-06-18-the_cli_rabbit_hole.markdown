---
layout: post
title:      "The CLI Rabbit Hole"
date:       2019-06-18 07:55:27 +0000
permalink:  the_cli_rabbit_hole
---


I will start this post off by saying that I may have been far too ambitious with the goals I had for my CLI application. I feel like I just got a brief glimpse into the mind of the main character of that 'choose your own ending' Bandersnatch episode of Black Mirror. It wasn't that I didn't have direction when I started this monster; the initial thought was "Oh, cool! I can scrape some superpowers! I bet I can make some quick superheroes with those!" And that would have fulfilled the assignment: 1. Scrape some stuff, 2. Do something with it. (And of course also demonstrate an understanding of OO Ruby and the basics.)

But then I started to become aware of the possibilities. And even as I'm sitting here writing this, I'm thinking of more things that I could, and probably should make this application capable of. It really made me realize the beauty of object oriented programming, because there are so many ways that the data can relate to each other, and so many ways to analyze it and compartmentalize it and look at it. This post isn't going to go into the technical part of this project so much because to be perfectly honest, that was not the part that gave me an issue. What I learned most from this project is that it is super important to plan out ahead of time, and set reasonable goals for the amount of time at hand. In my defense, this was my first foray into heavy scraping. But if I were to start this program over at the beginning, there are two things that I would make sure to do that would both save time, and improve the my direction:

1. Write tests! Even though I'm pretty sure (and I hope?) this wasn't actually a requirement for this project, I was initially planning to write at least a few basic tests. I set up a spec file and everything. But as I got started I found it was easier to just manually test things by calling the methods in the script and using binding, and that dealing with writing the tests was actually slowing me down considerably. But It's all well and good going through and testing one thing at a time when you've only got one issue at a time. Once I got to the point where there were several things that could go wrong, it started taking a lot longer to pin down the problem area - especially with the sheer volume of things that needed testing. It became obvious that tests would have made my life a lot easier in the long run, and I realized that I had taken the tests in the labs for granted. While it was possible to get down to it and test individual problems, I found that I would start testing an issue, find a second issue, and then start testing that one, leaving the first issue forgotten on the wayside until somewhere down the road I remembered that there was still a bit of broken code lying back there. Having tests would have at the very least given me some direction, and a handy list of things that I still needed to work on.

2. Plan out the shared methods ahead of time. Aside from a handful of super detailed tumblr themes, this is probably the most code I've ever written for a single project. But once it got down to it, and I really started taking advantage of the nature of object oriented coding, it didn't need to be. When I got to a point where most of the program was working and I could go in and refactor a bit, I found SO may redundancies. I think that this is always unavoidable to some extent. But I would have saved a lot of time by making the more simple methods compatible for multiple classes right off the bat, and THAT is totally doable. The methods I found that could apply to most classes were Findables, List Makers, and Validators - the ones that check a user's input. In fact, there are still some methods of those varieties that I' like to go back and move to my Resources Module. But doing it all in hindsight requires a fair bit of refactoring. In the future I'm going to just plan to write those sorts of methods in a module first. 

And actually, there was one cool technical thing that I figured out about  ```extend``` and ```include```. Because a module does not reference 'self' to differentiate between Class and Instance methods, I was able to use the same module to add functionality to classes on both the class *and* instance levels. Check it out:

In my Resources Module, I provide the following code:
```
module Resources
  module InputValidation
    ### INPUT VALIDATION METHODS
    def super_basic_range_validation(upper_limiter, selection)
        if (1..upper_limiter).include?(selection.to_i) || selection == 'exit'
            true
        else
            puts "Response is out of range. Please enter a valid response."
            false
        end
    end
		def basic_cli_validation(selection)
      if (1..4).include?(selection)
        true
      else
        puts "Please enter a valid selection ( 1 / 2 / 3 / 4 )"
        false
      end
    end
		
# the module continues with a variety of other validation methods.
```

I can then reference it with both ```extend``` and ```include```, and I can now call the ```#super_basic_range_validation``` method on both the CommandPrompt class directly, *and* instances of the CommandPrompt class.
```
class CommandPrompt
    include Resources::InputValidation
    extend Resources::InputValidation
	
	# class continues below
```

...meaning, both of these codes work:
```
# In this case, 'self' refers to an instance of the CommandPrompt class.

self.selection = gets.chomp!
self.selection = gets.chomp! until self.super_basic_range_validation(4, self.selection)


# In this case, it refers to the CommandPrompt class itself.

self.basic_cli_validation(self.selection)
```

Basically, I wish I had another two weeks to work on this project. Once I got to the point of being able to curate a hero's powers, and have a whole list of heros and villains that had cohesive powers, who actually started to feel like characters rather than randomly generated lists of data, I wanted to create a whole universe, with teams and affiliations, and world events! And even an interface for a battle! But I suppose it will have to wait until break week. :D
