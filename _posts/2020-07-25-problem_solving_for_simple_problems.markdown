---
layout: post
title:      "Problem Solving for Simple Problems"
date:       2020-07-25 11:51:39 -0400
permalink:  problem_solving_for_simple_problems
---


Today I'm going to walk through a simple problem. One that many programmers and developers have already hashed through by themselves. The point of this exercise is not to show the solution to the problem, but the process of getting there in an effective and efficient way. This could be in a technical interview, or just working through a problem on Code Wars or Hacker Rank, but more broadly, literally any time you're coding and have a problem that you haven't approached before. To demonstrate my approach to solving simple problems, I'm going to create a method to determine primality. I'm writing in Ruby, because I ♥ Ruby, but as I said this is about the process.

DISCLAIMER: I am not a math genius. I'm definitely more of a designer and developer. I know the basics of what makes a prime number. I'm going to optimize to the point that I can, but most likely this would not be a great solution to determining primality for very large numbers. I am aware that there are more effective ways to cut down the number of numbers you need to test, especially once you get up into those numbers that are represented with carrot notation, but I don't know what those methods might be or how to implement them off the top of my head. I'd like to think I could if I did some research. (Also, I'm not positive that 'carrot notation' is the proper name for that notiation.) I am VERY welcoming of improved methodology in the comments. This is just the way that I approached the problem with my current knowledge, not looking anything up, except for some higher-ish prime numbers for testing purposes, because in a technical interview situation that is what I'd have and that's what I'm practicing for right now. I am also VERY welcoming of feedback from anyone that has experience with evaluating candidates in technical interviews. ☺

Okay. Onto the problem at hand. I try to stick to a few basic steps when solving a problem for the first time:

1. Write out what the end goal is. What should your method accomplish and return? 
2. Pseudocode. 
3. Arguably the most important step. Solve the problem in the way that is easiest for you *knowing* that there will be ways to optimize. 
4. Once you have a working method, start optimizing, troubleshooting and safeguarding. 


Throughout all of this you should be keeping an eye out for edge cases or flaws in your logic. Now we can move through those steps with the actual example.


### Step One: The Goal
This step may seem unnecessary with such a simple problem, but writing it out helps me stay focused. It is absolutely crucial with a more complex problem that has a few stipulations and should accomplish a few things.

```
# Return TRUE or FALSE based on whether the inputed number is prime.
```


### Step Two: The Scaffold
I like to write in some real code here too, for instance, I usually just go ahead and create the frame of the function, but then inside there I comment about the steps that I think I should take.

```
def prime? num
    # Eliminate obvious things that would make a number not prime.
        # Negative numbers are not prime.
        # 0 and 1 are not prime.
        # Even numbers are not prime.

    # Test options that MAY make the number not prime.
        # The number is prime if it is divisible, with no remainder, by a number other than 1 and itself.
end
```


### Step Three: Get a Working Method
Just get something that works. If you're in a technical interview scenario, it's good to verbally communicate that you know there will be ways to optimize, but that you first want to just be able to generate a working method. Try to make a note of those places that you think could be optimized, but where actual optimization requires a bit more pondering. It's good to do this verbally too. In places where it is easy to optimize, just do it. Definitely don't just write out a long solution so you can optimize later. That will slow you down and make you look like you can't write succinctly when there are obvious ways to do it. Don't write out a three layer if/else statement when you can just test all three of your conditionals in the first if. But if you have an if statement and you aren't sure yet whether you'll need more than one action based on the conditional, I suggest going long hand. It gives you more wiggle room. You might even find that what you initially write out in a few steps can in fact be reduced to something that could be put in a ternary.

```
def prime? num
    # Eliminate obvious things that would make a number not prime.
        # Negative numbers are not prime.
        # 0 and 1 are not prime.
        # Even numbers are not prime.
				
    return false if num <= 1 || num.even?()


    # Test options that MAY make the number not prime.
        # The number is prime if it is divisible, with no remainder, by a number other than 1 and itself.
    
		(2..num-1).to_a().each do |i|
		    return false if num % i == 0
		end


    # If false has not yet been returned, the number must be prime.
    
		return true
end
```


### Step Four: Optimization, Troublshooting, and Code Integrity
Start with the places that you noted before. They will likely lead you to more places that can be simplified, reduced, combined or made more efficient from a runtime perspective. True optimization is working on that runtime perspective. 

Now, there are a few problems with the above code. To be specific: 
- There is some flawed logic
- There is a place where the code can be written more succinctly
- There is a place where you can cut down the runtime. 
- And there is also a place where you should defend against the user being a shenaniganizer and causing your code to break.

**Flawed Logic:** In my initial 'obvious' tests, I checked for whether the number was even. I did this so that if it was, I wouldn't have to go through the iterization, which would slow down the runtime in a completely unnecessary and avoidable way. However, there is actually *one* even number that *is* prime: 2. So I should add that into my check.

```
return false if num <= 1 || (num != 2 && num.even())
```


**Improving Succinctness** Now, it is very possible that some programmers would have just written this part as succinctly as necessary on the first go around. I actually did, but for the purpose of having an example of improving succintness, I wrote it out the long way. Your each loop can be written in one line, because it only has one line within it. It could even be written in one line if it only had two very short actions.

```
(2..num-1).to_a().each { |i| return false if num % i == 0 }
```

**Improving Runtime** Dividing any number by more than the number that would result in the solution equalling two, would result in a number with decimals between 1 and 2, so taking the modulus with an *i* that is higher than your number divided by 2 will never result in a remainder of 0. So there is really no need to test any numbers above your number divided by 2. In the case of a small number, this won't do much. But if you have a really big number, it could genuinely help.

```
(2..num/2).to_a().each { |i| return false if num % i == 0
```

Note that it is safe to just say num/2 because (at least) with Ruby, an Integer divided by an Integer will always return an Integer. So 4 / 2 = 2. But 5 / 2 will also = 2. The point being, you will never get 2.5. And actually, even if you did end up with the issue of getting a Float there, by using a Range and turning it into an Array, you safeguard against that too. You also don't need to worry about testing with 3, because that is higher than the true half of 5.

```
(1..5.5).to_a()
--> [1, 2, 3, 4, 5]
```

**Guarding Against Shenaniganizers** You know what also can't be a prime number? Something that isn't even an Integer to begin with. But that won't stop some tomfool from trying to enter a non-integer in as an argument. This is something that you should immediately test for. In fact, if they enter something like "Sarah is incredibly cool!", your code will break on the first line once you try to test if it is less than or equal to 1.

```
return false if !num.is_a?(Integer) || num <= 1 || (num != 2 && num.even?())
```

Now, I'm pretty happy with this method. It's the best I personally could do in an on the fly situation. If I were doing a technical interview for a position that relied heavily on an extreme amount of math knowledge, it probably wouldn't cut it. But just as an example of my problem solving process, as long as I could get there fast enough, I would at least be happy with my result.

```
def prime? num

    return false if !num.is_a?(Integer) || num <= 1 || (num != 2 && num.even?())

    (2..num/2).to_a().each { |i| return false if num % i == 0 }

    return true
end
```

If you want to take that method and play around with it, here are some test cases.

```
puts prime? 1    # >> false because 1 is not prime
puts prime? 2    # >> true because 2 is prime
puts prime? 191  # >> true because 191 is prime
puts prime? -5   # >> false because negative numbers aren't prime
puts prime? 24   # >> false because it is not prime
puts prime? 1.5  # >> false because it is a Float 
puts prime? "a"  # >> false because it is a String
puts prime? 7919 # >> true because it is prime
puts prime? [1, 2, 3, 4]  # >> false because it is an Array
puts prime? ({ a: "b" })  # >> false because it is a Hash
```



