---
layout: post
title:      "The 3 most important things I learned making my first application..."
date:       2018-05-11 00:55:17 +0000
permalink:  the_3_most_important_things_i_learned_making_my_first_application
---


The CLI data gem project was the first appication I ever built. In previous courses, I've written small programs to solve programming problems like  cleaning data and performing calculations, but I had never built anything on my own, sans instruction, from start to finish. Like all first times, it was a learning process. So here are the 3 most important things I learned building a CLI data gem, and the 3 things I wish I did differently.


#### 1. "Build the code you wish you had" - Avi Flombaum


Often, the hardest part of starting something, especially something you have no experience in, is actually starting. "Build the code you wish you had" was the best piece of advice I gleaned from an extremely helpful [video walkthru](https://www.youtube.com/watch?v=_lDExWIhYKI). Thinking about the functionality you want your program to have, from the layout of the main menu to the which functions you need to build to make that happen, helps you to organize your thought process. Particularly, this encourages you to focus on the big stuff first and to drill down to the details later. Not only does it help you to use time efficiently, but you program develops organically to support your vision.

#### 2. Constantly question yourself and your code

Or refactor, refactor, refactor. Functionality is important, but so is clean and efficient code. Always review you code to see if it could be more efficient, even if the current iteration works well enough. The same goes for functionality. So I like to constantly ask myself the following:

* _This is neat, but is it something that a user would really be interested in? _
* _Does this make sense where it is?_
* _What is the value-added of this feature_

If the answer to any of these is no, no, or nothing, then refactor/delete with extreme prejudice. Nothing should be sacred. 

#### 3. Have fun with it

 There's a reason why easter eggs are a thing in programming. Coding is frustrating, essentially you're moving from one bug to the next, and enlivening the process with some levity will keep yourself motivated through the process. Make up funny names for imaginary users, books, songs, article names, functions, classes (as long as they're still descriptive). Depending on the application these may not make it into the final product, but if you're going to spend hours/days/weeks on a project, then you should enjoy yourself while doing it. For my project, I ended up using a western theme for my data gem. In fact, my scraper class is called `DataWrassler`. 
 
 ## ... and the 3 things I wish I did differently
 
 We learn the most when we fail, so here are the three things I wish I did better:
 
 
 #### 1. `git commit -m " "` and `git checkout -b branch-name`
 
 I originally set out to be good about committing early and often, as the project guidelines stated. I also wanted to always checkout branches before make changes as opposed to making changes to `master`. Unfortunately, I often found myself forgetting to commit every 10-20 minutes or not keeping my edits/additions contained within the scope a branch named `finetune-scraper-class` would imply. Next time I may get myself a timer that will go off at intervals to remind me to `git commit`. And as for branches, I plan to download a scheduling app like Trello to keep an outline of my planned changes. It should help enforce some discpline on my code process.
 
 
 #### 2. Pseudocode
  
 I wish I had utilized pseudocode more. At the very least to start to build the a habit before moving on to more complicated problems. Besides being a useful tool for your own purposes - it's like thinking out loud but with code -  pseudocode is useful for other programmers who will need to review and understand your code. It also is helpful for future you, when you're rereading stuff you wrote months ago.
 
 
 #### 3. Write tests
 
 I ran my code in the console often to see what broke and how the interface appeared in the terminal, but I wish I would have written more tests. Again, it would have been good practice, and I wish I had taken advantage of the opportunity at the time. At least there's always next time! 
