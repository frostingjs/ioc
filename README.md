# FrostingJS: Inversion of Control

[![Join the chat at https://gitter.im/frostingjs/community](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/frostingjs/community)

**Note**: This is a fork of the popular InversifyJS however long term, much of code may be replaced as we get closer to a 1.0 release.

## Motivation

There are few libraries that already exist for doing dependency injection inside javascript (with some being typescript focused, and others including browser support):

* [injection-js](https://www.npmjs.com/package/injection-js)
* [typedi](https://www.npmjs.com/package/typedi)
* [tsyringe](https://www.npmjs.com/package/tsyringe)
* [typescript-ioc](https://www.npmjs.com/package/typescript-ioc)
* [inversify](https://www.npmjs.com/package/inversify)

They all work well enough depending on your use-case, however they are too low-level once you realize that your container must be dynamic based on the environment it runs in (dev vs prod, cli vs web app, serverless vs traditional).

Then there are more batteries-included frameworks that provide IoC out-the-box but also lock you into either the specific libraries they choose (orm, http server, validation) or their own proprietary libraries.

* [adonisjs](https://adonisjs.com/)
* [nestjs](https://nestjs.com/)

An similarly, if you prefer a framework to make all the decisions for you then one of those MAY scale with your architecture. However, if you are in the camp who prefer libraries over frameworks, there really isn't a project out there that sits in the middle, hence: FrostingJS.

## Design Philosophy

### Testing First

There tends to be a general design flaw with many open-source JS libraries on NPM today in that they rely on global state/singletons which inherently make them difficult to use for testing. In particular, depending on the what libraries you use, running your tests in parallel inside the same process can be near-impossible. Not only does this IoC want to encourage more performant tests at scale, but will help the community achieve this with "plugins" which wrap popular projects (such as Mongoose) with more test friendly/injectable patterns.

### Server Side Only

Simply put, the needs of a the client vs server are entirely different. If your use-case requires an IoC to be re-usable across both client and server, we'd love to hear about it on Gitter.

### Modular

Not only do we want to wrap popular projects with an IoC friendly interface, but also give users the ability to pick-and-choose which "frameworks" they prefer (express, koa, etc) while adhering to best practices.

### Asynchronous

Most imporantly, this library is built on the assumption that every project should be building their container through some asynchronous design. Meaning, if your application does not have I/O constraints (database) or change behavior depending on where it runs (deployment), then this library will be overkill. Most IoC libraries make the assumption that you are effectively going to build your container structure manually and at boot-time. This assumption easily becomes unmaintainable and breaksdown so as you want to do something like, share common code/services across both a command-line interfaces and a web application. 

**Example:**

You have an application that depends on 3 different I/O services (redis, elasticcache, postgres). In order for your web application to be performant, you always boot up each service at start with a "pool" to re-use connections (pretty standard pattern). However, now you want to add two new components to your code base, a CLI and some serverless functions, either of which may or may not use some of those I/O services (or perhaps they conditionally use all there depending on what is invoked).

Most IoC frameworks today effectively force you to map out (through code), every different container variation in order to avoid connecting to I/O you don't need for your execution path.

* cli:read_from_redis
* cli:read_from_es
* serverless:write_to_redis
* servleless:write_to_es

So with just four different execution paths, I need to have 4 different container configurations that only register the I/O depending on what function I call (because remember, I want to ONLY make a connection to the I/O if i need to). This pretty much defeats the purpose of IoC.

With our approach, container objects are only created when they are needed, and rely on async to be smart about resource utilization. This example is also assuming you have everything in one project, what if you want to modularize across repos with their own connectivity requirements? IoC without async simply does not scale in larger projects.

## Using Today

The current state of this framework is simply a fork from a [PR](https://github.com/inversify/InversifyJS/pull/1132) in the inversify project that had never been merged (as it appears the project has died). You should refer to both that PR and the general InversifyJS documentation as to how to use this library until we get around to documenting.
