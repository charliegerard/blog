---
layout: post
title: "Understanding Unidirectional Data Flow with Flux"
author: charlie_gerard
excerpt: "I started using React.js on a project I am working on at the moment and while I am trying to have a better understanding of how everything works, I've done some research around the difference between two-way data binding and unidirectional data flow (or one-way data binding)..."
tags: [technology, react.js, javascript, programming ]
comments: true
---

I started using React.js on a project I am working on at the moment and while I am trying to have a better understanding of how everything works, I've done some research around how unidirectional data flow (or one-way binding) works.

Flux is the application architecture we're using with React to be able to use this one-way binding and to understand how it works, here's a simple graph:

<img src="https://facebook.github.io/flux/img/flux-simple-f8-diagram-explained-1300w.png">

Basically, the flow goes this way: Actions -> Store -> View

Your view generates actions from the user's interaction (ex: a user clicks on a button).
Actions can be defined as new data entering the system in the form of packages containing the new data and the action type.
Then this action triggers the dispatcher that registers a callback. As each store registers a callback with the dispatcher, when new data comes in it, the dispatcher uses these callbacks to propagate this data to all of the stores.
When the stores receives these actions, they can use the action type to determine if they should respond to it and how.
Invoking these callbacks is made using the dispatch() method.

You can use Facebook's dispatcher library which is pretty easy to use. If we follow the example found in the Flux documentation:

<pre><code>
  {% highlight javascript linenos %}
    var flightDispatcher = new Dispatcher();

    //The CityStore keeps track of which city is selected
    var CityStore = {city: null};

    //When the user selects a city, the dispatcher dispatches the payload
    flightDispatcher.dispatch({
      actionType: 'city-update',
      selectedCity: 'Paris'
    })

    //The payload is then digested by the CityStore
    flightDispatcher.register(function(payload){
      if(payload.actionType === 'city-update'){
        CityStore.city = payload.selectedCity;
      }
    })
  {% endhighlight %}
</code></pre>

The dispatcher is useful to deal with dependencies between stores. As an app gets bigger, you often need an update made in store A before doing it in store B. This situation can be dealt with using waitFor();
Also, the dispatch() method runs callbacks in a synchronous way so it will wait for events to be finished before running the following ones. If we use waitFor() inside these callbacks, the execution of the callback will stop and the waitFor() function will run. The callback will continue running once the waitFor() block has been executed.

Now if we look at our previous example but adding dependencies, here is what it could look like:

<pre><code>
  {% highlight javascript linenos %}
    var flightDispatcher = new Dispatcher();

    var CityStore = {city: null};

    var CountryStore = {country: null};

    //The user changes the country selected
    flightDispatcher.dispatch({
      actionType: 'country-update',
      selectedCountry: 'Australia'
    })

    //The payload will now have to be digested by both stores
    CountryStore.dispatchToken = flightDispatcher.register(function(payload){
      if(payload.actionType === 'country-update'){
        CountryStore.country = payload.selectedCountry
      }
    });

    //When the callback to update CountryStore is registered, we save a reference in a toen.
    //Using this token in waitFor ensures that CountryStore will be updated before updating CityStore.
    CityStore.dispatchToken = flightDispatcher.register(function(payload){
      if(payload.actionType === 'country-update'){
        flightDispatcher.waitFor([CountryStore.dispatchToken]);

        CityStore.city = getDefaultCityForCountry(CountryStore.country);
      }
    });
  {% endhighlight %}
</code></pre>

You can find another good example in [this tutorial](http://blog.andrewray.me/flux-for-stupid-people/?utm_source=devmag.io).

Now if we want to understand how the full circle works, we need our store to emit a 'change' event:

<pre><code>
  {% highlight javascript linenos %}
    CityStore.dispatchToken = flightDispatcher.register(function(payload){
      if(payload.actionType === 'country-update'){
        flightDispatcher.waitFor([CountryStore.dispatchToken]);

        CityStore.city = getDefaultCityForCountry(CountryStore.country);

        CountryStore.trigger('change');
      }
    });

  {% endhighlight %}
</code></pre>

Your view needs to respond to this change event:

<pre><code>
  {% highlight javascript linenos %}
    componentDidMount: function() {
        CountryStore.bind( 'change', this.forceUpdate());
    },

    changeCountry: function(){
      flightDispatcher.dispatch({
        actionType: 'country-update',
        selectedCountry: 'Australia'
      })
    },

    render: function() {

      // Remember, ListStore is global!
      // There's no need to pass it around
      var countries = CountryStore;

      return <div>
          <ul>
              { countries }
          </ul>

          <button onClick={ this.changeCountry }>Country</button>

      </div>;
    }

  {% endhighlight %}
</code></pre>

Resources used for this post:
(http://blog.andrewray.me/flux-for-stupid-people/?utm_source=devmag.io)
(https://facebook.github.io/flux/docs/actions-and-the-dispatcher.html#content)
(https://devmag.io/post/1232/why-does-react-emphasize-on-unidirectional-data-flow-and-flux-architecture)
