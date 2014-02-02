# Lessons Learnt and Leverage Gained from Building the Heroku API

A year ago we started re-building the Heroku Platform API from the patchwork foundation that had grown organically up to that point. We spent months scrutinizing the fine details of every endpoint, building out a codebase that would allow us to support the old and new API versions simultaneously, and figuring out how to ship its associated clients, documentation, and tools to the public.

Shipping a new API was a huge accomplishment for us, but the real achievement was the momentum that we gained building other Heroku components by leveraging the design vision that we'd created for use with the new API, and the tools that we'd built for it. A few of the topics covered during the talk will include:

* Enforcing internal API conventions through JSON schema.
* Open-sourcing tools built for the new API and taking advantage of the community support.
* How the same clients built to access the Heroku API are re-used to talk to other services internally.
* Using JSON schema to build stubs and input/output validation that takes some of the effort out of managing a complex service-oriented architecture by allowing individual components to operate better in isolation.
