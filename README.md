# Reflekt.js

Reflekt is a logic-free, noMustache template engine for javascript. Reflekt is inspired by [pure.js](http://beebole.com/pure/), [plates](https://github.com/flatiron/plates/), [MTE](http://mootools.net/forge/p/moo_template_engine), [tmpl.js](https://zealdev.wordpress.com/2008/02/22/mootools-template-engine-a-new-approach/), [AngularJS](https://angularjs.org/), [tempan](https://github.com/watoki/tempan), [Weld](https://github.com/tmpvar/weld) and [DOMTemplate](http://camendesign.com/code/dom_templating).

> Reflekt is currently in development and in its very early stages. This is a preview of what Reflekt is going to look like.

## Example

The template:
```html
<div class="name">Artist</div>
```
The first step is to provide an object to tell Reflekt to which element it should bind to. The object is a simple json object which might look like this:

```js
var bind = {
  ".name": { "bind":"name" }
}
```

Our data object:
```js
var data = {
  name : "John Lellon"
}
```

How to render it? Pretty straight forward:
```js
var r = Reflekt(bind);
r(data);
```

The rendered template will look like this:
```html
<div class="name">John Lellon</div>
```


## Core Methods

```js
r(); // get data
r("name"); // get a specific data key

// set data
r({ name:"Mike McCartney" }); 
// or
r("name", "Mike McCartney");

// computed properties
r("fullName", function(){
  return r("firstName") +" "+ r("lastName");
});
// Reflekt tracks dependencies and automatically updates fullName
// whenever firstName or lastName changes.

// load data
r.get(url)
  .success(function(scope){})
  .fail(function(scope){});

// create a custom filter
// { bind: "name | reverse" }
r.filter('reverse', function(input, arg1){
  return input.split('').reverse().join(arg1!==undefined?arg1:'');
});
```


#### Repeat An Element

Simply provide an array and Reflekt will repeat the element &nbsp; ; )

```js
r("name", ["Mike McCartney", "John Lellon"]);
```

```html
<div class="name">Mike McCartney</div>
<div class="name">John Lellon</div>
```
-

## Extended Example

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
var bind = {
  "h1": { "bind":"album" },
  "a" : { 
    "bind":"artist | uppercase", 
    "attr-href":"link" 
  },
  "li": { 
    "bind":"$index +' '+ songs.title", 
    "if":"songs.year <= 1968"
  }
}
```

The data:
```js
var data = {
  album : "Greatest Hits",
  artist: "The Beatles",
  link  : "http://www.thebeatles.com/",
  songs : [
    { title : "Hey Jude", year : 1968 },
    { title : "A Day in the Life", year : 1967 },
    { title : "Let It Be", year : 1970 }
  ]
}
```

The javascript:
```js
Reflekt(bind)(data);
```

Output:
```html
<h1>Greatest Hits</h1>
<p>By <a href="http://www.thebeatles.com/">THE BEATLES</a></p>
<ul>
  <li>1. Hey Jude</li>
  <li>2. Let It Be</li>
</ul>
```
-

If you want to assign data to a child element but loop its parent, like so:

```html
<li><a href="link">1. Hey Jude</a></li>
<li><a href="link">2. Let It Be</a></li>
```
You assign the values to `a` but repeat `li`. Then your binding object needs to look like this:
```js
{
  "li": {
    "bind":"songs",
    "a": { 
      "bind":"title", 
      "attr-href":"link" 
    }
  }
}
```

## Backend Rendering

Ok, but what if you're a backend guy? Good news, you wouldn't have to change a thing – not your template nor the binding object! (Exeptions are custom filters)

```php
echo Reflekt('index.html', 'bind.json')->set($data);
```

## Directives

A directive is basically an instruction to attach a specific behavior on a DOM element. For instance `{ "bind": "items"}` will assign the value of the property `items` from the data object `{ "items": [] }` to the innerHTML of an element.

|Key    |Example|Description|
|:------|:------|:----------|
|bind   |{ bind: "items\|filter()" }|Assign data to the text value of a node| 
|if     |{ if: "items.length > 5" }|Bind only if condition applies|
|repeat |{ repeat: "item:items" }|Explicitly repeat a given item|
|attr-* |{ attr-href: "items" }|Set the value of an attribute|
|on-*   |{ on-click: function(){} }|Bind an event handler function to a node|
