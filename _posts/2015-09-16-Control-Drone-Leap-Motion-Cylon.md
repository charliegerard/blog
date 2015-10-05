---
layout: post
title: Controlling the Parrot AR Drone with the Leap Motion in Cylon.js
author: charlie_gerard
excerpt: "A tutorial to learn how to program the Leap Motion to control the Parrot AR Drone using Cylon.js"
comments: true
intro_image: https://charliegerard.files.wordpress.com/2015/01/cylonjs-drone-leapmotion.png
---

<a href="https://charliegerard.files.wordpress.com/2015/01/cylonjs-drone-leapmotion.png"><img class="aligncenter size-large wp-image-85" src="https://charliegerard.files.wordpress.com/2015/01/cylonjs-drone-leapmotion.png?w=660" alt="cylon-drone-leapmotion" width="660" height="152" /></a>

Following my tutorial on <a href="https://charliegerard.wordpress.com/2015/01/12/sphero-leap-motion-cylon-js/" target="_blank">controlling the Sphero using the Leap Motion</a>, I thought I would keep on converting my Node.js projects to Cylon.js and work on controlling the Drone with the Leap Motion.

If you've had a look at my last tutorial, you probably noticed that using Cylon.js makes it really easy to program for hardware and connect multiple devices together.

Below is the usual setup of any Cylon project:

<pre><code>
{% highlight javascript linenos %}
var Cylon = require('cylon');

Cylon.robot({
  connections:{
    leapmotion: {adaptor: 'leapmotion'},
    ardrone: {adaptor: 'ardrone', port: '192.168.1.1'}
  },

  devices: {
    leapmotion: {driver: 'leapmotion', connection: 'leapmotion'},
    drone: {driver: 'ardrone', connection: 'ardrone'}
  },
{% endhighlight %}
 </code></pre>
As you can see, you simply need to specify which devices you are using, the more interesting bit comes in the rest of the code...
<pre><code>
{% highlight javascript linenos %}

  work: function(my){
   my.leapmotion.on('hand', function(hand){
     my.drone.takeoff();
     after((5).seconds(), function(){
       my.drone.land();
     })
   })
 }
}).start();
{% endhighlight %}
</code></pre>

This code only makes the Drone take off when the Leap Motion senses a hand over it and land after 5 seconds (just in case it decides to go crazy...).

Then, if you want to make it do more interesting things, you will have to play around with what the Leap Motion has to offer; different types of gestures, distance, hands, fingers, etc... The Drone actions themselves are pretty straightforward:
<ul>
  <li>my.drone.up();</li>
  <li>my.drone.down();</li>
  <li>my.drone.forward();</li>
  <li>my.drone.back();</li>
  <li>my.drone.left();</li>
  <li>my.drone.right();</li>
</ul>
You can also make the drone rotate clockwise or counterclockwise but what I found the most awesome thing is that the cylon-ardrone module makes the 'flip' movement really easy to execute. On a 'keyTap' for example, your drone could do a backflip!!

The code for that would look like this:

<pre><code>
{% highlight javascript linenos %}
  work: function(my){
   my.leapmotion.on('gesture', function(gesture){
     if(gesture){
       my.drone.takeoff();
       if(gesture.type === 'keyTap'){
         my.drone.backFlip();
         after((6).seconds(), function(){
          my.drone.land();
         }
       }
     } else {
       my.drone.stop();
     };
   };
 }
}).start();
{% endhighlight %}
</code></pre>

If you wanna see the difference with Node.js, you can find my original Github repo <a href="https://github.com/charliegerard/leap_drone" target="_blank">here</a>, otherwise <a href="https://github.com/charliegerard/cylon-projects/tree/master/cylon-drone-leapmotion" target="_blank">here is the repo</a> with more commands!

If you have any question, don't hesitate!