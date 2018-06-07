---
layout: post
title:      "Adding a Capybara test suite"
date:       2018-06-07 21:55:47 +0000
permalink:  adding_a_capybara_test_suite
---


Going into the Sinatra section of Flatiron School's curriculum, I had decided I wanted to write a test suite for my final project. Up to that point, everything I had learned or read about Test Driven Development (TDD) had given the impression that it was a best practice I should be utilizing. Plus, I knew the company I worked for was a big fan, so I decided come project time, I'd get my feet wet and build out an entire test suite. 

Several days later, I'm glad I did. I've emerged from the other side with a better understanding of Capybara, a passing test suite, and minimal emotional scarring. 

![BOOOOOOM](https://gph.is/2HsgASR)


Overall, it was pretty simple and while writing out the tests added more time, it was worth it for the experience. That being said, test writing would have been easier if I had a better idea of what I was doing when I started. I was pretty much winging it, basing my tests off of what I had seen in the spec files in Flatiron's Learn IDE.  On the one hand, hands-on practice and stepping outside of your comfort zone to complete a set goal is perhaps the best way to master a subject. On the other hand, I ended up making a crucial  mistake that required that I refactor my entire test suite (up to that point). So before I start listing the what I learned writing out my own Capybara test suite... 

## A disclaimer

Do I say, not as I did. Read the Capybara [README](https://github.com/teamcapybara/capybara/blob/master/README.md) or browse the [documentation](http://teamcapybara.github.io/capybara/) before diving in. While I learned how to write and structure tests, having a deeper understanding of would have saved me hours of pain.

And now...

## Set up

Setting up a Capybara test suite is as simple as setting up a spec folder that contains a `spec_helper.rb` and requiring it in each spec file. You can see my spec folder setup and `spec_helper.rb` at my [github repo](https://github.com/Jirles/mangez-meal-planner).

## Structuring tests

```
describe 'Recipe controller' do 
    context 'recipe view page' do 
		    before do 
				  # items to do before the beginning of each 'it' block
						# maybe create an Recipe and a User and log in
				end 
				
			it "shows a recipe's details" do 
				   # write tests
			end 
		end
end 

```

Each file followed that structure. The topmost `describe` block encompassed the entire test suite and was named after the controller it was testing. The next level down were the `context` blocks that descibed the particular page I was testing (you can also use `describe` again). Finally, under that one were the `before` and `it` blocks, the real heart of the testing suite. `describe` and `context` blocks do exactly what they say they do, they provide a description and context for the tests executed within them. In fact, if the test above threw an error, it would say `Recipe controller recipe view page show's a recipes details`. Capybara takes all of title strings and concatenates them together, so make those descriptive. 

The `before` and `it` blocks contain executable code that your application is tested against. `before` blocks work by executing code before the beginning of each block after it, in the case above, it would be the `it` blocks. This is important because  each `it` block is essentially run in isolation, meaning that any log-ins or created recipes will dissapear once the next `it` block starts. I often used `before` blocks to do things like create objects I would need for subsequent tests like recipes and users, and make sure a user was always logged in. 

The  `it` blocks contain the integration code that will actually run against your website. It will simulate a user navigating your website and then check the Capybara Session object to see that they see the appropriate page or that the page's content or url are what you expected. 

## `visit` & `page` vs. `get`/`post`/`patch` % `last_response`

Seeing as I just jumped into Capybara testing with no understanding of the underlying code outside of reading through some test suites in the Learn IDE, it's not surprising I ran into troubles halfway through. I had noticed that pattern of using `visit '/some-page'` in conjunction with `page` in the `#expect` statement:
```
visit '/hello-world' 

expect(page.body).to include("Hello World")

```
or using an HTTP request followed by `last_response`:
```
get '/hello-world'

expect(last_response.body).to include("Hello World")

```

At first, I thought they were exactly the same, just two different ways of saying the same thing. In a way, that was right, but in another, more important way it was wrong. This was the mistake that had me refactoring my test suite halfway through. Essentially, they differ in that the `get/post/patch '/somepath` / `last_response` combination are actually Rack Test methods and **don't maintain sessions**. This was what got me.  The Rack Test suite methods wouldn't remember that a user has been logged in so it was never seeing the page or path that I needed. The tests were being redirected to the log-in page because you had to be logged-in to view them! 

Once I discovered this, everything was pretty much smooth sailing. 

By the end of my test suite, I had a general rule of thumb:
* Stick to `visit` and `page`: the number of methods and matchers were far richer than Rack Test's and it more closely follows how a user would actually use a site with navigation methods like `click_button` and `fill_in`
* Only resort to using Rack Test when you need an HTTP request other than `get`, normally this was to act like a malicious user by sending the server bad data with missing fields 
* Once I used either Capybara or Rack Test within an `it` block, I stuck with it


**Using Capybara sessions**
```
it 'allows a user to edit a recipe and redirects them to the recipe index page' do 
		# user was logged-in in the before block
		
		visit "/recipes/#{@recipe.id}/edit"
		fill_in(:name, :with => Better Than A PB&J Sandwich)
		fill_in(:instruction, :with => "spread peanut butter, bananas, and nuttella on bread and enjoy")
    click_button "Save"
		
		expect(page.current_url).to include('/recipes') #=> in Rack Test, last_response.location
		expect(page.body).to include("Better Than A PB&J Sandwich")
end

```

**Using Rack Test**
```
it 'redirects the user to the create recipe page if the data submitted was invalid' do 
    # unlike the capybara tests above, you need to login within this block to use Rack Test
    params = {:username => "test user", :password => "secret"}
		post '/login', params #=> send a 'post' request to the path '/login' and pass in     
		
		params = { :name => "PB&J", :ingredients => "peanut butter, jelly, bread"} 
		#missing the instruction field
	  post '/recipes', params
		
		expect(last_response.status).to eq(302)
		follow_redirect! #=> Capybara automatically follows redirects, but Rack Test needs this method
		expect(last_response.body).to include("Please fill in all fields.")
end 
```

## Nifty methods

I'm going to finish out this blog post by sharing some nifty methods I learned stumbling my way through Capybara. 

First off, take a look at some of the matchers Capybara gives you access to [here](https://www.rubydoc.info/github/jnicklas/capybara/Capybara/RSpecMatchers#have_selector-instance_method). 
* Need to make sure the page rendered has a form? `expect(page).to have_selector("form")`.  
* Want to make sure a certain link doesn't show up? `expect(page).not_to have_link("Delete")` .  
* Need to make sure certain checkboxes or radio buttons are checked? `expect(page).to have_checked_field("recipe-id")`. 

Nifty. Just be sure you know how Caybara is finding elements on your site. Regarding the previous examples,`have_selector` is looking using the css selector `form`, `have_link` is looking for text, and `have_checked_field` if looking for the tag id. 

Another interesting thing you can do with Capybara is using css selectors to scope your test to a certain section of your page. For example, my app had a complicated form that had the same information (lists of recipes) in three different sections. So I wrapped each section in a section tag with an id, and in the test file: 

```
within(:css, "#breakfast") {choose "1"} #=> "1" was the element id
within(:css, "#lunch"){choose "2"} #=> :css indicates Capybara should use the css selector "#lunch" to find the block to look into 
within(:css, "#dinner"){choose "3"}

```

Finally, there's the `save_and_open_page` method. It will save a snapshot of the page as Capybara sees it and opens the snapshot in your browser. It's helpful for debugging. Just make sure you have the [launchy gem](https://rubygems.org/gems/launchy/versions/2.4.3) in your Gemfile and you're good to go!

