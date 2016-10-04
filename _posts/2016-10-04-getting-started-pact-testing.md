---
layout: post
title: "Getting started with Pact testing"
author: charlie_gerard
excerpt: ""
tags: [technology, pact, testing, TDD]
comments: true
---

A few days ago, I had to run a brown bag session about Pact testing. I had absolutely no idea what it was but I had to prepare the workshop anyway so I started by looking at <a href="https://docs.pact.io/" target="_blank">this resource</a>

The essence of it can be summarised in 3 words: "Consumer", "Provider" and "Contracts".

The goal of Pact testing is to let the 'Consumer' of a service specify and verify the interactions between different modules of an application.
Once the Consumer has defined what kind of interactions they want to have with the Provider, these 'rules' are written in a 'pact' file that is then available to the Provider.
The other services must then agree to these 'Contracts' and ensure that they are not breaking them.

For my workshop, I decided to implement an example in JavaScript using Jasmine and Node.js, but implementations of the Pact framework are available in other languages such as <a href="https://github.com/SEEK-Jobs/pact-net" target="_blank">.NET</a>, <a href="https://github.com/DiUS/pact-jvm" target="_blank">Java</a>, <a href="https://github.com/realestate-com-au/pact" target="_blank">Ruby</a>, etc...

##Setup:

To get started with Pact testing in JavaScript, I followed the steps indicated <a href="https://github.com/DiUS/pact-consumer-js-dsl" target="_blank">here</a>

It basically consists of the following steps (for MAC OSX):

1- If you don't have that already installed, install <a href="https://www.ruby-lang.org/en/downloads/" target="_blank">Ruby</a> & <a href="https://rubygems.org/pages/download" target="_blank">RubyGems</a>

2- Create a folder where your app is going to live and create a Gemfile.

3- In your terminal, cd into your app and run `gem install bundler && bundle install`.

4- Inside your Gemfile, paste the following code:

{% highlight ruby linenos %}
source 'https://rubygems.org'
gem 'pact-mock_service', '~> 0.7.0'
{% endhighlight %}

5- Run `bundle install` again.

6- Make sure you have Node.js and npm installed on your machine and run `npm init` to generate your `package.json` file.

7- Install Karma by running `npm install karma karma-jasmine karma-chrome-launcher --save-dev` and `npm install -g karma-cli`

8- Run `karma init`. Answer <strong>jasmine</strong> for testing framework and <strong>no</strong> for use require.js.

9- Add pact-consumer-js-dsl to your project by running `bower install pact-consumer-js-dsl --save-dev`.

10- Tell Karma about pact-consumer-js-dsl.js in `karma.conf.js`. In the files: [] section add a new entry for `bower_components/pact-consumer-js-dsl/dist/pact-consumer-js-dsl.js`

11- Allow tests to load resources from pact mock server. One way to do this is in the `karma.conf.js`, change `browsers: ['Chrome']`, or `browsers: ['PhantomJS']`, to

{% highlight javascript linenos %}
browsers: ['Chrome_without_security'],
customLaunchers: {
   Chrome_without_security: {
       base: 'Chrome',
       flags: ['--disable-web-security']
   }
},

or:

browsers: ['PhantomJS_without_security'],
customLaunchers: {
   PhantomJS_without_security: {
     base: 'PhantomJS',
     flags: ['--web-security=false']
   }
},
{%endhighlight%}

Once you've been through all of these steps, we can start writing our tests and implementations.

##Consumer test:

Let's start by writing the consumer test first, create a file called `client-spec.js`.

Let's imagine we have a simple app that returns the word 'hello' when you make a GET request to '/hello'.

Our `client-spec.js` file is going to look something like this:

{% highlight javascript linenos %}
(function(){
  describe('Client', function(){
    var client, provider;

    beforeEach(function(){
      client = exampleApp.createServer('http://localhost:1234');
      provider = Pact.mockService({
        consumer: 'consumer',
        provider: 'provider',
        port: 1234,
        done: function(err){
          expect(err).toBe(null);
        }
      })
    });

    it('should say hello', function(done){
      provider
        .uponReceiving('a request for hello')
        .withRequest('get', '/hello')
        .willRespondWith(200, {
          'Content-Type': 'application/json'
        }, {
          reply: 'hello'
        });

      provider.run(done, function(complete){
        expect(client.sayHello()).toEqual('hello')
        complete();
      })
    })
  })
})()
{%endhighlight%}

