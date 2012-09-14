Intro
-----

@brandur
![http://logos.heroku.com/images/heroku-logo-light-234x60.png](Heroku logo)

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

Best Practices
==============

