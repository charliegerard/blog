---
layout: post
title: "Virtual Reality In the Browser with Three.JS"
author: charlie_gerard
excerpt: "Virtual Reality is going to be big, if we like it or not. Learning how to build 3D environments is not easy though, it takes a lot of time and practice.
However, if you want to try something a bit simpler than 3DMax, Unity and C#, the Three.JS library offers the opportunity to create some VR effect."
tags: [technology, innovation, VR, 3D, JavaScript, Three.js]
comments: true
---

This year is supposed to be big for Virtual Reality as the Oculus Rift is finally going to be available for consumers.

Learning to build 3D environments is not easy though, it takes a lot of time and practice.
Also, with a price of around $600, it is not going to be accessible to everyone.

However, there is another alernative! The Three.JS library offers the opportunity to create some VR effect on the web, so you can create your own VR experience, visit your webpage on your phone, insert it in a Google Cardboard, and you have a low-cost VR headset!

<img src="http://cdn.pastemagazine.com/www/articles/Screen%20Shot%202015-01-29%20at%2012.20.50%20PM.png"/>

If you're not familiar with Three.js, it is a JavaScript library that allows you to create 3D graphics in the browser  (basically a simpler WebGL). It's very powerful and once you get comfortable with it, you can create very amazing stuff!

During the holidays, I gave it a try and built a basic VR experiment of flying over a procedural city.

<img src="https://lh3.googleusercontent.com/FZ6iH4eRXr_6TNvduOCiAT6j0l-tXkJr5MGt05KwRYDrd2NqaSvZOjqTFWyzo1kdj6KB6qKf0sbVAq5YVOWY9xYAgdJNGrcvINMowyluI2rZyhMRTlFCpGdrYX2tEfZd2l-qDx6SROQrD8qg1x0Z3riIWXql_kvhfUUMfEx6h0_nAv1oGtnBa1KNYCa6Cv7m_djZKQdh78G85Z8TaIVhtId2FJjmG_ibR7z-t1eAuG0SVAD2zP-I2ZlqkRDoQCcsL6UbeIfhTL0csStAqhSHAAuwDC1ZijcPeqdRyQmUI-o1qcSuJMshuPXhdtrcNbET9Vf-4H4tIBNlg9vigHeTZfBfZDyq7i2J8_y8BjhkAG24wtD5JpR9RGO7GcXEzYBqH34SekRLBD-YlkI7aYMaMAAwlpjK7xMHZCUt13hYjP9Zo2xc5fqQGgpcAEf1pqNFKvJ0tx8v9iMcnUbhCK9N1IyBEYxO93nPvSkTWwa06hWWpT5b_F7NV_LK5C0WPdWhRz0wlmiLs1cSKUzTphCgoLO8BCRFtlFveLe_W3fzw5VGPOlWSUdWUQT3KSFPXC8=w2527-h1465-no"/>

If you'd like to reproduce something similar, you can follow the steps below or you can check the full code on [Github](https://github.com/charliegerard/Threejs-VR/tree/master/city_vr).

