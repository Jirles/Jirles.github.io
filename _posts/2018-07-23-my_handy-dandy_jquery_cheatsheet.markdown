---
layout: post
title:      "My handy-dandy jQuery Cheatsheet"
date:       2018-07-23 22:31:54 +0000
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

