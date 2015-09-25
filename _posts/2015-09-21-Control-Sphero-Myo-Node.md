---
layout: post
title: Control the Sphero with the Myo Armband in Node.js
author: charlie_gerard
excerpt: "Learn how to program the Myo armband to control the Sphero using Node.js"
comments: true
---

<a href="https://charliegerard.files.wordpress.com/2015/01/myo-sphero-node.png"><img class="aligncenter size-full wp-image-118" src="https://charliegerard.files.wordpress.com/2015/01/myo-sphero-node.png" alt="myo-sphero-node" width="660" height="152" /></a>

I received my<strong> <a title="Myo armband" href="https://www.thalmic.com/en/myo/" target="_blank">Myo armband</a></strong> a few months ago and I have been working on a few projects to control my other devices with it. To start with, I thought I would explain how I managed to control the<strong> <a title="Sphero" href="http://www.gosphero.com/" target="_blank">Sphero</a></strong> ball using the movements of my arms and <a title="Node.js" href="http://nodejs.org/" target="_blank"><strong>Node.js</strong></a>.

To follow along, you can view all the code on my <strong><a href="https://github.com/charliegerard/myo_sphero" target="_blank">github repo</a></strong>.

The setup of the server side is pretty straight-forward, here's what is in my app.js file:
<pre><code>
  {% highlight javascript linenos %}
  var express = require('express'),
    path = require('path');

var app = express();

var lab = require('./my_modules/myo_sphero');
lab();

// app.use(express.static(path.join(__dirname, 'views')));

//.all('*', function(req, res){
// res.sendfile('views/index.html');
//});

app.listen(3001);
console.log("Server running on port 3001");

{% endhighlight %}
</code></pre>

As you can see, you start by <strong>requiring 'express' and 'path'</strong> to be able to start your server and serve your different files.

You then <strong>require the module</strong> you created and you call it so it can be executed when you start your app.

The commented part of the code gives you the ability to serve something in your browser as well by calling the files in your 'views' folder but I am not using it at the moment as I just make the connection between the Myo and the Sphero without building anything in the front-end. The first part calls all the files in your folder whereas the second part allows you to serve a single file.

Finally, the app uses the port 3001 that you can check in your browser by visiting http://localhost:3001.

Then, you can write your commands in your module; here's a snippet of my index.js:
<pre><code>
  {% highlight javascript linenos %}
  module.exports = function() {

  var spheron = require('spheron');
  var myo = require('myo');

  myo = Myo.create();

  // Set this to the device Sphero connects as on your computer.
  var device = '/dev/tty.Sphero-RBR-AMP-SPP';

  var safeMode = true;

  var controlSphero = function(sphero) {
    myo.on('wave_out', function(){
      myo.setLockingPolicy();
      console.log('RIGHT');
      sphero.heading = 90;
      sphero.roll(70, 90, 1);
    })
  }
}
{% endhighlight %}
</code></pre>

<strong>Wrap your code</strong> into a 'module.exports' function to access it from other files.

Then, <strong>require the modules</strong> you need, create an instance of the Myo and find the reference of the Sphero on your computer by entering 'ls /dev/tty.Sphero*' in your command line once your Sphero and your bluetooth connection are on.

The controlSphero function checks if you are doing the <strong>'wave out' movement</strong> with the Myo and if so, makes the Sphero roll to the right.

The <strong>myo.setLockingPolicy()</strong> command stops your Myo from being locked after a few seconds so you can keep on making movements and tracking them.

Myo updated their firmware and API recently and introduced an automatic locking of the armband to avoid accidental tracking of gesture but for this app, we need it to be turned off. You can usually pass in the value 'none' or 'standard' but I made it default to none. If you want to change this, you can find the myo.js file inside the myo module and find the function 'setLockingPolicy'. You can learn more about this command on the <a href="https://developer.thalmic.com/docs/api_reference/platform/script-reference.html#script-api-setLockingPolicy" target="_blank">API reference</a>.

With the update of the firmware, new gestures have been added such as 'index to thumb' or 'click' and the 'thumb to pinky' has been removed so there is a little bit more you can play with.

Once again, if you're interested, you can find the full code on my<a title="Controlling the Sphero with the Myo armband in Node.js" href="http://charliegerard.github.io/myo-sphero" target="_blank"> github.</a>

Let me know if you have any question :) Good luck!