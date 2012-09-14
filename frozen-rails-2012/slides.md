Intro
-----

@brandur
![Heroku logo](http://logos.heroku.com/images/heroku-logo-light-234x60.png)

Agenda
------

* A story of composition
* Composable apps
* Best practices

A Story of Composition
======================

From thick to thin.

Heroku's API team
-----------------

* If you've used the CLI, you've use the API

    ```
    heroku list
    ```

    ```
    GET https://api.heroku.com/apps
    ```

* Try it!

    ``` bash
    # you can your API key from ~/.netrc
    curl --user ":$API_KEY" -i https://api.heroku.com/apps
    ```

Core
----

* A Rails app
* Then, a **BIG RAILS APP**
* Severe impact on development velocity and effort

Things were broken out
----------------------

* Process management.
* Billing.
* Addons.
* Kernel layer infrastructure management.
* Domain management

BUTC
====

Break Up the Core!

Still too big
-------------

* The goal is something small and agile
* Rails taught us to design an API like this:

    ``` ruby
    responds_to do |format|
      format.json { render :json => @apps }
      format.html
    end
    ```

* But we're an API, why should the web component get special treatment?

Dashboard
---------

@todo
[INSERT SCREENSHOT]

Implementation
--------------

* Rails
* Heroku.rb: the backend that handles API calls for the CLI

    ``` ruby
    # GET /apps
    def get_apps
      request(
        :expects  => 200,
        :method   => :get,
        :path     => "/apps"
      )
    end
    ```

* Dashboard is also built on Heroku.rb

Shout-out to the Fat Client
---------------------------

* We enjoy the Ruby experience
* Puts all the elements in place for a fat client
    * Graceful degredation
* Backbone still recommends bootstrapped models

    ``` js
    <script>
      var Apps = new Backbone.Collection;
      Apps.reset(<%= @apps.to_json %>);
    </script>
    ```

Metrics
-------

* Dashboard
    * + 4500 LOCs (3200 Ruby + 1300 templates)
* Core
    * 66k LOCs (61k Ruby + 5k templates)
    * Down to 55k LOCs (55k Ruby)
    * - 11k (~15%)
    * Not the final win, but we'll take it

Our API is Now an API
---------------------

```
Core    -->     Core     -->        Core            -->    *API*
        -->    Billing
                         -->  Process Management
                                                    -->     Web
```

A Good API is a Reusable API
----------------------------

* Manager -- teams and organization management

@todo
[INSERT SCREENSHOT]

* Manager is further composed into two tiers
    * API
    * Web

Composable Apps
===============

Composability
-------------

```
    API     <-      CLI + developers

     |
     v

    Web     <-      Other users
```

Service oriented architecture
-----------------------------

* Loosely coupled components
* Encourages strong contracts (they're required)
* Independent scaling of each service
* API is a critical factor the increasingly important mobile component

Rails as frontend
-----------------

* Great at interface
    * Template options
    * Asset pipeline
* Maintenance of state through sessions
* Security considerations: XSS/CSRF/etc.

Rails as API
------------

* Helpers/views become less useful
* ActiveRecord pretty good, but it's now decoupled
* `ActiveRecord::Serialization#to_json` is a bad idea when strong contracts are important
* APIs are about verbs and nouns
    * A routing DSL is an unneeded layer of abstraction

First-class APIs
----------------

* We're not post-Rails, we just moved it up a layer

Logistically
------------

* Smaller teams
* Happiness
    * Designers and frontend people get to work on a thin web app
    * Backend people don't have to worry about CSS floats
* Internal self-service

Flexibility
-----------

* Use an agnostic protocol (stay HTTP)
* Then use the right tool for the right job

@todo: real diagram
    ```
    --> Ruby (psmgr)       --> Ruby (API)   --> Ruby (Dashboard)
    --> Erlang (logplex)                    --> Scala (Manager API) --> Ruby (Manager web)
    ```

Best Practices
==============

Stubs
-----

* More pieces in the system make development and testing harder
* You've just composed your apps to streamline your work; setup should also be streamlined

excon-artifice
--------------

* Patches Excon to route calls to a Rack app
* Based on Wycat's Artifice, that does the same thing for Net::HTTP
* Easy stubbing for test purposes

    ``` ruby
    stub(Config).billing_api { "https://billing-api.localhost" }
    stub(Config).process_api { "https://process-api.localhost" }

    # stub with fully functional Rack apps
    Artifice::Excon.activate_for(Config.billing_api, BillingAPIStub.new)
    Artifice::Excon.activate_for(Config.process_api, ProcessAPIStub.new)
    ```

And it's a Rack app!
--------------------

* Add it to your `Procfile`:

    ``` bash
    web:         bundle exec thin start -R config.ru -e $RACK_ENV -p $PORT
    billing_api: bundle exec thin start -R stubs/billing_api.ru -e $RACK_ENV -p $PORT
    process_api: bundle exec thin start -R stubs/process_api.ru -e $RACK_ENV -p $PORT
    ```

* Deploy onto your platform

Platform
--------

* Use Heroku. Obviously.

... but if you can't
--------------------

* The pain to deploy a new app must be low
* The pain to deploy a new app formation should be low
* Allow reconfiguration
    * Web can point to a production API or a deployed stub

@brandur
--------

brandur@mutelight.org
