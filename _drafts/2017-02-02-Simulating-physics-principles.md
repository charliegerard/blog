---
layout: post
title: "Simulating physics principles with Processing"
author: charlie_gerard
excerpt: "I started reading 'The Nature of Code' by Daniel Shiffman and realised that simulating physics principles with code was fascinating..."
comments: true
---

I started reading *'The Nature of Code'* by Daniel Shiffman and realised that simulating physics principles with code was fascinating.

Even a simple sketch like a bouncing ball actually gets a lot more interesting once you understand the physics applied to it.

You don't realise how many forces are applied to a moving object until you actually start looking at it closer.

For example, let's look at a bouncing ball. The movement of the bouncing ball involves different forces like *gravity*, *wind* and *friction*.

**Gravity** pulls the object down to earth, when **wind** applies a force pushing the object either left or right. **Friction** on the other hand represents the force pushing the object on the opposite direction of velocity.  

Coding that in Processing would look like:

```
Mover mover;

void setup(){
  size(800,400);
}

void draw(){
  background(0);

  PVector wind = new PVector(0.01, 0);
  float m = mover.mass;
  PVector gravity = new PVector(0, 0.1*m);

  float c = 0.01; // coefficient of friction;
  float normal = 1; // normal force;
  float frictionMagnitude = c * normal;

  PVector friction = mover.velocity.get();
  friction.mult(-1);
  friction.normalize();
  friction.mult(frictionMagnitude);

  mover.applyForce(friction);
  mover.applyForce(wind);
  mover.applyForce(gravity);

  mover.update();
  mover.display();
  mover.checkEdges();

}

class Mover{
  PVector position;
  PVector velocity;
  PVector acceleration;
  float mass;
  int colour;

  Mover(float m, float x, float y){
     mass = m;
     position = new PVector(x, y);
     velocity = new PVector(0,0);
     acceleration = new PVector(0, 0);
     colour = 200;
  }

  void applyForce(PVector force){
     PVector f = PVector.div(force, mass); //Make a copy of the force vector to not overwrite it.
     acceleration.add(f);
  }

  void update(){        
     velocity.add(acceleration);
     position.add(velocity);
     acceleration.mult(0);
  }

  void display(){
     noStroke();
     fill(colour);
     ellipse(position.x, position.y, mass*16, mass*16);
  }

  void checkEdges(){
     if(position.x > width){
       position.x = width;
       velocity.x *= -1;
     } else if(position.x < 0){
       velocity.x *= -1;
       position.x = 0;
     }

     if(position.y > height){
       velocity.y *= -1;
       position.y = height;
     }
  }
}
```
