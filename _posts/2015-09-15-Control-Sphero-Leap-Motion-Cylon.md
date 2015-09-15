---
layout: post
title: Controlling the Sphero using the Leap Motion in Cylon.js
author: charlie_gerard
excerpt: "A tutorial to learn how to program the Leap Motion to control the Sphero using Cylon.js"
---

<a href="https://charliegerard.files.wordpress.com/2015/01/cylonjs-sphero.png"><img class="aligncenter size-large wp-image-61" src="https://charliegerard.files.wordpress.com/2015/01/cylonjs-sphero.png?w=660" alt="cylonjs-sphero-leap-motion" width="660" height="152" /></a>

In my personal time, I love to play around with hardware and robots.

I started in Node.js but recently I discovered Cylon.js and after a quick play around with it, I found it pretty awesome and decided to rewrite my projects using this framework.

As a starting point, I decided to rewrite the project to control the <a href="http://www.gosphero.com/" target="_blank">Sphero</a> with the <a href="https://www.leapmotion.com/" target="_blank">Leap Motion</a>.

You can find the original repo <a href="http://charliegerard.github.io/leap_sphero" target="_blank">here</a>, but here are a few code snippets:

<a href="https://charliegerard.files.wordpress.com/2015/01/screen-shot-2015-01-10-at-3-02-47-pm.png"><img class="aligncenter wp-image-62 size-large" style="border:1px solid #000000;" src="https://charliegerard.files.wordpress.com/2015/01/screen-shot-2015-01-10-at-3-02-47-pm.png?w=660" alt="Screen Shot 2015-01-10 at 3.02.47 pm" width="660" height="348" /></a>

<a href="https://charliegerard.files.wordpress.com/2015/01/screen-shot-2015-01-10-at-3-04-09-pm.png"><img class="aligncenter wp-image-63 size-large" style="border:1px solid #000000;" src="https://charliegerard.files.wordpress.com/2015/01/screen-shot-2015-01-10-at-3-04-09-pm.png?w=660" alt="Screen Shot 2015-01-10 at 3.04.09 pm" width="660" height="419" /></a>

The way it works is pretty straight forward. The Sphero connects via bluetooth and the Leap Motion needs to be plugged in your computer. Once the Sphero is detected, the hand is tracked by the Leap Motion and the direction will be applied to the Sphero.

Feel free to have a better look at the code on <a href="http://charliegerard.github.io/leap_sphero" target="_blank">Github</a>.

Now, let's move on to <strong>Cylon.js</strong>. The first thing I noticed about this framework is the short amount of code necessary to get to the same result. I managed to do pretty much the exact same thing in 68 lines of code!

I guess what makes it easier is that Cylon already has some modules you can install to program for certain devices, like the ones below:

<a href="https://charliegerard.files.wordpress.com/2015/01/screen-shot-2015-01-11-at-8-59-14-pm.png"><img class="aligncenter size-large wp-image-69" src="https://charliegerard.files.wordpress.com/2015/01/screen-shot-2015-01-11-at-8-59-14-pm.png?w=660" alt="cylon-devices" width="660" height="487" /></a>

To start using Cylon, you need to require it and specify which devices you are working with.

<pre><code>
var Cylon = require('cylon');

Cylon.robot({
  connections: {
    leapmotion: {adaptor: 'leapmotion'},
    sphero: {adaptor: 'sphero', port: '/dev/rfcomm0'}
  },

  devices: {
    leapmotion: {driver: 'leapmotion', connection: 'leapmotion'},
    sphero: {driver: 'sphero', connection: 'sphero'}
  },

  work: function(f){
  }
}).start();
</code></pre>

At the moment, this code is not really doing anything but you can see how to specify which devices you are going to use.

You have to specify a port for the Sphero because it connects to your computer via Bluetooth. To find the port for your own Sphero, run 'ls /dev/tty.Sphero*' in your console and replace the port in this code with the result you get.

The rest of the code goes inside the 'work' function as below:

<pre><code>
work: function(my){
  my.leapmotion.on('frame', function(frame){
   if(frame.valid &amp;&amp; frame.gestures.length &gt; 0{
     my.sphero.roll(70,0,1);
   }
  }
}

</code></pre>

The code above makes the Sphero go forward if the Leap Motion detects any kind of gesture.

For the full code, have a look at <a href="https://github.com/charliegerard/cylon-projects/tree/master/cylon-leapmotion-sphero" target="_blank">my github repo</a>.

I'll probably write another tutorial soon once I have a chance to rewrite another project but in the meantime let me know if you have any question!