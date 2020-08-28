---
layout: post
title:      "Dynamic Method Calling with `.send`!"
date:       2020-08-27 22:36:42 -0400
permalink:  dynamic_method_calling_with_send
---


Gonna dip into some ruby today. One thing to strive for when writing in an object oriented language is to write DRY code - meaning "don't repeat yourself". This is accomplished with *abstraction*, and there are many ways to abstract code. Inheritance, for one, which can be utilized by creating a class that subclasses extend from, or by creating a module that can be included in multiple classes. But another way to abstract is to make methods as multi-functional as possible. 

Now wait a tic, I want to emphasize that having a single octopus-like method that does ALL THE THINGS is bad. Making a method multi-functional should still be balanced with the 'single responsibility principal'. But it's also ridiculous to have three different methods that are nearly identical, except the return hinges on manipulating the data in different ways.

The aim is to combine methods that answer the same basic question, into a single method that can *dynamically* trigger other methods. Have you ever, for instance, wanted to pass in the name of a method as an argument - (a string to be specific) - and then call it? Well, you can!

## .send

Consider the following:

An instance of a `Team` has two properties: `wins` and `losses`. Sometimes you need to calculate the team's percentage of wins, sometimes you need to calculate the percentage of losses. You could make two methods:

```
def win_percentage
    return self.wins.to_f / (self.wins + self.losses)
end

def loss_percentage
    return self.losses.to_f / (self.wins + self.losses)
end

# NOTE that the `.to_f` is in there so that it will return a float. 
# 8 / 10 will return 0 
# 8.0 / 10 will return 0.8 - much more useful.
```

But these are essentially doing the same thing. They're dividing a number by the sum of two numbers. The *same* sum in fact. The only thing that differs is the method being called to access the property you want to know the percentage of. Wouldn't it be a lot easier to just have one method, and be able to tell it which one you wanted to calculate the percentage of? `.send` to the rescue.

```
def calculate_percentage(desired_stat)
    return self.send(desired_stat).to_f / (self.wins + self.losses)
end
```

Now, now method can be called with the name of the property, and it will dynamically call that on the instance of team. Calling `self.send("wins")` is equivalent to calling `self.wins`.

```
team.wins = 8
team.losses = 2

team.calculate_percentage("wins")
=> 0.8

team.calculate_percentage("losses")
=> 0.2
```

And there are so many cases where passing in a method name as an argument could come in handy. Personally, when I learned about `.send` it was a game changer, and I hope it will be for you too. ~(˘▾˘~)

