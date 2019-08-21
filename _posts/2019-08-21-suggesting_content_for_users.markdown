---
layout: post
title:      "Suggesting Content for Users"
date:       2019-08-21 17:33:53 +0000
permalink:  suggesting_content_for_users
---


One of the main features that I wanted to incorporate in my app was the ability to suggest recipes that the user might like. But one thing I don't like about a lot of "suggestion" based content is that once the user starts liking a bunch of the same kind of thing, that becomes all they see. They like a thing, it suggests a similar thing, and then they like that thing, and another similar thing is suggested and then without much effort or really meaning to do so, the content they're being shown is basically all the same. I didn't want to have the users get pigeon-holed - unless they wanted to.

To walk this fine line I created two methods - one that suggests recipes based on both ingredients that the user has liked, as well as all of the ingredients in other recipes they have liked, and one that just suggests similar recipes, using a recipe's ingredients. The first method is featured on the home page when a user is logged in, and the second is featured on individual recipe pages. So they're shown a wide variety of suggestions on the home page, but they can also just click through similar recipes by viewing recipe pages.

This is the method for the home page 
 - It first sets up a variety of arrays to sift through the data, and collects all of the user's likes.
 - It then randomly selects three of the users likes and breaks them down into ingredients. So if a like is an ingredient, it will add that ingredient to the 'working_ingredients' array. But if one of the three random likes is a recipe, it will extract all of the main ingredients (not garnishes), and add those.
 - What you end up with is an array with at least three ingredients.
 - It then collects all recipes that use those ingredients, and randomly selects three to show to the user.

In this way, the variety of recipes that might be found is pretty wide.

```
def you_may_like(user)
      all_likes = []
      selected_likes = []
      working_ingredients = []
      suggestions = []
      already_liked = user.rlikes.map { |rl| Recipe.find(rl.recipe_id)}

      # Collect all likes
      user.ilikes.each { |i| all_likes << i }
      user.rlikes.each { |r| all_likes << r }

      # Randomly select 3 likes to use as a base
      until selected_likes.length == 3 do
        s = all_likes.sample
        selected_likes << s unless selected_likes.include?(s)
      end

      # Extract ingredients from selected likes.
      # Add ingredient if the like is already an ingredient.
      # Add ingredients if like is a recipe (but not garnish ingredients)
      selected_likes.each do |like|
        if like.class == "Rlike".constantize
          extract = Recipe.find(like.recipe_id).ingredients
          extract.each { |i| working_ingredients << i if i.category != "Garnish" }
        else
          working_ingredients << Ingredient.find(like.ingredient_id)
        end
      end

      # From working_ingredients, collect all recipes that use each ingredient.
      # Make sure the suggestion array is unique.
      working_ingredients.each do |i|
        i.recipes.each { |r| suggestions << r if !already_liked.include?(r) && r.creator != current_user }
        suggestions.uniq!
      end

      # If more than three suggestions were generated, randomly select three to feature.
      # Otherwise return all suggestions.
      if suggestions.length > 3
        suggestions.sample(3)
      else
        suggestions
      end
    end
```


In the similar_recipes method, recipes will be shown if they share at least two ingredients with the recipe the user is currently viewing. Non-Garnish ingredients are extracted, and other recipes the are used in are collected. Then I compare the ingredient sets by subtracting the arrays. If at least two are shared, it collects the recipe to show the user.

```
def similar_recipes(recipe)
      # Extract main ingredients
      recipe_mains = recipe.ingredients.map { |i| i unless i.category == "Garnish" }.compact
      similar = []
			
			# For each ingredient, find all recipes that include that ingredient.
			# Compare the ingredients of that recipe with the current recipe
			# Collect that recipe if they share two ingredients, (and aren't the same recipe)
      recipe_mains.each do |i|
        if i.category != "Garnish"
          i.recipes.each do |r|
            compare_mains = r.ingredients.map { |i| i unless i.category == "Garnish" }.compact
            similar << r if (recipe_mains - (recipe_mains - compare_mains)).length >= 2 && r != recipe
          end
        end
      end
      similar.uniq!
    end
```

The subtracting array part came in handy for a few other methods too. Here's an example of what's going on:

```
array1 = [1, 2, 3, 4, 5, 6]
array2 = [4, 5, 6, 7, 8]

 # This is what array1 has that array2 doesn't have.
array1 - array2 = [1, 2, 3]   

# By subtracting what they don't have in common, you are left with what they do have in common.
array1 - (array1 - array2) = [4, 5, 6] 
```

And those are the methods I used to suggest content to my users. I do think that with a massive database they would need to be refactored or maybe some reorganization would be necessary so that the app didn't become slow. But with a reasonable sized database they seem to hold up. And conceptually they accomplish what I was going for.


