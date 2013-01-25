# Testing, Operating, Owning, and Loving Your Service-oriented Architecture

## Public Description

So you've followed industry hints and developed your own service-oriented architecture (SOA). Now what? You've just made a fundamental tradeoff in your system to gain development speed in exchange for a more complex distributed model. How do you keep that system alive and healthy?

We'll talk about the fundamental gains of SOA, but also how to make sure that your system's complexity doesn't spiral out of control, along with topics like:

* Using Artifice and other adapters to test communication with other services
* Testing failure cases to verify that your system behaves sanely
* Patterns for measuring communication with other services in production
* Building out metrics and alerts to monitor stability of your production service
* Having components owned by small and agile teams managing lean codebases

## Private Description

### Intro

* Talk about SOA basics
* Talk about how we leverage SOA at Heroku, and how it's helped us
* Intro to Heroku's API team and an introduction to the various kernel services that it speaks to

### Testing

* Going to SOA leaves a huge hole in testing methodology; testing interaction between components is now significantly more difficult
* We started out with WebMock, and it still works pretty well
* Now we build out Rack stubs that are the canonical contract for how services speak to each other
    * These stubs are fully-fledged Rack apps and are easily platform deployable (platform as in a PaaS)
    * They're also easily deployed in testing environments so that services can test against each other locally
    * Stubs are mutable to represent known failure conditions or unavailability
    * Example!

### Operating

* For production services, operations are key, and there must be constant visibility into all services composing the platform
* Visibility is attained by monitoring all critical components of the system, all the time, and that includes communication with other services
* Examples of simple patterns used to monitor response time and error rates of remote services
* Building out a system of alerts based on those metrics
* Eventually, after proven production reliability, handing those alerts produced by your system over to the teams responsible for the services that trigger them

### Owning

* Decreasing the scope of individual services allows the smaller teams that understand their own service inside and out, and makes iterations on building new features very fast
* Team size should scale with a service's size, but at some point they may fission (both the service splits into smaller services, and the team splits to manage them)
* Teams are responsible for their service, including bugs it contains and system-wide problems that it causes
* Deep understanding of an owned service enables maintainers to quickly and ably identify problems in production and repair them
* Combined with the concept dev/prod parity, the barrier to putting new features and infrastructure into production is signficantly lowered, enabling a constant stream of deploys

## Why should we choose you?

I've developed, tested, operated, and owned the Heroku API for the one year that I've been with the company. At Heroku, we've operated SOA's and built on SOA principles for years, and those of us on the platform engineering team have good insight on how well these idealist concepts lend themselves production load. Rather than talk just about abstract ideas and principles, I'll cover solid theoretical foundation combined with lessons learned in industry.

## Video

Unfortunately, I don't have any recorded talks, but here are my slides from Frozen Rails 2012. https://composable.herokuapp.com/
