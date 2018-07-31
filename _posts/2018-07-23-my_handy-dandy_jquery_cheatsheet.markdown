---
layout: post
title:      "My handy-dandy jQuery Cheatsheet"
date:       2018-07-23 18:31:54 -0400
permalink:  my_handy-dandy_jquery_cheatsheet
---


That's it. Just a cheatsheet/summary of some [jQuery](https://jquery.com/) selectors. functions, and AJAX request/response functions![](![](http://)http://) that I found to be helpful for past projects. 

_Note: Arguments listed in example snippets below are not necessarily the arguments as named in the jQuery document linked above. I may choose personal clarity over official syntax in some cases._

### jQuery Objects

A jQuery call returns a jQuery Object that contains the selected HTML element(s) as entries with indexed keys along with a few other keys: `context`(DOM node context originally passed, often document), `length`, `selector`, and `__proto__`.

```
const exampleHeader = $('#returning-empty-set');

>> jQuery.fn.init [ h4#returning-empty-set, context: document, selector: "#returning-empty-set"]

exampleHeader[0];
>> <h4 id='returning-empty-set'>Returning an Empty Set</h4>


exampleHeader.context
>> #document // returns a copy of the DOM

exampleHeader.length
>> 1

exampleHeader.selector
>> '#returning-empty-set'

exampleHeader.__proto__
>> // JQuery Object protoype

```

JQuery functions will only work on jQuery objects, so if you want to use `.html()` to get or set the HTML of an element, the function call must be chained to the entire jQuery object, not the element itself. In the case you need to get/set the HTML of an element, perhaps while iterating over a jQuery object, use `.innerHTML`.

```
<p>Hello, World</p>


//  proper use of JQuery.html()
$('p').html()
>> "Hello, World"

// Calling $('p')[0] will return the p element so a call to .html() will fail, here use .innerHTML
$('p')[0].innerHTML;
>> "Hello, World"

```

Need an array instead of an object? There are two ways to convert a jQuery object into a plain 'ol Javascript Array: `$.makeArray()` and `.toArray`.

```
<ul>
 <li>Foo</li>
 <li>Bar</li>
</ul>

// Using $.makeArray()

const foobar = $('li');
$.makeArray(foobar);
>> [<li>Foo</li>, <li>Bar</li>]

// Using .toArray()
$('li').toArray();
>> [<li>Foo</li>, <li>Bar</li>]

```

The converted object is a regular Javascript array, so jQuery-specific methods will no longer work on it.

### Selectors

Selecting elements in the DOM with jQuery is easy, just wrap a CSS selector in `$( )`, which is shorthand for `jquery()` : 

- **Elements**  `$('p')`
- **Class** `$('.klassy')`
- **ID** `$('#blessed')`
- **Combinators** `$('p + a')`
- **Attributes** `$('td[data-x="3"]')`

### Dataset Attributes

As I work through the AJAX section of the bootcamp, the HTML `dataset` attribute has been getting a hell of a workout. `dataset` attributes are any attribute that is prefixed with `data-`. jQuery has some special functions to deal with them.

#### `.data()`

`.data()` is getter/setter method for the `dataset` attribute of a element. It's chained to a  jQuery selector/object and depending on the arguments passed in it will get or set  a data attribute or return the entire `dataset` object. All arguments are optional.

```
// without arguments it gets the entire dataset object
$('td').data();
>> { } // if the dataset attribute is empty an empty array is returned 
>> { id: 4 } // if there are dataset attributes they are returned 

// to retrieve a particular dataset value, pass in the associated key
$('td').data('id');
>> 4

// to set an attribute pass in the key and value
$('td').data( 'language', 'english');
>> { id: 4, language: 'english' }

```


#### `.removeData()`

Pretty self-explanatory. Calling `.removeData()` will clear the dataset attribute.

### AJAX Requests


#### `$.get()`: GET Request

Sending a GET request with jQuery, only the URL is required, but a success callback will probably always be included. Otherwise, what's the point if you're not going to use the data? The datatype attribute defaults to making an intelligent guess: xml, json, script, text, html.

`$.get(urlOrRelativePathOfResource, dataYouWantToSend, successCallback, dataTypeOfExpectedResponse)`

```

$.get('/posts', function(data) { console.log(data) });

>> { 'id': 4, 'title': 'My First Post', 'content': "This is my first post. How am I doing?" } // data in this case as JSON

```


