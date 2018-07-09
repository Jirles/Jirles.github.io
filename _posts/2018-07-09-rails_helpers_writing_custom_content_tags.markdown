---
layout: post
title:      "Rails Helpers: Writing custom content tags"
date:       2018-07-09 17:16:57 +0000
permalink:  rails_helpers_writing_custom_content_tags
---


I just finished up my Ruby on Rails final project for Flatiron, and in an effort to keep my app as DRY as possible and my views as logic-less as possible, I found myself creating custom helpers. It was a bit of a struggle in the beginning, especially when it came to nesting HTML elements and passing helpers to other helpers, so I figure I better write a blog post about it to solidify my understanding and provide myself a cheatsheet of sorts. Also, maybe there are other Learn students who might benefit.

### Review

First, a little review. TL;DR helpers are ruby functions that encapsulate display logic you can use in your views. This keeps your views easy to read and understand by removing messy Ruby logic from your files. It also keeps everything nice and DRY if there's a particular display or layout you use in multiple views. 

Helpers are shared across every controller even though they should be organized by controller. 
```
app
|
|---
      helpers
			 |
			 |---
			       application_helper.rb
						 users_helper.rb
						 blogs_helper.rb
						 posts_helper.rb

```
Above is a basic example of the helpers folder file structure, probably for one of those blog applications programmers like to user in their examples so much. In the case of this file structure, helpers regarding displaying a post should be located in the `posts_helper.rb` file, but can be called in `'blogs#show` or `'users#profile'` or whatever view you need to display posts in. 

We already use helpers all the time. Rails has a lot of built in helpers such as `form_tag`, `link_to`, and `submit_tag`.  But sometimes, they're not enough so the rest of this post goes over how to make your own.

### `content_tag`

