#Reflekt.js

Reflekt is a logic-free, noMustache template engine for javascript. Reflekt is inspired by [pure.js](http://beebole.com/pure/), [plates](https://github.com/flatiron/plates/), [MTE](http://mootools.net/forge/p/moo_template_engine), [tmpl.js](https://zealdev.wordpress.com/2008/02/22/mootools-template-engine-a-new-approach/), [tempan](https://github.com/watoki/tempan), [Weld](https://github.com/tmpvar/weld) and [DOMTemplate](http://camendesign.com/code/dom_templating).

> Reflekt is currently in development and in its very early stages. This is a preview of what Reflekt is going to look like.

##Example

The template:
```html
<div class="name">Artist</div>
```
The first step is to provide an object to tell Reflekt to which element it should bind to. The object is a simple json object which might look like this:

```js
var _bind = {
  ".name" : "name"
}
```
The key on the left side is a simple CSS selector. On the right side is the actual key or directive which will be attached to our DOM element so that it easily can be addressed later on. 

Our data object:
```js
var _data = {
  name : "John Lellon"
}
```

How to render it? Pretty straight forward:
```js
var r = Reflekt({
  bind : _bind,
  data : _data
});
```

The rendered template will look like this:
```html
<div class="name">John Lellon</div>
```

By default, Reflekt applies the data to the elements body. To apply it to an attribute we add the `@` at the end of the selector and then the name of the attribute:
```json
{
  "#id@class" : "class",
  "#id@style.color" : "color"
}
```

####Change Data

```js
r.set({
  name : "Mike McCartney"
});
```

####Key Specific Methods

```js
r.$name(); // get data
r.$name("Mike McCartney"); // set data

// prepend/append data
r.$name.prepend("John Lellon");
r.$name.append("John Lellon");

// modify data before applying
r.$name.filter(function(data, index){});

// observe if data has been changed
r.$name.observe(function(obj){});

// attach an event handler function
r.$name.on('click', function(e){});
```


####Repeat An Element

Simply provide an array and Reflekt will repeat the element &nbsp; ; )

```js
r.$name(["Mike McCartney", "John Lellon"]);
```

```html
<div class="name">Mike McCartney</div>
<div class="name">John Lellon</div>
```
-

##Extended Example

The template:
```html
<h1>Album</h1>
<p>By <a>Artist</a></p>
<ul>
  <li>Song</li>
</ul>
```

The binding object:
```js
var _bind = {
  "h1" : "album",
  "a"  : "artist",
  "a@href" : "link",
  "li" : "song.title"
}
```

The data:
```js
var _data = {
  album : "Greatest Hits",
  artist: "The Beatles",
  link  : "http://www.thebeatles.com/",
  song  : [
    { title : "Hey Jude", year : 1968 },
    { title : "A Day in the Life", year : 1967 },
    { title : "Let It Be", year : 1970 }
  ]
}
```

The javascript:
```js
var r = Reflekt({ bind : _bind});

// modify data before rendering
r.$song.$title.filter(function(title, index){
  return index +". "+ title;
});

r.set(_data);
```

Output:
```html
<h1>Greatest Hits</h1>
<p>By <a href="http://www.thebeatles.com/">The Beatles</a></p>
<ul>
  <li>1. Hey Jude</li>
  <li>2. A Day in the Life</li>
  <li>3. Let It Be</li>
</ul>
```
-

If you want to assign data to a child element but loop its parent, like so:

```html
<li><a href="link">1. Hey Jude</a></li>
<li><a href="link">2. A Day in the Life</a></li>
```
You assign the values to `a` but repeat `li`. Then your binding object needs to look like this:
```js
{
  "li" : {
    "a" : "song.title"
    "a@href" : "song.link"
  }
}
```

####Map Keys

Sometimes you might not be sure how your final data object will look like. To fix this problem, so that you don't always have to change the name of your key specific methods, you can use `->` in your binding object to do the mapping, like so:

```js
var _data = {
  tmp : "Bill Murray",
};

var _bind = {
  "h1" : "tmp -> key"
}

r.$key(); // "Bill Murray"
```
