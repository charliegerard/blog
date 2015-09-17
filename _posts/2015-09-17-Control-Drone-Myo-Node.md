---
layout: post
title: Controlling the Parrot AR Drone with the Myo Armband in Node.js
author: charlie_gerard
excerpt: "A tutorial to learn how to program the Myo armband to control the Parrot AR Drone using Node.js"
comments: true
---

<a href="https://charliegerard.files.wordpress.com/2015/02/myo-drone-node.png"><img class="aligncenter size-full wp-image-136" src="https://charliegerard.files.wordpress.com/2015/02/myo-drone-node.png" alt="myo-drone-nodjs-tutorial" width="660" height="152" /></a>

Following the last tutorial I wrote on how to control the <a href="http://www.gosphero.com/" target="_blank">Sphero</a> with the <a href="https://www.thalmic.com/en/myo/" target="_blank">Myo armband</a>, here is another one on how to control the <a href="http://ardrone2.parrot.com/" target="_blank">Parrot AR Drone</a> with the Myo using Node.js.

If you want to follow along, you can find the <a title="Myo Drone" href="https://github.com/charliegerard/myo_drone" target="_blank">repo on my github</a>.

To begin with, we need to setup the server configuration. After requiring the necessary modules, here is what we need to write:
<pre><code>
  var env = process.env.NODE_ENV || 'development';
if('development' == env){
  app.set('port', process.env.PORT || 3000);
  app.use(express.static(__dirname + '/public'));
  app.use("/node_modules", express.static(path.join(__dirname, 'node_modules')))
}

server = require('http').createServer(app);

var bayeux = new faye.NodeAdapter({
    mount: '/faye',
    timeout: 50
  });

  bayeux.attach(server);

  client = new faye.Client("http://localhost:" + (app.get("port")) + "/faye", {});

  client.subscribe("/drone/move", function (d) {
    console.log(d);
    console.log("drone move?")
    return drone[d.action](d.speed);
  });

  client.subscribe("/drone/drone", function (d) {
    console.log(d);
    console.log("drone stuff")
      return drone[d.action]();
  });

  server.listen(app.get('port'), function () {
    console.log("Express server listening on port " + app.get("port"));
  })
</code></pre>
We use express to set up the server and Faye &amp; Bayeux for the communications between the Drone and your app.

We also use the module 'dronestream' to be able to stream video from the drone to the browser that is listening on port 3001.

The rest of the code deals with the commands sent from the Myo to the drone. First of all, we need to create an instance of the Myo and then define some commands.
<pre><code>
  myo = Myo.create();

myo.unlock();

myo.on('fingers_spread', function(){
    takeoff();
});

myo.on('wave_in', function(){
    goLeft();
})

var takeoff = function () {
    flying = true;
    return faye.publish("/drone/drone", {
      action: 'takeoff'
    });
};

var goLeft = function(){
    stopped = false;
    setTimeout(function (){
      return faye.publish("/drone/move", {
        action: 'left'
        // speed: adjustXspeed
      })
    }, timeout);
  };
</code></pre>
Once the instance of the Myo is created, it can detect certain types of movements and run functions accordingly.

The 'myo.unlock()' command should allow the Myo to be unlocked while you use the app. However, I noticed I still needed to execute the unlock gesture 'double tap' between each command... Then the 'faye.publish' allows to send the commands and make the drone move. You can adjust the action and speed as you like.

This is just a snippet so once again, if you wanna have a look at the rest of the code or if you want to play around with it, everything is available on my <a title="Github repository for Myo Drone" href="https://github.com/charliegerard/myo_drone" target="_blank">Github</a> :)

Hope it helps and enjoy!