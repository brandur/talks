Post-Rails? Composable Apps with a First-class API
==================================================

* Agenda

    * What happened to us.
    * Composable apps.
    * Best practices (platform / testing).

* What happened to us.

    * API team at Heroku.
        * If you've ever tried out the CLI, you've used our API
        * `heroku list`
        * `GET https://api.heroku.com/apps`
        * `curl --user ":$API_KEY" -i https://api.heroku.com/apps`
    * Meet core.
    * This thing got big, to the point where development velocity was impacted severely.
        * Normal sized in the beginning.
        * THEN HUGE.
    * We started breaking pieces out.
        * Process management.
        * Billing.
        * Addons.
        * Kernel layer infrastructure management.
        * Domain management
    * BUTC
        * BREAK UP THE CORE
    * But it was still too big -- we wanted less code!!
        * We're an API, why should our web component get special treatment?
    * Rails has taught us that this is how to design an API:

        ``` ruby
        responds_to do |format|
          format.json { render :json => @apps }
          format.html
        end
        ```

        * That's what we'd done, but it's not the final answer.
    * DASHBOARD WAS BORN (pic).
    * Implementation details.
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

        * Dashboard is built on the same library that powers the CLI!
        * Rails
    * What about Backbone (the fat client)?
        * We enjoy the Ruby experience
        * Puts all the elements in place for a fat client -- plus enables graceful degredation

        ```
        <BOOTSTRAP CODE>
        ```
    * Metrics
        * Dashboard
            * + 4500 LOCs (3200 Ruby + 1300 templates)
        * Core
            * 66k LOCs (61k Ruby + 5k templates)
            * --> 55k LOCs (55k Ruby)
            * - 11k (~15%)
            * Not the final win, but we'll take it.
    * The best part? Our API can now be an API. (in sharp contrast to our previous system)

        Core    -->     Core     -->        Core            -->    *API*
                -->    Billing
                                 -->  Process Management
                                                            -->     Web
    * Providing a strong API has other advantages
        * Manager -- our new system for teams and organization management, built on the same API
        * Manager itself is two tiers
            * API
            * Web


* Composable apps.

    * Composability
        ```
            API     <-      CLI + developers

             |
             v

            Web     <-      Other users
        ```
    * Service oriented architecture
        * Loosely coupled components
        * Encourages strong contracts (they're required)
        * Independent scaling of each service
        * API is a critical factor the increasingly important mobile component
    * Rails as frontend
        * Great at interface
            * Template options
            * Asset pipeline
        * Maintenance of state through sessions
        * Security considerations: XSS/CSRF/etc.
    * Rails as API
        * Helpers/views become less useful
        * ActiveRecord pretty good, but it's now decoupled
        * `ActiveRecord::Serialization#to_json` is a bad idea when strong contracts are important
        * Rails' routing DSL isn't a particularly useful abstraction when you're thinking directly in nouns and verbs
    * First-class APIs
        * We're not post-Rails, we just moved it up a layer.
    * Logistically
        * Smaller teams
        * Happiness
            * Designers and frontend people get to work on a thin web app
            * Backend people don't have to worry about CSS float problems
        * Internal self-service 
    * Use an agnostic protocol (stay HTTP)
      * Then use the right tool for the right job

          ```
          --> Ruby (psmgr)       --> Ruby (API)   --> Ruby (Dashboard)
          --> Erlang (logplex)                    --> Scala (Manager API) --> Ruby (Manager web)
          ```

* Best practices (platform / testing).

    * Stubs
        * More pieces in the system make development and testing harder
        * You've just composed your apps to streamline your work; making setup difficult is the last thing you want to do now
    * excon-artifice
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

    * And it's a Rack app!
        * Add to your `Procfile`

            ``` bash
            web:         bundle exec thin start -R config.ru -e $RACK_ENV -p $PORT
            billing_api: bundle exec thin start -R stubs/billing_api.ru -e $RACK_ENV -p $PORT
            process_api: bundle exec thin start -R stubs/process_api.ru -e $RACK_ENV -p $PORT
            ```

        * Deploy onto your platform
    * Platform
        * Use Heroku, obviously
    * ... but if you can't
        * The pain to deploy a new app must be low
        * The pain to deploy a new app formation should be low
        * Allow reconfiguration
            * Web can point to a production API or a deployed stub

* Investigating the future?
