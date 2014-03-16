# API Days Berlin 2014 Proposal

## Title

The Story of a Thousand Services: How a Constellation of Apps With First-class APIs Underpin a Platform

## Abstract

This talk tells the story of how a large Ruby app that did too much was broken down over time into a mesh of services, and how the company running it learnt re-used the patterns that were developed while building its public API to establish compelling techniques internally around how to build APIs for those services.

Along the way we'll extract fragments of the story and turn them into more broadly applicable lessons:

* Some backstory around how a public API was designed first to set precedent for internal products.
* Considerations for designing a set of authoritative API conventions that will pave the way for future services.
* How to establish consistency across API's inside an organization by defining and adopting convention and using declarative tools to make it more difficult to break from the pattern.
* How to build and leverage API/service toolkits to bootstrap new components.
* Best practices for gaining visibility into how services are speaking to each other and debugging complex interactions when things go wrong.
* Some challenges that may arise from heavy internal API use like performance and service reliability, and how to mitigate them.

## Bio

Brandur is a Canadian computer engineer working for Heroku in San Francisco. While his career started doing .NET work for big enterprise, he eventually found nirvana working with open-source languages like Ruby. Today he works on the Heroku platform API that empowers developers around the world, and acts as the nerve center for Heroku's distributed platform.
