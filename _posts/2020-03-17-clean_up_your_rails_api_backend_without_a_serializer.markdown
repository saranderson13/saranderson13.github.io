---
layout: post
title:      "Clean Up your Rails API Backend Without a Serializer"
date:       2020-03-17 17:59:42 +0000
permalink:  clean_up_your_rails_api_backend_without_a_serializer
---


This is just a quick little tutorial on one technique that might help cleanup a Rails API backend, specifically, if you need to have a complex json render structure, but one that you would use over and over again. To be honest, this might be common sense to a lot of programmers, but for beginners I just wanted to put this out there because it wasn't something that immediately occurred to me.

Basically you're accomplishing what a serializer would do, but on a very small scale. And serializers do come in handy when you're working with lots of complicated data structures that require lots of different render configurations. In cases like this, they make your code more readable and can definitely save you a few lines. But I just came across a case where I needed to set up specific and moderately complex json renders, but for most controllers, that setup would be the same for every action. Using a serializer would have been overkill, but repeating the same very long render setup with the exact same code would have wreaked of repetition. Honestly, even for structures that are not that complex, I liked this idea because I could change the structure in one place, and not need to change it in every action. At this point you probably know what I'm getting at.

I put it in a method. :)

Specifically a private method. Take the following example of a class, where I have an index action and a show action. Note that they are both rendering the exact same json. Repeating this long(ish in this case) chunk of code is what I want to avoid. Because I could need to make the render a lot more complicated. 

```
class MonthsController < ApplicationController

    def index
        months = Month.all
        render json: months.to_json(
            include: [
                :days,
                :trackers,
                :events,
                :goals
            ]
        )
    end


    def show
        month = Month.find_by(id: params["id"])
        render json: month.to_json(
            include: [
                :days,
                :trackers,
                :events,
                :goals
            ]
        )
    end
end
```

Cue the private method. You simply return your entire include statement. For example:

```
class MonthsController < ApplicationController

    # All of your actions...

    private

    def json_include
        return [
            :days,
            :trackers,
            :events,
            :goals
        ]
    end
end
```

You would then format any action that needed to use this same configuration like this:

```
def index
    months = Month.all
    render json: months.to_json(
        include: json_include()
    )
end
```

So, that is a very simple include - but you can make it as complicated as you need to, with having nested includes and such. For example, a more complex example would be:

```
def json_include
    return [
        { :days => { include: :events } },
        { :trackers => {
            include: {
                :tracker_lines => { include: :tracker_days }
            }
        }},
        :events,
        :goals
    ]
end
```

Note that here you have several nested includes. So - you can do anything you would put in your usual 'render json'. And if you just have a single json return that you're repeating over and over again, this will clean up your code considerably. 