#### `$.post()`: POST Request

Post is structured the same way as `$.get( )`:

```

$.post('/posts', function(data) { console.log(data) });

>> { 'id': 5, 'title': 'My Second Post', 'content': "This post was created using AJAX!" } // data returned is the object just created by the POST request as JSON

```


#### `$.ajax()` : PATCH Request

The `$.ajax()` is the lower-level call to make an AJAX request using jQuery -- shorthand methods for GET and POST are below. The `$.ajax()` call allows you set more arguments than the shorthand methods, including headers and request types. There is no shorthand call for PATCH, so a PATCH request must be sent using `$.ajax()`.

```

$.ajax( { url: '/posts', data: { 'id': 5, 'title': 'My Second Post', 'content': 'This post was updated using AJAX! ' }, type: 'PATCH' , success: function(data) { console.log(data) });

>> { 'id': 5, 'title': 'My Second Post', 'content': 'This post was updated using AJAX! ' }
```

Other settings are listed in jQuery's documentation for [`$.ajax( )`](https://api.jquery.com/jQuery.ajax/).


### Events and Turbolinks

A lot of the items above are really only helpful when called in conjunction with event listeners, like a form that listens for a submit request before using `$.post()` to send data to an API. They're what make the modern web so interactive and seemless. 

#### Attaching Event Listeners

