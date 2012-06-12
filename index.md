# Backbone.js - Why single page applications are powerful

!SLIDE

# Backbone.js
## Why SPA are powerful

!SLIDE

# About me

Romain Philibert

@Atos since 1 year

PEPS - BSD

!SLIDE

# SPA ?
## === Single Page Application

``` htmlmixed
<!doctype html>
<html>
<head>
  <link rel="stylesheet" href="app.459703.css">
</head>
<body>
  <div role="main"> </div>

  <script src="app.6423e0.js"></script>
</body>
</html>
```

!SLIDE
# URL History

## Classic:

Url:

    http://company.ext/page1
    http://company.ext/page2/subpage

Html:

``` htmlmixed
<a href="page1">Click on me</a>
```

!SLIDE
# URL <span style="text-decoration: line-through;">History</span>

## In JS

Url:

    http://company.ext/
    http://company.ext/


!SLIDE
# URL <span style="text-decoration: line-through;">History</span>

## In JS

Html:

``` htmlmixed
<a class="link1" href="#">Click on me</a>
```

JS:

``` javascript
$('a.link1').click(function(e){
  e.preventDefault();
  $('#main').load('http://company.ext/partials-1.html');
})
```

!SLIDE
# URL History

## In JS with an hashUrl Router

Url:

    http://company.ext/#page1
    http://company.ext/#page2/a-sub-page

Html:

``` htmlmixed
<a href="#/page1">Click on me</a>
```

!SLIDE
# URL History

## In BB with an hashUrl Router

``` javascript
var App = Backbone.Router.extend({
  routes: {
    "page1":               "page1",
    "page2/:sub":          "page2"
  },

  page1: function(){
    $('#main').load('/partials-1.html')
  },
  page2: function(subPage){
    $('#main').load('/sub/' + subPage + 'html')
  }
});

Backbone.history.start()
```

!SLIDE
# URL History

## In JS with a pushState Router

Url:

    http://company.ext/page1
    http://company.ext/page2/a-sub-page

Html:

``` htmlmixed
<a href="/page1">Click on me</a>
```

!SLIDE
# URL History

## In BB with a pushState Router


``` javascript
var App = Backbone.Router.extend({
  //same than before
});

Backbone.history.start({pushState: true})
```

!SLIDE
# Separation of concerns

* KLOC of Javascript mixed with Server side rendering.

