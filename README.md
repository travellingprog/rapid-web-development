# Rapid Web Development

Notes and examples of my web dev knowledge, to build quickly and deploy cheaply.


## Goal

This is **NOT** knowledge for *hacking together* an application, this is knowledge that I plan to use as reference to put together an application with patterns that have succeeded for me on previous projects. This application should be easy to grow and scale afterwards.

Hacking together an application is certainly fun, but IMO should be limited to hackathons. Even for a Proof of Concept, all that time you "saved" upfront hacking something is more than repaid afterwards with the no-fun business of rewriting an application.

A secondary goal is to document how an application can be deployed on the cheap in the short-term.


## Tech Stack

In my experience, what often matters the most when you are picking your tech stack is the **size of the active ecosystem** of the tool you choose, more so than the performance and even the developer experience. When the ecosystem around your tool is big, you can often find some good third-party libraries that can you a ton of time. But it's important that the ecosystem is also currently very active, so that bugs are fixed continuously and you can easily find help if you come across a bug that you cannot solve on your own.

### Backend

Here is the stack that I am currently very familiar with, that scales well.

- Node
- Express
- Postgres
- Sequelize

Of course, many more libraries/tools are added to this stack, but these are the core components that the majority of your code is going to deal with.

With a good use of indexes and lightweight queries, Postgres can be very fast and perfectly suitable by itself for quite a long time. Making use of in-memory caching with [node-cache](https://github.com/mpneuried/nodecache) or [lru-cache](https://github.com/isaacs/node-lru-cache) can extend the scalability of a Postgres-as-the-only-db even further. However, when you make use of either one of those modules, you should wrap them with a service that operates asynchronously, so you can easily replace them with a database engine somewhere down the road.

Depending on the nature of your application, you may need to a database for caching sooner rather than later. Redis is great for that. Memcached is the tradional choice. RocksDB is fairly new, but has gained a lot of praise.

### Frontend

Funny enough, the frontend is in many ways a more complex beast than the backend. Here is the stack that I am currently very familiar with, that scale well.

- React, set up with...
- create-react-app
- Redux
- redux-actions
- redux-thunk
- React Router
- Sass (if necessary)

create-react-app is arguably the most important piece of this stack. While it *does* limit what you can do with your build process, I've found that setting up and maintaining a proper Webpack (or Grunt, or Gulp, or Broccoli, or...) configuration is often an infuriating experience. Even when you have everything working, you'll often find that over time your custom setup become untolerably SLOW. My recommendation: save yourself the pain! On top of that, the docs of create-react-app offer a ton of great tips for things you might want to add to your stack.

React Router is the only component that I hesitate to recommend. While it shines in some aspects (like making the routing *responsive*, so you can have different routes for mobile!), some fundamental needs are just overly complicated by the library's goal to express the routing with React components. Anything having to do with authentication or redirects, in particular, can be difficult to reason about, and lead to some unexpected bugs.

Redux, for people unfamiliar with it, can feel very tedious and boilerplate-y. And in all fairness, it really is not a lightweight solution. But it really does lead to code that is easy to predict and difficult to break. redux-actions helps reduce some of the repetitiveness. Javascript's async/await along with redux-thunk are plenty enough for creating asynchronous action creators.

The one glaring weakness in this stack is the creation of forms. The React world is just not a fun place to build forms, especially with modern UI expectations (validation, error/success messages, loading/submitting states, disabling of buttons while form is invalid, etc.). The most popular library to go along with the above stack is Redux Form. While this library is quite powerful, IMO it is overtly complex, heavy and its internal code is very difficult to debug. I have no form library recommendations at this moment.


### Activity/Event Tracking

One lesson I've recently learned is how crucial it is to track events happening in your application, along with a good amount of metadata to go with that event.