jQuery has [a ton of shortcuts](https://api.jquery.com/category/events/) for event listeners, but let's start with the basic jQuery call, `.on()`. 

`.on()`, like all jQuery functions, must be called on a jQuery object like so: `$('#some-dom-element-id').on('someEventToListenFor', ( ) => { // an anonymous callback function that handle the event })`. What this line does is attach an event listener (`someEventToListenFor`) to the DOM element with an id of `#some-dom-element-id`. When that particular event is heard, the callback function is triggered. Here's a modified example from my Rails/jQuery project:

```
$('#comments-submission-form').on('submit'', function(e){
 // stop the form from submitting normally
 e.preventDefault();
 // grab the value of the textarea within the form
 const data = $('textarea').val();
 // grab the dataset value with a key of recordid - in HTML this would be the attribute data-recordid
 const id = $('.show.pettings').data('recordid');
 // send data to internal API route that creates a new comment with a POST request 
 const posting = $.post(`/api/pettings/${id}/comments`, { 'petting_id': id, 'content': data });
 // when the post is completed take the response data (a comment JSON object) and run a callback function
 posting.done(function(data){
  // prepend data (add to the top of the div#comments-collection) 
  $('#comments-collection').prepend(`<div class='comment-card'>${data.content}</div>`);
	});
 });

```

This particular example uses `'submit'` but you can call any of the event listeners you would normally call with `.addEventListener()`: 
-  `click`
-  `mouseover`
-   `keypress`
-   `scroll`
-   `resize`

... and so one. Just make sure they're passed into `.on()` as strings. Of course, jQuery also has a number of short cuts that bypass the `.on( )` function in favor of calling the event directly: 
-  `$('#comments-submission-form').click(function(e) { //callback })`
-  `$('.js-next-btn').click(function(e) { //callback})`
-  `$('input#telephoneNumber').keypress(function(e) {//callback})`

But it's good to know our lower level calls. Especially when you get to Event Delegation.

#### Event Delegation and Turbolinks

When I was just starting my jQuery/Rails project for Flatiron, I started running into a problem where my API calls were not firing when I navigated to a page. Everything was working correctly, from my API routes, to my serializers, to my jQuery `$.get( )` requests ( and they were all GET requests at that time). And I knew this because everything loaded and displayed correctly if I refreshed the page. But only if I refreshed the page, not when I navigated there from an outside link. Something had to be done because forcing a user to refresh the page after each link was just crappy UX/UI. 

So what the hell was going on? 

The answer was [Turbolinks](https://github.com/turbolinks/turbolinks), an actually quite helpful JavaScript library that makes navigating your site quicker:

>Turbolinks makes navigating your web application faster. Get the performance benefits of a single-page application without the added complexity of a client-side JavaScript framework. Use HTML to render your views on the server side and link to pages as usual. When you follow a link, Turbolinks automatically fetches the page, swaps in its `<body>`, and merges its `<head>`, all without incurring the cost of a full page load.

Like I said, helpful, but it was interfering with how I was attaching my event listeners and API calls. Essentially, since Turbolinks was avoiding a full-page refresh, it was interfering with `$(document).ready( )`, the traditional event on which we attach our event listeners, API calls (in my case), and compile our javascript files. So how do we take advantage of Turbolinks' speed advantage while still having all of that JavaScript functionality? 

By listening for the `turbolinks:load` load event and using event delegation.

**`turbolinks:load`**

You can read a little more about `turbolinks:load` in the Turbolinks README linked above, but for my cheatsheet purposes it's kind of replacing `$(document).ready( )` in that `turbolinks:load` event fires when the page is loaded using Turbolinks. You can attach a listener like so: 

`$(document).on('turbolinks:load', function(){ //callback })`

A couple notes before the more practical example taken from my jQuery/Rails project: 
- The domain of my project was an app wherein users can keep track of the dogs they pet. The term `pettings` or `Petting` will show up in the example below and some subsequent examples -- it represents a time a user pet a dog. 
- All of my JavaScript was compiled into one document in my Rails asset pipeline so my example will make use of conditional statements to ensure that API calls are only triggered on specific pages. I wrapped all of a pages' content in a `div` with specific classes that referred to the object model that was being populated and the RESTful route that the page represented. Though you will see the class `home` below as it was the homepage or `root_path` in Rails parlance. 


```
// attach the 'turbolinks:load' event listener to $( ) which is shorthand for document.ready
$(function(){

// with this event attached we can listen for the turbolinks:load event each time a user navigates our site
 $(document).on('turbolinks:load', function(){
 
  // a condition that ensures the $.get( ) call to the API pettings index endpoint is only called if a div with the classes home and pettings is present
	// if it is not present this expression evaluates to 0 which is falsey in JS
  if ($('.home.pettings').length){
	
	 $.get('/api/pettings', function(data){
	 
	  // pass the data to a static method in Petting JavaScript class to create a collection of pet cards, which are already formatted into HTML 
	  const pettingCards = Petting.createPetCardCollectionFromJSON(data)
		
		// add all that HTML to div#petting-cards-container
		$('#petting-cards-container').html(pettingCards);
    });
  };
});

```

WIth that, my API calls were now firing when I navigated to the pages that required them. 

The next issue I ran into was adding other event listeners like `submit` and `click` to my pages while still getting around Turbolinks. Initially, I thought I could ride the `turbolinks:load` event wave for all my event listener needs. But Turbolinks actually argues against this, so I figured I should listen to them and use their suggested workaround: [Event Delegation](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events).

**Event Delegation**

Put simply, event delegation, means you place an event listener on Element A (normally `document`) and ask it to listen for an event that originated or bubbled up from its child, Element B (a form or button, for example). The advantages of using event delegation, even beyond getting around `turbolinks:load`, is that you can attach all of your listeners at once (on `document` in this case) so they're available across your happ. But since they are listening for where the event originated from, you can still control what gets called. 

So how do you delegate events? By using jQuery's `.on( )` function with a third (or second, I guess) argument. Reusing the `.on( )` example from above: 

`$(document).on('someEventToListenFor', ' #theElementWhereTheEventOriginates', ( ) => { // an anonymous callback function that handle the event })`

What this example is saying: _Hey, `document`, would you please run this anonymous callback function IF AND ONLY IF A) you hear the `someEventToListenFor` event and that event 
B) bubbled up from an element with the id `#theElementWhereTheEventOriginates`_

This works because every DOM element is a child of `document`. If you place your delegated an event listener to an element besides `document`,  you must make sure it is a parent of the target element. 

Finally, a practical example taken from the form submission example above, but now with event delegation:

```
$(document).on('submit'', '#comments-submission-form', function(e){

 // stop the form from submitting normally
 e.preventDefault();
 
 // grab the value of the textarea within the form
 const data = $('textarea').val();
 
 // grab the dataset value with a key of recordid - in HTML this would be the attribute data-recordid
 const id = $('.show.pettings').data('recordid');
 
 // send data to internal API route that creates a new comment with a POST request 
 const posting = $.post(`/api/pettings/${id}/comments`, { 'petting_id': id, 'content': data });
 
 // when the post is completed take the response data (a comment JSON object) and run a callback function
 posting.done(function(data){
 
  // prepend data (add to the top of the div#comments-collection) 
  $('#comments-collection').prepend(`<div class='comment-card'>${data.content}</div>`);
	});
});

```

Easy-peasy. Just refactor that first line and you're done.