![Quite hard](http://images.lifesambrosia.com/food/large/spaghetti-carbonara.jpg)


!SLIDE long
# Separation of concerns

MIDLLE === WebService REST

![REST Webservice](http://www.crummy.com/writing/RESTful-Web-Services/cover.png)

FRONT === Serving static files: html, css, js

![Nginx](http://blog.dev-art.fr/wp-content/uploads/2012/03/nginx_php1.gif)


!SLIDE
# Separation of concerns


* [templating]()
* [model]()
* [view]()
* [modularize your code]()
* [communicate between instances]()
* [instances life cycle]()
* [synchronize views with models]()
* [memory leaks]()
* [optimize your assets for production]()
* [SEO]()
* [i18n]()
* [test]()



!SLIDE template
# Template

What is a template:

``` htmlmixed
<div class="entry">
  <h1>{{title}}</h1>
  <h2>By {{author.name}}</h2>
  <div class="body">
    {{body}}
  </div>
</div>
```

Datas

``` javascript
{
  title: "My First Blog Post!",
  author: {
    id: 47,
    name: "Yehuda Katz"
  },
  body: "My first post. Wheeeee!"
};
```

!SLIDE
# Template

Compile and populate

``` javascript
var template = Handlebars.compile(templateStr);
$('#main').html(template(data)}
```

And we obtain this

``` htmlmixed
<div class="entry">
  <h1>My First Blog Post!</h1>
  <h2>By Yehuda Katz</h2>
  <div class="body">
    My first post. Wheeeee!
  </div>
</div>
```

!SLIDE
# Template

Not in Backbone


* [handlebars ❤](http://handlebarsjs.com/)
* [jquery-tmpl](http://api.jquery.com/jquery.tmpl/)
* [underscore](http://documentcloud.github.com/underscore/#template)
* [dust](http://akdubya.github.com/dustjs/)
* [mustache with hogan](http://twitter.github.com/hogan.js/)
* ...

!SLIDE
# Model

We need data !

``` javascript
$.getJSON('ajax/test.json', function(data) {});
```

!SLIDE
# Model
## Request each time

``` javascript
function render(){
  $.getJSON('ajax/test.json', function(data) {
    $('main').html(template(data));
  });
}
```

!SLIDE
# Model
## Use a cache

``` javascript
$.getJSON('ajax/test.json', function(data) {
  window.data = data;
}

function render(){
    $('main').html(window.data);
}
```

!SLIDE
# Model
## Use a cache

* great for mobile
* gret for performance
* Cache invalidation is hard

!SLIDE
# Model
## In Backbone


Create a model, and fetch the data

``` javascript
window.MyModel = Backbone.Model.extend({
  url: 'ajax/test.json'
});
window.myModel = new MyModel();

myModel.fetch().fail(function(){
  console.error('Error to fetch')
}).then(function(){
```

!SLIDE
# Model
## In Backbone

Use the model a lot.

``` javascript
}).then(function(){

  myModel.get('title');
  myModel.get('title');
  myModel.get('title');
  myModel.get('title');
```

No requests are sent

!SLIDE
# Model
## In Backbone

we update the data.

``` javascript
myModel.set({
  title: "My First Blog Post!",
});

myModel.save().fail(function(){
  console.error('Error to save');
});
```

!SLIDE
# Model
## In Backbone


Without waiting for the answer.

Continue using the model a lot

``` javascript
myModel.get('title');
myModel.get('title');
myModel.get('title');
myModel.get('title');
```

!SLIDE
# Model
## In Backbone


You only wait for 1 request.


!SLIDE
# View
## DOM Maniuplation

``` htmlmixed
<div class="view-1">
  <div class="a-thing"></div>
  <input type="button" value="Click on me" />
</div>
```

``` javascript
$('input').click(function(){
  $('.a-thing').text('Hello World');
});
```
!SLIDE

In real application, it looks more like this:

``` htmlmixed
<div class="view-1">
  <div class="a-thing"></div>
  <input type="button" value="Click on me" />
</div>
<!-- ... -->
<div class="view-2">
  <div class="something">
    <div class="a-thing">
      <h1>Hello you</h1>
    </div>
  </div>
</div>
```

``` javascript
$('input').click(function(){
  $('.a-thing').text('Hello World');
});
```

!SLIDE
# View
## We need DOM isolation

A view in Backbone:

``` javascript
var View1 = Backbone.View.extend({
  events: {
    'click button': 'onClick'
  },

  onClick: function(e){
    this.$('.a-thing').text('Add something');
  }
});

var view1 = new View1({el: $('.view1')});
```

!SLIDE
# Modularize your code

* RequireJS
* With a Namespace

!SLIDE
# Modularize your code
## Create Namespace

Create a namespace

``` htmlmixed
<script src="namespace.js"></script>
```

``` javascript
window.xx = {};
xx.view = {};
xx.model = {};
```


!SLIDE

# Modularize your code
## Extend the namespace


``` htmlmixed
<script src="view/todo/TodoView.js"></script>
```

``` javascript
xx.view.TodoView = Backbone.View.extend({
  template: JST.todo,

  events: {
    'click .refresh': 'render'
  },

  render: function() {
    this.$el.html(this.template({key: "value"}));
    return this;
  }
});
```

!SLIDE

# Communication
## Create an app instance

``` htmlmixed
<script src="model/User.js"></script>
```

``` javascript
xx.model.User = Backbone.Model.extend({
  url: 'http://company.ext/ajax/data.json'
});
xx.model.user = new xx.model.User();
```

!SLIDE

# Communication
## Communicate with it


``` htmlmixed
<script src="view/todo/TodoView.js"></script>
```

``` javascript
xx.view.TodoView = Backbone.View.extend({

  render: function() {
    this.$el.html(template({
      user: xx.model.user.toJSON()
    }));
    return this;
  }
});
```

!SLIDE

# Communication
## life cycle === app

Communication with app instances ✔


!SLIDE

# Communication
## Container View:

``` htmlmixed
<script src="view/todo/TodoView.js"></script>
```

``` javascript
xx.view.ContainerView = Backbone.View.extend({
  template: JST.todo,

  initialize: function(){
    this.user = new xx.model.User();
    this.subView = new xx.view.SubView({
      parent: this
    });
  }
});
```

Pass container references to sub views.

!SLIDE

# Communication
## Sub Views

``` javascript
xx.view.SubView = Backbone.View.extend({
  template: JST.todo,

  initialize: function(options){
    this.parent = options.parent;
  },

  render: function() {
    this.$el.html(this.template({
      user: this.parent.user.toJSON()
    }));
    return this;
  }
});
```

!SLIDE
# Communication
## life cycle === view

Communication with view instances ✔

!SLIDE
# Communication
## Model ⇝ View

Based on events

* change
* change:title
* reset
* add
* remove

jQuery like method:

* model.on
* model.off
* model.trigger

!SLIDE
# Communication
## Model ⇝ View
``` javascript
var View1 = Backbone.View.extend({
  initialize: function(){
    xx.model.user.on('change', this.render);
  },

  render: function() {
    this.$el.html(this.template({
      user: xx.model.user.toJSON()
    }));
    return this;
  }
});
```
``` javascript
    xx.model.user.set({name: 'Romain'});
```

!SLIDE
# Communication
## Model ⇝ View

``` javascript
    xx.model.user.set({name: 'Romain'});
```

several events are triggered: `change`, `change:name`

``` javascript
    xx.model.user.on('change', this.render);
```

You can listen different events: `all`, `change`, `change:name`

!SLIDE
# Memory leak
## Event Listener ⇒ Memory leak

Create a dispose method

``` javascript
var View1 = Backbone.View.extend({
  initialize: function(){
    xx.model.user.on('change', this.render);
  },

  dispose: function(){
    xx.model.user.off('change', this.render);
  }
};
```
Call `dispose` manually.

!SLIDE leak
# Memory leak
## Event Listener ⇒ Memory leak

Or do it automatically with [Backbone.dispose](https://github.com/Filirom1/Backbone.dispose) plugin

``` javascript
new View1({el: $('.div1')});

$('.div1').remove(); //dispose called
$('.div1').html('<div/>'); //dispose called
```

![leak](https://github.com/Filirom1/Backbone.dispose/raw/master/draft.png)


!SLIDE
# Optimize for prod
## Concat, minify, md5

Transform this

``` htmlmixed
<script src="vendor/log.js"></script>
<script src="vendor/underscore.js"></script>
<script src="vendor/backbone.js"></script>
<script src="vendor/handlebars.js"></script>
<script src="namespace.js"></script>
<!-- ... -->
<script src="templates.js"></script>
<script src="view/todo/TodoView.js"></script>
<script src="Router.js"></script>
```

into this

``` htmlmixed
    <script src="app.6423e0.js"></script>
```

!SLIDE
# Optimize for prod
## Even more

* [coffee-script](http://coffeescript.org/)
* [less-css](http://lesscss.org/)
* [optipng](http://optipng.sourceforge.net/)
* [jpegtran](http://jpegclub.org/jpegtran/)
* minify, concat, md5 your js
* minify, concat, md5 your css
* [css-b64-images](https://github.com/Filirom1/css-base64-images)
* [minify html](https://github.com/kangax/html-minifier)


!SLIDE
# Optimize for prod
## Build tools


Java: [Wro4J](http://code.google.com/p/wro4j/)

NodeJs:, [No-Build-Conf](https://github.com/Backbonist/no-build-conf-build-script), [h5bp build script](https://github.com/h5bp/node-build-script)


!SLIDE
# Modularize your templates
## Several HTML files

    .
    ├── helpers.hbs
    ├── multiple.hbs
    ├── partials.hbs
    ├── plain.hbs
    ├── sample.hbs
    └── subfolder
        └── subsub
            └── sample.hbs


!SLIDE
# Modularize your templates
## Precompiled into 1 JS file

``` htmlmixed
<script src="templates.js"></script>
```

Use it simply

``` javascript
$('#main').html(JST.sample({title: 'Hello World'}));
```

Instead of

``` javascript
$.get('/template/my-template.html', function(templateStr){
  var template = Handlebars.compile(templateStr);
  $('#main').html(template({title: 'Hello World'})}
})
```

!SLIDE
# SEO
## Search Engine Optimization

* Do not use SPA
* Proxify all requests to a headless browser

[spa-seo](https://github.com/Backbonist/spaseo)

☠ Not tested in production.

!SLIDE
# i18n
## internationalization

[en/colors.json](https://github.com/SlexAxton/messageformat.js/blob/master/example/en/colors.json)
``` javascript
{
  "red": "red",
  "blue": "blue"
}
```

[fr/colors.json](https://github.com/SlexAxton/messageformat.js/blob/master/example/fr/colors.json)
``` javascript
{
  "red": "rouge",
  "blue": "bleu"
}
```
[messageFormat](https://github.com/SlexAxton/messageformat.js)

!SLIDE
# i18n
## internationalization

Compile it

    messageformat --locale en ./en ./en/i18n.js

``` javascript
var locale =
  (localStorage.getItem('lang') || 'en')
  + '/i18n.js';

document.write(
  '<script src="' + locale + "><\/script>');
```

then use it:

``` javascript
    window.i18n.colors.red()
```

!SLIDE
# tests
## Unit tests

* [Qunit](http://docs.jquery.com/QUnit)
* [Jasmine](http://pivotal.github.com/jasmine/)
* [Mocha](http://visionmedia.github.com/mocha/)

Mocking tools: [Sinon.js](http://sinonjs.org/)

!SLIDE
# tests
## Functional tests

* [Selenium](http://seleniumhq.org/)
* [PhantomJs](http://phantomjs.org/)
* [Casper](http://casperjs.org/)
* [ZombieJS](http://zombie.labnotes.org/)