Basically what is happening here is that we are creating a mock server and mock provider service and we are checking that the call we are expecting to make to '/hello' will return the response 'hello'.

Before being able to run this test, we need to add it to the `files:[]` array in our `karma.conf.js` file.

Then, in your terminal, run:

```
bundle exec pact-mock-service -p 1234 --pact-specification-version 2.0.0 -l log/pact.logs --pact-dir tmp/pacts
```

And in another terminal window, run `karma start`.

At the moment, your test should fail because we have not yet written the implementation.

To do so, create a file called `client.js`.  

This file should look something like this:

{% highlight javascript linenos %}
var exampleApp = exampleApp || {};

(function(){
  var baseUrl;

  this.createServer = function(url){
    baseUrl = url;
    return this;
  };

  this.sayHello = function(){
    var xhr = new XMLHttpRequest();
    xhr.open('GET', baseUrl + '/hello', false);
    xhr.send();

    return JSON.parse(xhr.responseText).reply;
  }
}).apply(exampleApp)
{%endhighlight%}


As we can see, we have our function to mock the server, and our `sayHello` function that does the 'GET' request to '/hello'.

Before running the tests again, you'll need to add the `client.js` file to the `karma.conf.js` file.

If you try again the 2 commands to run the tests, it should all pass this time.

Also, it should have generated a `tmp` folder with a json file inside, this is our <strong>pact file</strong> or 'Contract'. This file specifies the terms of the contract between the Consumer and the Provider.

Now that our Consumer test is passing and we have our Pact file, we should work on the Provider implementation.

##Provider test

To start working on the server side, we need to install <a href="https://github.com/pact-foundation/pact-node" target="_blank">Pact-Node</a> and <a href="http://expressjs.com/" target="_blank">Express.js</a>:

{% highlight javascript linenos %}
  npm install @pact-foundation/pact-node --save-dev
{%endhighlight%}

{% highlight javascript linenos %}
  npm install express --save-dev
{%endhighlight%}

This should be all you need to install. Let's get started with the Provider test.

Create a file called `provider-spec.js`; it should look like this:

{% highlight javascript linenos %}
var pact = require('@pact-foundation/pact-node');
var path = require('path');

var opts = {
    providerBaseUrl: 'http://localhost:3000', //API host endpoint
    pactUrls: [path.resolve(__dirname, './tmp/pacts/consumer-provider.json')], //path to your pact files
};

pact.verifyPacts(opts).then(function(){
  console.log('SUCCESS!')
}).catch(function(error){
  console.log('failed', error)
});
{%endhighlight%}

We start by requiring our modules, then we define the port on which the server is running and where to find our pact files, and finally we verify that our provider code meets the requirements of the contract (pact file).

To run this, we need to write the code for our server. Create a `provider.js` file, and write something like this:

{% highlight javascript linenos %}
var express = require('express');
var app = express();
var bodyParser = require('body-parser');

app.use(bodyParser.json());

app.get('/hello', function (req, res) {
  var response = {reply: 'hello'};
  res.writeHead(200, {'Content-Type': 'application/json'});
  res.end(JSON.stringify(response));
});

app.listen(3000, function () {
  console.log('Example app listening on port 3000!');
});
{%endhighlight%}

Here, we require the modules needed, we create our server, give it a port and implement the code for our '/hello' request.

To check if our Provider works with our contract, open two new terminal window and in one of them, run `node provider.js` and in the other one, run `node provider-spec.js`

You should see the message 'SUCCESS' being returned.

That's it! If everything works fine you've just written your first Consumer and Provider Pact test! :)

If you need more info, here's a <a href="http://techblog.newsweaver.com/why-should-you-use-consumer-driven-contracts-for-microservices-integration-tests/" target="_blank">good article</a> I found about Pact testing.
