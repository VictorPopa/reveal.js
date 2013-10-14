# Reveal.js [![Build Status](https://travis-ci.org/hakimel/reveal.js.png?branch=master)](https://travis-ci.org/hakimel/reveal.js)

A framework for easily creating beautiful presentations using HTML. [Check out the live demo](http://lab.hakim.se/reveal-js/).

reveal.js comes with a broad range of features including [nested slides](https://github.com/hakimel/reveal.js#markup), [markdown contents](https://github.com/hakimel/reveal.js#markdown), [PDF export](https://github.com/hakimel/reveal.js#pdf-export), [speaker notes](https://github.com/hakimel/reveal.js#speaker-notes) and a [JavaScript API](https://github.com/hakimel/reveal.js#api). It's best viewed in a browser with support for CSS 3D transforms but [fallbacks](https://github.com/hakimel/reveal.js/wiki/Browser-Support) are available to make sure your presentation can still be viewed elsewhere.


#### More reading:
- [Installation](#installation): Step-by-step instructions for getting reveal.js running on your computer.
- [Changelog](https://github.com/hakimel/reveal.js/releases): Up-to-date version history.
- [Examples](https://github.com/hakimel/reveal.js/wiki/Example-Presentations): Presentations created with reveal.js, add your own!
- [Browser Support](https://github.com/hakimel/reveal.js/wiki/Browser-Support): Explanation of browser support and fallbacks.

## Slides

Presentations are written using HTML or markdown but there's also an online editor for those of you who prefer a graphical interface. Give it a try at [http://slid.es](http://slid.es).


## Reveal.JS + PUBNUB

### What is PUBNUB?

 PubNub is a new kind of instantaneous Web Data Push Service for Mobile Phones, TVs, HTML5 Web Browsers, Tablets and Game Consoles. We believe in unified broadcasting and mass communication. Develop once and push everywhere. PubNub is a Global Web Data Push Service for Broadcasting Bidirectional Messages to Mobile, TV, Web, Tablet and Console simultaneously.

PubNub is considered a Web Push Engine able to push (or stream) any textual data in real-time data push to any types of clients across the Internet. PubNub solves all the complexity of pushing data in a scalable, secure, efficient, reliable and portable way. On the client side, APIs are provided so that it is very easy to build Bidirectional Data Push Apps (transforming your existing client or creating a new one from scratch). A powerful HTML5 library makes it possible to push data updates to Mobile Phones, Tablets and Web Pages in real-time data push through a zero-install client.

PubNub is also referred to as a web data push service with Reverse AJAX capabilities which allows you to send data to clients with BOSH Comet technology. This removes the need to constantly poll for new updates in the app. Other associated terms are: browser push, websockets, web data push, data push, real-time push and push notifications.

Objective-C, Java, PHP, JavaScript, Python, Ruby, Erlang, C#, .NET and more are provided for mobile phones and tablets such as Android and iPhone for Web Data Push. Every web browser is supported for web browser push.

Basically, PubNub can be considered a publish/subscribe middleware based on the standard HTTP 1.1 protocol.

Send messages from any device and receive messages on any device. From any device to any device. Send and receive messages using a Phone, TV, Car, Tablet, GPS Device, Game Console and Web. Send a message from one phone to another phone. Anything can receive messages. Anything can send messages. Broadcast messages to multiple devices simultaneously. Or broadcast a message to one device at a time.

PubNub provides client libraries for all major programming languages and mobile platforms to make web data push development as easy as pie. PubNub is tuned for gaming performance and will shine with any application. Build web-scale Data Push communication applications such as analytics, games, messaging, tweets and more! 

### How Hard is PubNub to Use?

#### Universal Function Calls

The PubNub API couldn't be simpler. There are two methods to know for each programming interface: 

#### Publish & Subscribe

Execute a Subscribe API call to begin listening for messages, automatically keeping the connection open until the application is closed. Subscribe API Call needs to be issued once per client.

Publish a message to send data simultaneously to all subscribed clients. Publish API Call needs to be issued only once. 

```javascript
// send events
PUBNUB.publish({
  channel: "hello_world",
  message: "data"
});


// listen to events
PUBNUB.subscribe({
  channel: "hello_world",
  callback: alert
}); 
```

### How Can I Use PubNub with Reveal.Js?

We can create a remote control interface or a SYNC ability to keep everyone on the same Slide Number.
In other words, we can add PubNub by making the slideshow aspect real-time by making each slide number sync between clients.

Every time the presenter changes the slide number in presentation mode, all clients are on the same slide number. 
They all follow the presenter.


#### PubNub CDN JavaScript SDK

You may access the latest PubNub JavaScript SDK on The PubNub Network CDN.

```html
<script src=http://cdn.pubnub.com/pubnub-3.5.1.min.js ></script>
```

#### Find out the slides show part in the reveal.js

In the reveal.js, all the slides are showed by slide() function.

```javascript
function slide( h, v, f, o ) {
	// Remember where we were at before
	previousSlide = currentSlide;

	// Query all horizontal slides in the deck
	var horizontalSlides = document.querySelectorAll( HORIZONTAL_SLIDES_SELECTOR );

	// If no vertical index is specified and the upcoming slide is a
	// stack, resume at its previous vertical index
	if( v === undefined ) {
		v = getPreviousVerticalIndex( horizontalSlides[ h ] );
	}

	// If we were on a vertical stack, remember what vertical index
	// it was on so we can resume at the same position when returning
	if( previousSlide && previousSlide.parentNode && previousSlide.parentNode.classList.contains( 'stack' ) ) {
		setPreviousVerticalIndex( previousSlide.parentNode, indexv );
	}

	// Remember the state before this slide
	var stateBefore = state.concat();

	// Reset the state array
	state.length = 0;

	var indexhBefore = indexh || 0,
		indexvBefore = indexv || 0;

	// Activate and transition to the new slide
	indexh = updateSlides( HORIZONTAL_SLIDES_SELECTOR, h === undefined ? indexh : h );
	indexv = updateSlides( VERTICAL_SLIDES_SELECTOR, v === undefined ? indexv : v );

	// Update the visibility of slides now that the indices have changed
	updateSlidesVisibility();

	layout();

	// Apply the new state
	stateLoop: for( var i = 0, len = state.length; i < len; i++ ) {
		// Check if this state existed on the previous slide. If it
		// did, we will avoid adding it repeatedly
		for( var j = 0; j < stateBefore.length; j++ ) {
			if( stateBefore[j] === state[i] ) {
				stateBefore.splice( j, 1 );
				continue stateLoop;
			}
		}

		document.documentElement.classList.add( state[i] );

		// Dispatch custom event matching the state's name
		dispatchEvent( state[i] );
	}

	// Clean up the remains of the previous state
	while( stateBefore.length ) {
		document.documentElement.classList.remove( stateBefore.pop() );
	}

	// If the overview is active, re-activate it to update positions
	if( isOverview() ) {
		activateOverview();
	}

	// Find the current horizontal slide and any possible vertical slides
	// within it
	var currentHorizontalSlide = horizontalSlides[ indexh ],
		currentVerticalSlides = currentHorizontalSlide.querySelectorAll( 'section' );

	// Store references to the previous and current slides
	currentSlide = currentVerticalSlides[ indexv ] || currentHorizontalSlide;


	// Show fragment, if specified
	if( typeof f !== 'undefined' ) {
		var fragments = sortFragments( currentSlide.querySelectorAll( '.fragment' ) );

		toArray( fragments ).forEach( function( fragment, indexf ) {
			if( indexf < f ) {
				fragment.classList.add( 'visible' );
			}
			else {
				fragment.classList.remove( 'visible' );
			}
		} );
	}

	// Dispatch an event if the slide changed
	var slideChanged = ( indexh !== indexhBefore || indexv !== indexvBefore );
	if( slideChanged ) {
		dispatchEvent( 'slidechanged', {
			'indexh': indexh,
			'indexv': indexv,
			'previousSlide': previousSlide,
			'currentSlide': currentSlide,
			'origin': o
		} );
	}
	else {
		// Ensure that the previous slide is never the same as the current
		previousSlide = null;
	}

	// Solves an edge case where the previous slide maintains the
	// 'present' class when navigating between adjacent vertical
	// stacks
	if( previousSlide ) {
		previousSlide.classList.remove( 'present' );

		// Reset all slides upon navigate to home
		// Issue: #285
		if ( document.querySelector( HOME_SLIDE_SELECTOR ).classList.contains( 'present' ) ) {
			// Launch async task
			setTimeout( function () {
				var slides = toArray( document.querySelectorAll( HORIZONTAL_SLIDES_SELECTOR + '.stack') ), i;
				for( i in slides ) {
					if( slides[i] ) {
						// Reset stack
						setPreviousVerticalIndex( slides[i], 0 );
					}
				}
			}, 0 );
		}
	}

	// Handle embedded content
	if( slideChanged ) {
		stopEmbeddedContent( previousSlide );
		startEmbeddedContent( currentSlide );
	}

	updateControls();
	updateProgress();
	updateBackground();

	// Update the URL hash
	writeURL();

}
```

#### Find out the event part in the reveal.js

In the reveal.js, all the slides are changed when they receive event.
These are some event received functions that influence main slide changes.

```javascript
function onDocumentKeyDown( event ){...}
function onProgressClicked( event ) {...}
function onNavigateLeftClicked( event ) {...}
function onNavigateRightClicked( event ) {...}
function onNavigateUpClicked( event ) {...}
function onNavigateDownClicked( event ) {...}
function onNavigatePrevClicked( event ) {...}
function onNavigateNextClicked( event ) {...}
```


### Presenter only Publish on Slide Change, Audience Viewer Subscribe only

Only presenter can publish on slide changes by sending message with slide number.
Then audience viewers will receive this slide number and change their slides.
We can distinguish presenter and audience viewer by description of URL.
The presenter will have following URL.

```html
<http://......./index.html?presenter>
```

And audience viewers have origin URL.

```html
<http://......./index.html>
```

#### Presenter will only Publish when the slide has taken change event

We can publish slide number whenever the presenter changes it.
In other words, pubnub will publish slide number when the slides are changed by presenter.
Here are some example.

```javascript
function onProgressClicked( event ) {
	//pubnub init
	var pubnub = PUBNUB.init({
		publish_key : 'demo',
		subscribe_key : 'demo'
	})

	//if this is presenter, it will change slide and publish slide number via "reveal" channel.
	var str = window.location.href;
	var subpath=str.substring(str.lastIndexOf("?")+1,str.lastIndexOf("#"));
	if (subpath == "presenter")
	  {

		event.preventDefault();

		var slidesTotal = toArray( document.querySelectorAll( HORIZONTAL_SLIDES_SELECTOR ) ).length;
		var slideIndex = Math.floor( ( event.clientX / dom.wrapper.offsetWidth ) * slidesTotal );

		slide( slideIndex );

		var hash = window.location.hash;
		
		// Attempt to parse the hash as either an index or name
		var bits = hash.slice( 2 ).split( '/' ),
			name = hash.replace( /#|\//gi, '' );
		pubnub.publish({
			channel : "reveal",
			message : bits
		})
    }
}
```

#### Audience viewer will only Subscribe (not Publish).

All the audience viewers will start at the start() function in reveal.js.
So they can subscribe slide number there.

```javascript
function start() {
	var pubnub = PUBNUB.init({
		publish_key : 'demo',
		subscribe_key : 'demo'
	})
	var str = window.location.href;
	var subpath=str.substring(str.lastIndexOf("?")+1,str.lastIndexOf("#"));
	if (subpath != "presenter")
	  {
		 pubnub.subscribe({
			    backfill : true,
				channel  : 'reveal',
				message  : function(m){ slide(m);}
		 })
	  }
	...
	...
	...
}
```


## Installation

The **basic setup** is for authoring presentations only. The **full setup** gives you access to all reveal.js features and plugins such as speaker notes as well as the development tasks needed to make changes to the source.

### Basic setup

The core of reveal.js is very easy to install. You'll simply need to download a copy of this repository and open the index.html file directly in your browser.

1. Download the latest version of reveal.js from <https://github.com/hakimel/reveal.js/releases>

2. Unzip and replace the example contents in index.html with your own

3. Open index.html in a browser to view it


### Full setup

Some reveal.js features, like external markdown, require that presentations run from a local web server. The following instructions will set up such a server as well as all of the development tasks needed to make edits to the reveal.js source code.

1. Install [Node.js](http://nodejs.org/)

2. Install [Grunt](http://gruntjs.com/getting-started#installing-the-cli)

4. Clone the reveal.js repository
```
$ git clone git@github.com:hakimel/reveal.js.git
```

5. Navigate to the reveal.js folder
```
$ cd reveal.js
```

6. Install dependencies
```
$ npm install
```

7. Serve the presentation and monitor source files for changes
```
$ grunt serve
```

8. Open <http://localhost:8000> to view your presentation

You can change the port by using `grunt serve --port 8001`.


### Folder Structure
- **css/** Core styles without which the project does not function
- **js/** Like above but for JavaScript
- **plugin/** Components that have been developed as extensions to reveal.js
- **lib/** All other third party assets (JavaScript, CSS, fonts)


### Contributing

Please keep the [issue tracker](github.com/hakimel/reveal.js/issues) limited to **bug reports**, **feature requests** and **pull requests**. If you are reporting a bug make sure to include information about which browser and operating system you are using as well as the necessary steps to reproduce the issue.

If you have personal support questions use [StackOverflow](http://stackoverflow.com/questions/tagged/reveal.js).


#### Pull requests

- Should follow the coding style
  - Tabs to indent
  - Single-quoted strings
  - No space between function name and opening argument parenthesis
  - One space after opening and before closing parenthesis 
- Should be made towards the **dev branch**
- Should be submitted from a feature/topic branch (not your master)


## License

MIT licensed

Copyright (C) 2013 Hakim El Hattab, http://hakim.se
