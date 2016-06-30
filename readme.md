# Final Lesson Before Projects: a couple of Loose Ends


# A Couple More Things to Say About `this`...

Remember how we talked about the four ways that `this` is determined?

1) By default, in the global context or inside any function, `this` refers to the global object, the `window` object.

2) If you are running a function as a method on an object -- meaning using the dot syntax or the bracket syntax, like this:

```js
myObject.foo()
// or...
myObject["foo"]()
```

then inside the body of the function `foo`, `this` refers to `myObject`.

3) If you are running a function with the `new` operator (i.e. running it as a constructor function), then inside that constructor, `this` refers to the new object that will be returned from the constructor.

4) Lastly, you can explicitly set the value of `this` within the running of a function, using some functions called `call`, `apply`, and `bind`. 

***Trick Question:*** Which of those categories does this example fall into?

```js
$('#some-element').on('click', function() {
  var message = $(this).html();
  alert(message);
});
```

***Answer:*** Under the hood, what's going on here is our old friend `element.onclick = function() {...}` :

```js
document.getElementById('#some-element').onclick = function() {
  var message = this.innerHTML;
  alert(message);
}
```

So we're basically adding a method -- `onclick` to the DOM element object, and when the user clicks on the button, the browser runs that method -- essentially by running `element.onclick()`, so the answer is category 2 above: running a method on an object, where the object is the DOM element.



***Even Tricker Question:*** Which of those categories does this example fall into?


```js

$('#some-element').fadeOut(2000, function() {
  $(this).fadeIn(3000);
});
```

***Answer:*** This is jQuery magic. Every time you run a jQuery function on a jQuery object, you always get a `this` which refers to all of the elements in the jQuery object, in turn. This is basically just because jQuery decided it wanted you to be able to do that, and it uses `.call()` internally to specify the context. So the answer is, under the hood, category 4 above: specifying the context with `.call()`

The larger moral here is that often when using jQuery, you'll find that `this` refers to something convenient, but you'll have to remember what it is, and it won't necessarily be the same as the way `this` is set in vanilla JS, or in other libraries.

# The 'Mixed Content' problem.

## What encrypted transmissions are

What’s happened is this: for a long time, encrypted transmissions of html, javascript and css pages over the internet (“https”) has been a much better idea than unencrypted transmissions (“http”). The unencrypted ones were vulnerable to many more of what are called “man-in-the-middle” attacks, like people sitting in coffee shops listening in on your the transmission of your password to your bank, over the coffee shop WiFi network 

- This is really easy to do — Google “Wireshark coffee shop security”.

## Mixed Content

But if you (the user) have your browser request a page that is delivered to you encrypted (meaning, the url for the html page you’re requesting begins with `https://`), and when that page renders in your browser, it runs some JavaScript that makes an Ajax request for some data that will NOT be encrypted (meaning, the url for the Ajax request begins with `http://`), then you’ve made yourself vulnerable to security breaches, and you might as well not have used encryption to begin with. This is called 'Mixed Content'

For this reason, there’s long been a push to have browsers actually block this kind of situation, which would force all hosting providers to enable the serving of their pages over `https`, otherwise their content would be disabled if someone tried to access it from a page that HAD been served over `https`.

Because this was unreasonable at first (a lot of major websites were dragging their heels and still not serving via https — because they hadn’t gotten around to it, but also because it is a bit more involved, and requires a bit more resources and configuration), they had warnings in the browser for a long time about “Mixed Content” — content being served to the same page using two different security protocols, which makes the whole session as insecure as the least secure content that was served.

## Recent developments

But it appears that in the past year or so, because a critical mass of websites and APIs can now serve content via `https` (some of them respond to both `https` and `http` requests, although obviously things like banking websites don’t respond to `http` requests, and never have), all the browsers have switched over to a system where instead of a warning, your browser actually blocks Mixed Content.

So why did we not see this problem until now? Because it doesn’t affect html pages you just open as files on your computer. You have to be requesting them from a server, either locally on your computer (`http://localhost:3000`) or from an app you've deployed on Github or Firebase, both of which give you no option but to serve pages via `https` (as of two weeks ago, in the case of Github).

## Openweathermap is one example of a site that might cause you problems

Openweathermap seems to have gone the route of offering `https` access, but charging for it. I’ve never heard of that.

Anyway, there’s a way around this, but the user has to take an action: they have to click a shield icon in the url bar, to allow mixed content. So you have two options if you host your site on Github or Firebase and want to access an API that is only served via `http` (or in the case of openweathermap, it’s only served _free_ via `http`, and you don’t want to pay for it):

1. Contact the makers of the API and get them to start serving their pages over `https` (or do so without charging), or

2. Put a notice in your site explaining to your users that they’re going to have to click that shield icon in the url bar in order to see all the content they’re supposed to see.



## So, what should we do?

1. You’ll have to change the urls for all your api requests from `http` to `https`, and 

2. If the api doesn’t support that (like openweathermap), then leave it as `http` and you’ll have to tell your users that they’re going to have to click the shield icon that will appear in their url bar, if they want to view the content. 



[Here’s a good page from Indiana University](https://kb.iu.edu/d/bdny) explaining the problem to users, and telling them that sometimes, they’re just going to have to click that shield icon:




And here’s some other background info:

[Blog post on Mixed Content](http://www.howtogeek.com/181911/htg-explains-what-exactly-is-a-mixed-content-warning/)

[Mozilla explanation of mixed content](https://developer.mozilla.org/en-US/docs/Web/Security/Mixed_content#Mixed_passive.2Fdisplay_content)
