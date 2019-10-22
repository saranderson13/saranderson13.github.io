---
layout: post
title:      "Finding the Balance: How Tiny Tweaks can Manipulate the Battle"
date:       2019-10-22 04:12:59 +0000
permalink:  finding_the_balance_how_tiny_tweaks_can_manipulate_the_battle
---


The key feature of my rails project - really the entire point of it, and what everything leads up to - is the BATTLE. There was a lot of (...what I guess I would call) infrastructure necessary to get to the point where a functional battle is even possible, but once the groundwork was layed down what was left were a bunch of numbers, and a TON of potential ways to manipulate them. Here's a quick rundown:

Characters have (up to three) Superpowers
Superpowers have two Moves
Moves have a Base Point value.

Characters also have Statistics, and a Level:
-- A Hitpoint stat (the one that determines how much damage they can take), 
-- an attack stat
-- and a defense stat
-- and their level increases as battles are won

What I wanted to accomplish in the gameplay was for the users to not go into the battle knowing if they would win or lose. It should be somewhat unpredictable - otherwise what's the fun. But that turned out to be a very fine line to walk. Write the formula for calculating the damage a move can do one way, and the characters with high attack stats will always win. Make dodging too easy and characters with high defense stats can never be beat. I wanted a formula that would allow characters of all kinds to win *sometimes*. 

In the end this is the formula that I came up with and it seems to do the trick - characters with all different kinds of stat profiles can find a way to win. 

```
def adjusted_pts(protag, antag)
    # The method is called with arguments of the attacker and their opponent in that order.

    # The type of move is determined - whether it is a common attack or a power attack.
    if self.move_type == "att"
      
      # The base points of the move are added to 2x the character's level:
      # That is then multiplied by a formula that takes the protagonist's attack stat into account.
      # The result of the #protag_att_adjustment method will be between 1 and 2, and increase the inflicted damage.
      # That result is then multiplied by a formula that takes the opposing character's defense stat into account.
      # The result of the #antag_def_adjustment method will be between 0 and 1, and will decrease the inflicted damage.
			
      (((self.base_pts + (2 * protag.level)) * protag.protag_att_adjustment) * (1 - antag.antag_def_adjustment)).round


      # If the move is a power move, there is a higher multiplier on the level.
      # Meaning power moves get stronger more quickly as you level up.
      # The formulas are otherwise the same.
			
    elsif self.move_type == "pwr"
      (((self.base_pts + (3 * protag.level)) * protag.protag_att_adjustment) * (1 - antag.antag_def_adjustment)).round
    end
  end
```

These are the other two methods used in the formula above.
```
def protag_att_adjustment
    1 + (self.att/500.0)
  end

def antag_def_adjustment
    (1 - (self.def/500.0))/2
  end
```

Tweaking the multiplier, or changingthe way the stat adjustment formulas are calculated changed the effective damage of a move dramatically. For instance, before cutting the defense adjustment in half, the actual damage level of a move was almost always less than the base point valule, and it came into even more effect when it came to dodging.

Dodging is based on their defense stat, using the same formula as above. Basically, it takes their adjusted defense stat, which will usually be a float number below .5, and turns that into a percentage out of 100. It will shovel that number of 'true's into an array, and fill the rest of the array up with false. (So, if their adjusted defense stat is .3, their dodge array will have 30 trues, 70 falses, and approximately a 1 in 3 chance of dodging an attack.

When the defense adjustment formula was not divided by two, characters with high defense stats would dodge almost every time, because their array would end up with 80 trues (meaning they dodged) and only 20 falses.

```
def dodge?
    arr = []
    ((self.antag_def_adjustment * 100).to_i).times { arr << true }
    (100 - arr.count).times { arr << false }
    arr.sample
  end
```


So, it's all about balance - in the current state, some characters can knock out certain characters in one move! But they can't knock out *every* character in one move, and they can definitely be beaten by a character with the right stats.


