---
layout: post
title: "Turning the Makey Makey Into a Capacitive Sensor"
author: charlie_gerard
excerpt: "I bought a Makey Makey a few months ago and when I started playing with it, I realised quickly that having to attach one of the alligator clip to your wrist or finger to be able to use the device properly made the experience a bit less exciting for me..."
tags: [technology, innovation, electronics, wearables, hacking, makey makey]
comments: true
intro_image: https://photos-5.dropbox.com/t/2/AAAw04BTeKhpJBcJrpCn1Uue82ymNvaixZp1kjti1ldnzA/12/41049144/jpeg/32x32/1/_/1/2/makey%20makey_03.jpg/EPboqx8Y5hcgAigC/HdWbucnn66fa8Bn6D0ctGDpUuNwzMrUky-vVpXQ7tic?size=1024x768&size_mode=2
---

<a href="https://charliegerard.files.wordpress.com/2015/04/11511-01b.jpg"><img class="aligncenter size-full wp-image-176" src="https://charliegerard.files.wordpress.com/2015/04/11511-01b.jpg" alt="makey-makey" width="600" height="600" /></a>

I bought a Makey Makey a few months ago and when I started playing with it, I realised quickly that having to attach one of the alligator clip to your wrist or finger to be able to use the device properly made the experience a bit less exciting for me.

What I really wanted was to be able to simply touch any conductive surface to create a reaction, without have to touch the other wire usually connected to the "earth" part of the board.

So I started looking for a solution to turn this board into a real capacitive sensor.
At first I thought it would not be possible but, after looking for a few days, I finally found a solution.

So, if like me, you'd like to make your board do something different, follow the steps below:

- Plug a wire in the pin number 5 of the board.

- Plug your Makey Makey in your computer.

- Launch the Arduino IDE and in the "Board" section of the menu, select the Makey Makey.

- Copy and paste the following code:

<pre><code>
    {% highlight c linenos %}
int capSensePin = 5;

// This value may be different.
int touchedCutoff = 0;

void setup(){
  Serial.begin(9600);
}

void loop(){

  if (readCapacitivePin(capSensePin) &gt; touchedCutoff) {
    Serial.println(readCapacitivePin(capSensePin));
  }

}

uint8_t readCapacitivePin(int pinToMeasure){
  // This is how you declare a variable which
  // will hold the PORT, PIN, and DDR registers
  // on an AVR
  volatile uint8_t* port;
  volatile uint8_t* ddr;
  volatile uint8_t* pin;
  // Here we translate the input pin number from
  // Arduino pin number to the AVR PORT, PIN, DDR,
  // and which bit of those registers we care about.
  byte bitmask;
  if ((pinToMeasure &gt;= 0) &amp;&amp; (pinToMeasure &lt;= 7)){
    port = &amp;PORTD;
    ddr = &amp;DDRD;
    bitmask = 1 &lt;&lt; pinToMeasure;
    pin = &amp;PIND;
  }
  if ((pinToMeasure &gt; 7) &amp;&amp; (pinToMeasure &lt;= 13)){
    port = &amp;PORTB;
    ddr = &amp;DDRB;
    bitmask = 1 &lt;&lt; (pinToMeasure - 8);
    pin = &amp;PINB;
  }
  if ((pinToMeasure &gt; 13) &amp;&amp; (pinToMeasure &lt;= 19)){
    port = &amp;PORTC;
    ddr = &amp;DDRC;
    bitmask = 1 &lt;&lt; (pinToMeasure - 13);
    pin = &amp;PINC;
  }
  // Discharge the pin first by setting it low and output
  *port &amp;= ~(bitmask);
  *ddr |= bitmask;
  delay(1);
  // Make the pin an input WITHOUT the internal pull-up on
  *ddr &amp;= ~(bitmask);
  // Now see how long the pin to get pulled up
  int cycles = 16000;
  for(int i = 0; i &lt; cycles; i++){
    if (*pin &amp; bitmask){
    cycles = i;
    break;
  }
}
// Discharge the pin again by setting it low and output
// It's important to leave the pins low if you want to
// be able to touch more than 1 sensor at a time - if
// the sensor is left pulled high, when you touch
// two sensors, your body will transfer the charge between
// sensors.
*port &amp;= ~(bitmask);
*ddr |= bitmask;

return cycles;
}
{% endhighlight %}

</code></pre>

- Upload the code onto the Makey Makey and open the serial monitor.

You should see the value 0 printed when you're not in contact with the wire plugged in pin 5. If it changes to 1 when you're touching it, then it's working!

Now if you plug the wire in a conductive matter, you should be able to produce an effect by touching it without having to hold the cable plugged in the earth port of the board! :)

To get started, you could try to connect an Arduino with an LED and make it turn on when you're touching the wire!

Hope it helps!