First, the basics. The [`content_tag`](https://apidock.com/rails/v4.2.7/ActionView/Helpers/TagHelper/content_tag) method is a building block for all my future helper method examples so let's start there. You can pass in an HTML element symbol, content, and some other options to `content_tag` and it will wrap it all up and present you view with some HTML. 

`content_tag :div, "Hello World", class: "greet-world-div"`

creates HTML

`<div class="greet-world-div">Hello World</div>`

There you go. There's your basic layout: 

`method_call, :html_element_as_symbol, "Your content", other_options_like_class: "option value"`

You can also pass in another `content_tag` call to the content section if you want to nest an HTML element inside of a parent element: 

`content_tag :div, content_tag(:p, "Hello World", class: "greet-world-p-child), class: "greet-world-div-parent"`

creates HTML

`<div class="greet-world-div-parent"><p class="greet-world-p-child">Hello World</p></div>`

This works well for a single nested child, but when you want multiple lines of HTML things can get a little weird.

### Multi-line and nested HTML helpers: blocks and `capture`

If you want a helper that displays multple lines of HTML, you might think you can do this: 

```
def display_user_info(user)
    content_tag :span, user.name, id: "user-name"
    content_tag :span, user.hometown, id: "user-hometown"
    content_tag :span, user.birthday, id: "user-birthday"
end 

```

But you would be wrong. In fact, it will only return the last line (in the above example, our user's birthday). So how do you display mutliple lines? 

You pass `content_for` a block or use the `capture` method! These two helpful methods work in different ways, so depending on whether you want your HTML elements nested under a parent (children), or just chilling next to each other (siblings) you use one or the other.

Want HTML siblings? Use a `capture` block: 

```
def display_user_info(user)
    capture do 
        content_tag :span, user.name, id: "user-name"
        content_tag :span, user.hometown, id: "user-hometown"
        content_tag :span, user.birthday, id: "user-birthday"
    end 
end 

# creates HTML

<span id="user-name">User name</span>
<span id="user-hometown">User's Hometown</span>
<span id="user-birthday">User's Birthday</span>

```

Want HTML children nested under a parent? Pass a block to `content_tag` and use the `concat` method to concatenate all the children elements:

```
def display_user_info(user)
    content_tag :div, class: "parent-div" do 
        concat(content_tag :span, user.name, id: "user-name")
        concat(content_tag :span, user.hometown, id: "user-hometown")
        concat(content_tag :span, user.birthday, id: "user-birthday")
    end 
end 

# creates HTML

<div class="parent-div">
    <span id="user-name">User name</span>
    <span id="user-hometown">User's Hometown</span>
    <span id="user-birthday">User's Birthday</span>
</div>
```

It's important to use the `concat` function as it adds the content to the output buffer. Otherwise, you'll have an empty `div`!


You can also use an enumerator like `each` and `collect` to produce HTML content:

```
def display_authors_posts(author)
 content_tag :div, class="author-posts-div" do 
  author.posts.each do |post|
   concat(link_to post.title, author_post_path(post))
  end 
 end 
end 

# creates HTML

<div class="author-posts-div">
 <a href="/authors/1/posts1">Post 1</a>
 <a href="/authors/1/posts2">Post2</a>
</div>

```


###  Passing helpers to helpers

Helperception. 

While writing my Rails project, I found myself creating some simple helpers that cleaned up my views so I wasn't constantly using ERB to interpolate strings. Keeping with our blog application example, these helpers would be something like a `display_post_rating` helper that took information from a post comment and created the following line: "Shelly rated this post 5 stars!" for a post's view page or "Shelly rated your post 5 starts!" if you were an author view his/her own posts. The helper would look a little like this:

```
def display_post_rating(author, comment)
 if author == comment.post.author
  "#{comment.author} rated your post #{comment.rating} stars!"
 else
  "#{comment.author} rated this post #{comment.rating} stars!"
 end 
end 
```

Super simple. But let's say I've now decided to create a larger helper to handle post formatting and I want to use this helper in my larger helper: 
```
# in view 

<% @posts.each do |post| %>
 <%= display_posts(post, @author) %>
<% end %>

#helper, assuming comment class has a class scope method .most_recent, which orders result into  according to when they were created/updated

def display_posts(post, author)
 content_tag :ul do 
  concat(content_tag :li, post.title)
  concat(content_tag :li, post.content)
  post.comments.most_recent.limit(3).collect{ | comment | concat(content_tag :li, display_post_rating(author, comment)) }
 end 
end 
```

Unfortunately, that won't work. I can't just pass the name of this helper into my `display_posts` helper as an argument as is. First, I have to turn it into a [Proc](https://ruby-doc.org/core-2.4.1/Proc.html) object.
> Proc objects are blocks of code that have been bound to a set of local variables. Once bound, the code may be called in different contexts and still access those variables.

You should look at the Ruby documentation to learn more about `proc`'s, but for our purposes, just think of `proc` objects as Ruby objects that let us pass methods as arguments and call them inside other methods.

To get his big helper working, first, we need to rewrite `display_post_rating` to make it a `proc`:

```
def display_post_rating
 Proc.new do  |author, comment|
  if author == comment.post.author
   "#{comment.author} rated your post #{comment.rating} stars!"
  else
   "#{comment.author} rated this post #{comment.rating} stars!"
  end 
 end 
end 

```

That's it. We wrap our original code in a `Proc.new` call so it is sent to the `proc` initialization method as a block. Our arguments become block level variables. Now, `display_post_rating` can be safely passed into `display_posts` as an arguement:

```
# in view 

<% @posts.each do |post| %>
 <%= display_posts(post, @author, display_post_rating) %>
<% end %>

#helper 

def display_posts(post, author, display_post_rating)
 content_tag :ul do 
  concat(content_tag :li, post.title)
  concat(content_tag :li, post.content)
  post.comments.most_recent.limit(3).collect{ | comment | concat(content_tag :li, display_post_rating(author, comment)) }
 end 
end 
```

We're closer now, but our larger helper still won't work correctly. Right now calling `display_post_rating` with `display_posts` will fail. In fact, calling `display_post_rating` will return a `proc` object (`#<Proc:0x007fccd6b342f0@(irb):3>`), which is not what we want. We want to invoke the block we wrapped our `proc` object around. To do that, we need to use the `#call` method inside the helper:

```
def display_posts(post, author, display_post_rating)
 content_tag :ul do 
  concat(content_tag :li, post.title)
  concat(content_tag :li, post.content)
  post.comments.most_recent.limit(3).collect{ | comment | concat(content_tag :li, display_post_rating.call(author, comment)) }
 end 
end 
```


Viola. 

