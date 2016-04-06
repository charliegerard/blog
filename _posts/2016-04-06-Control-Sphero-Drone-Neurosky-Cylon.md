---
layout: post
title: "Control the Sphero and Parrot AR Drone with the Neurosky brain sensor in Cylon.js"
author: charlie_gerard
excerpt: "I have been experimenting with the Neurosky brain sensor for a few months now but I forgot to write a blog post about it so here it is!..."
tags: [technology, creative technologies, robots, robotics, hardware, programming, innovation ]
comments: true
---

I have been experimenting with the Neurosky brain sensor for a few months now but I forgot to write a blog post about it so here it is!

![Neurosky](http://www.zenzoneinteractive.com/Img/Neuroskysensor1.png)

In the continuity of my previous experiments with the [Sphero](http://charliegerard.github.io/blog/Control-Sphero-Leap-Motion-Cylon/), [Parrot AR Drone](http://charliegerard.github.io/blog/Control-Drone-Leap-Motion-Cylon/), Leap Motion and [Myo armband](http://charliegerard.github.io/blog/Control-Drone-Myo-Node/), I bought a Neurosky to try and control the Sphero and Drone using my brain waves.

Using the framework Cylon.js, I was able to write a basic program in less than 50 lines of code.

![cylon.js](https://camo.githubusercontent.com/3420092e29cad2a25520a3efd3c995a3ad22996f/687474703a2f2f692e696d6775722e636f6d2f553370614e68522e706e67)

[Here is the code](https://github.com/charliegerard/neurosky-sphero-cylon) to control the Sphero:

{% highlight javascript linenos %}
// require the npm module
var Cylon = require('cylon');

// setup the ports and devices to connect to
Cylon.robot({
	connections: {
		neurosky: {adaptor: 'neurosky', port: '/dev/tty.MindWaveMobile-DevA'},
		sphero: { adaptor: 'sphero', port: '/dev/tty.Sphero-RBR-AMP-SPP' }
	},

	devices: {
		headset: {driver: 'neurosky', connection: 'neurosky'},
		sphero: {driver: 'sphero', connection: 'sphero'}
	},

  // main function for all programs written in Cylon.js
	work: function(my){
		my.headset.on('attention', function(data){
			console.log('attention: ' + data);
      // If attention data is superior to 80, make the Sphero roll forward
			if(data > 80){
				my.sphero.roll(70,0,1);
				after((4).seconds(), function(){
					my.sphero.roll(0,0,0);
				});
			}
		});
	}
}).start();

{% endhighlight%}

At the moment, the program does not do much apart from making the ball roll forward if the attention level is above the threshold 80 and making it stop after 4 seconds. However to get started, it's a pretty good way to get familiar with Cylon.js and the Neurosky.

The headset also gives you access to some raw data like Delta, Theta, Beta, etc... but I don't really know what they relate to so I haven't been using them so far.

[Here is the code](https://github.com/charliegerard/neurosky-drone) to control the Parrot AR Drone:

{% highlight javascript linenos %}
var Cylon = require('cylon');

Cylon.robot({
	connections: {
		neurosky: {adaptor: 'neurosky', port: '/dev/tty.MindWaveMobile-DevA'},
		ardrone: { adaptor: 'ardrone', port: '192.168.1.1' }
	},

	devices: {
		headset: {driver: 'neurosky', connection: 'neurosky'},
		drone: {driver: 'ardrone', connection: 'ardrone'}
	},

	work: function(my){
		my.headset.on('attention', function(data){
			console.log('attention: ' + data);
			if(data > 70){
				my.drone.takeoff();
				after((4).seconds(), function(){
					my.drone.land();
				});
			}
		});
	}
}).start();
{% endhighlight %}

As you can see, this is pretty much similar to the code to control the Sphero, which makes it easy to get something working quickly.

The only downside is that there is so much magic in there that you don't really get exposed to what is happening in the background.

For it to be more interesting, I'd like to recreate these programs in Node.js.