To get started, you need to import the framework itself. Either through [cdnjs](https://cdnjs.com/#q=three.js) or by adding the file to your project folder.

{% highlight javascript linenos %}
  // in your index.html file
  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r73/three.min.js"></script>

{% endhighlight %}

You'll also need to add a few other files to generate the procedural city, add the flying controls, the Device Orientation Controls for mobile and the VR effect.
(I'm actually using the SteroEffect in this tutorial, but Three.js also has another library called VREffect that i'll be trying next)

{% highlight javascript linenos %}
  // in your index.html file, below the three.js script link
  <script src="https://raw.githubusercontent.com/jeromeetienne/threex.proceduralcity/master/threex.proceduralcity.js"></script>
  <script src="https://raw.githubusercontent.com/mrdoob/three.js/master/examples/js/controls/FirstPersonControls.js"></script>
  <script src="https://raw.githubusercontent.com/mrdoob/three.js/master/examples/js/controls/DeviceOrientationControls.js"></script>
  <script src="https://raw.githubusercontent.com/mrdoob/three.js/master/examples/js/effects/StereoEffect.js"></script>

{% endhighlight %}

Once this is done, we can start by creating a container div in the body of the html that will be used to render everything on the page:

{% highlight html linenos %}
  <div id="container"></div>
{% endhighlight %}

The rest of the code will have to live in a script tag. It's better practice to separate the JavaScript from the html and to create an index.js file but for the purpose of this tutorial, I just added the JavaScript code internally.

We start by declaring a few variables and call functions we'll write later:

{% highlight javascript linenos %}

    var camera, scene, renderer;
  var effect, controls;
  var element, container;

  var clock = new THREE.Clock();

  init();
  animate();

{% endhighlight %}

The details of the init() function comes below. I added comments in the code to explain what each line does:

{% highlight javascript linenos %}
  function init(){
    // Creates a Three.js scene.
    scene = new THREE.Scene();

    // Add some fog (not necessary);
    scene.fog	= new THREE.FogExp2( 0xd0e0f0, 0.0025 );

    // Creates a renderer, sets its size and appends everything to the container div.
    renderer = new THREE.WebGLRenderer();
    renderer.setSize( window.innerWidth, window.innerHeight );
    element = renderer.domElement;
    container = document.getElementById('container');
    container.appendChild(element);

    // Creates a camera, sets its position and adds it to the scene.
    camera = new THREE.PerspectiveCamera(90, window.innerWidth/window.innerHeight, 0.01, 3000);
    camera.position.set(-500, 100, -200);
    scene.add(camera);

    // Creates the Stereo Effect for the VR experience.
    effect = new THREE.StereoEffect(renderer);

    // Adds some light, sets its position and adds it to the scene.
    var light = new THREE.HemisphereLight(0xfffff0, 0x101020, 1.5);
    light.position.set( 0.75, 1, 0.25 );
    scene.add(light);

    // Creates the platform that will be beneath all buildings.
    var material	= new THREE.MeshBasicMaterial({ color: 0x101018 })
    var geometry	= new THREE.PlaneGeometry( 2000, 2000 )
    var plane	= new THREE.Mesh( geometry, material );
    plane.rotation.x= - 90 * Math.PI / 180;
    scene.add( plane );

    // Creates the procedural city and adds it to the scene.
    var city = new THREEx.ProceduralCity()
    scene.add(city)

    // Adds some control so you can move through the scene using the mouse or keyboard.
    controls = new THREE.FirstPersonControls( camera );
    controls.movementSpeed	= 20;
    controls.lookSpeed	= 0.05;
    controls.lookVertical	= true;

    // Adds different controls if seen on mobile.
    function setOrientationControls(e) {
      // If device orientation is not available, return.
      if (!e.alpha) {
        return;
      }

      // Create controls for mobile.
      controls = new THREE.DeviceOrientationControls(camera, true);
      controls.connect();
      controls.update();

      element.addEventListener('click', fullscreen, false);

      window.removeEventListener('deviceorientation', setOrientationControls, true);
    }
    window.addEventListener('deviceorientation', setOrientationControls, true);

    //Resize page.
    window.addEventListener('resize', resize, false);
    setTimeout(resize, 1);

  }  
{% endhighlight %}

The rest of the code handles resizing, updating the controls and camera, rendering, animating and requesting fullscreen.

{% highlight javascript linenos %}
    // on resize of the window, check the width and height of the container div and update the camera and rendering.
    function resize() {
      var width = container.offsetWidth;
      var height = container.offsetHeight;

      camera.aspect = width / height;
      camera.updateProjectionMatrix();

      renderer.setSize(width, height);
      effect.setSize(width, height);
    }

    // update the resizing and controls.
    function update(dt) {
      resize();

      camera.updateProjectionMatrix();

      controls.update(dt);
    }

    function render(dt) {
      effect.render(scene, camera);
    }

    function animate(t) {
      requestAnimationFrame(animate);

      update(clock.getDelta());
      render(clock.getDelta());
    }

    function fullscreen() {
      if (container.requestFullscreen) {
        container.requestFullscreen();
      } else if (container.msRequestFullscreen) {
        container.msRequestFullscreen();
      } else if (container.mozRequestFullScreen) {
        container.mozRequestFullScreen();
      } else if (container.webkitRequestFullscreen) {
        container.webkitRequestFullscreen();
      }
    }
{% endhighlight %}

And that's basically it for the code!

<img src="https://lh3.googleusercontent.com/SdjigFJKICWULdgWnvmmwhR3tLbxcaYblclGjbgeJ0nIi6sBu-qe6qgCleCM2Q0gb0zyRBnd40Imx9yRT1G4ESU0FcvcyoiIdOnk3MhlY2hHcUjzOPFnCcKVxconTiXnKeLNEAcDy8CxIbGLO168wWesuDMDYZo7N39w44ASB-IVyawdjlmzYwDyovCp9dd1MGj6G7pgt-PDDrOps3mwt8yZS6__nY_vLmQ7ylaEVvk3kzVXmUHNBTPbFYrcYQ678Na3iRD236dD0Ugjor_Awcr9ukfkH1p8sz5aTeBFi0EemB-63MY31a2E80yiyw9zm93IfCtLhyWT0zTxV_PpUN1D4RUvuCa__VKWiRsMy3QgvnbIhM8etzNBld-T2qIPgdEQt1RQnahdMBFVEstX1ys34TMM-uVxaNu5xq6m7ED4b686HSYafPeuGX7cHqfsgXyDL5PZJjVQhyKAPD5XAuy4eQSlKIIL41jFRHy4r8YMASMQxa1gwTIfZGtcRJcKzfyHKnfo-WqpGPZfWz1QXDo6twzX1sQwARYJRiNqj0YfAVkYK3PY987VbQHEMsg=w426-h240-no"/>

Of course, there is still a lot to do to get to a nice demo! But to get started, that's it :)

If you want to test it on your phone without having to publish your code, you can use [ngrok](https://ngrok.com/) to share your localhost.

Once again, if you want to get the full code, everything is on [Github](https://github.com/charliegerard/Threejs-VR/tree/master/city_vr)!
