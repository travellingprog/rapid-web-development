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

- [Node](https://nodejs.org/)
- [Express](http://expressjs.com/)
- [Postgres](https://www.postgresql.org/)
- [Sequelize](sequelize.readthedocs.org)

Of course, many more libraries/tools are added to this stack, but these are the core components that the majority of your code is going to deal with.

With a good use of indexes and lightweight queries, Postgres can be very fast and perfectly suitable by itself for quite a long time. Making use of in-memory caching with [node-cache](https://github.com/mpneuried/nodecache) or [lru-cache](https://github.com/isaacs/node-lru-cache) can extend the scalability of a Postgres-as-the-only-db even further. However, when you make use of either one of those modules, you should wrap them with a service that operates asynchronously, so you can easily replace them with a database engine somewhere down the road.

Depending on the nature of your application, you may need to a database for caching sooner rather than later. Redis is great for that. Memcached is the tradional choice. RocksDB is fairly new, but has gained a lot of praise.

### Frontend

Funny enough, the frontend is in many ways a more complex beast than the backend. Here is the stack that I am currently very familiar with, that scale well.

- [React](https://reactjs.org/), set up with...
- [create-react-app](https://github.com/facebookincubator/create-react-app)
- [Redux](https://redux.js.org/)
- [redux-actions](https://github.com/reduxactions/redux-actions)
- [redux-thunk](https://github.com/gaearon/redux-thunk)
- [React Router](https://github.com/ReactTraining/react-router)
- [Sass](http://sass-lang.com/) (if necessary)

create-react-app is arguably the most important piece of this stack. While it *does* limit what you can do with your build process, I've found that setting up and maintaining a proper Webpack (or Grunt, or Gulp, or Broccoli, or...) configuration is often an infuriating experience. Even when you have everything working, you'll often find that over time your custom setup become untolerably SLOW. My recommendation: save yourself the pain! On top of that, the docs of create-react-app offer a ton of great tips for things you might want to add to your stack.

React Router is the only component that I hesitate to recommend. While it shines in some aspects (like making the routing *responsive*, so you can have different routes for mobile!), some fundamental needs are just overly complicated by the library's goal to express the routing with React components. Anything having to do with authentication or redirects, in particular, can be difficult to reason about, and lead to some unexpected bugs.

Redux, for people unfamiliar with it, can feel very tedious and boilerplate-y. And in all fairness, it really is not a lightweight solution. But it really does lead to code that is easy to predict and difficult to break. redux-actions helps reduce some of the repetitiveness. Javascript's async/await along with redux-thunk are plenty enough for creating asynchronous action creators.

The one glaring weakness in this stack is the creation of forms. The React world is just not a fun place to build forms, especially with modern UI expectations (validation, error/success messages, loading/submitting states, disabling of buttons while form is invalid, etc.). The most popular library to go along with the above stack is Redux Form. While this library is quite powerful, IMO it is overtly complex, heavy and its internal code is very difficult to debug. I have no form library recommendations at this moment.


### Activity/Event Tracking

One lesson I've recently learned is how crucial it is to track events happening in your application, along with a good amount of metadata to go with that event.

The main reason to have some event tracking in your application is to evaluate the user experience (UX) of your application. The more time you spend working on your application, the harder it is to look at it from the point of view of a new or casual user. Therefore, a user flow that you find intuitive could be a big source of frustration for the majority of your users. Without any type of tracking, it's much more difficult to find these weak spots of the UX. Yes, you could conduct a series of user tests to discover some of these weak spots, but nothing validates the need for action more than data gathered from real-world usage.

Therefore, whenever you develop a significant new user-facing feature, think early on about how you're going to track the success or failure of this feature. Often, the criteria for success will be something along the lines of "the vast majority of people who click on button A will afterwards fill out and submit the form on page B" or "most users who open this page will click at least one of these button". Both these examples can be expressed as a percentage (e.g. *# of page B form submitted* / *# of button A clicks*), which represents a **success rate**.

The other big reason to track events is simply to have a lot of data to dig into afterwards, to get insights into your users' preferences and use those insights to help guide your future features and priorities.

[Google Analytics](https://www.google.com/analytics/#?modal_active=none) is the 3rd-party tool that most projects begin with, if only because the free tier provides good enough features for a while. I've recently had success with [Mixpanel](https://mixpanel.com/), although it gets expensive quickly once your want to upgrade from the free tier plan.

Unfortunately, I haven't found an open-source solution yet that provides everything you'll need: front-end page and event tracking, storage of tracking data and visual analytics of this data, with some complex filtering options. However, of these 3 requirements, the first 2 (front-end tracking, data storage) are relatively easy to implement on your own, and it's really the 3rd requirement (data dashboard) that would take a long time to build.  To fulfill that 3rd requirement, a lot of teams make use of [Tableau](https://www.tableau.com/), although there are now interesting open-source alternatives like [Apache Superset](https://github.com/apache/incubator-superset), [Redash](https://github.com/getredash/redash) and [Metabase](https://github.com/metabase/metabase).


### Testing

Having good code coverage with automated tests is something that most developers aspire to, but, in my experience, high code coverage does not make for *rapid* development. It's certainly a fact that in a well-tested codebase, your test code can often grow to be bigger than your application code. On top of that, your most important code is usually interacting with APIs and services (such as the database) that are difficult to mock. Figuring out *how* to test your code can often take longer than writing the code itself.

All that being said, as your application grows bigger and more complex, the absence of automated tests will make you more nervous. As you introduce more and more new features, it becomes more likely that you'll overlook an uncommon scenario in one of your older features. Manual testing certainly has its limits.

Therefore, I believe that it's understandable if, as a balance between speed and reliability, you only write tests for the *most critical* parts of your application.

With the frontend and backend tools I've recommended above, the most appropriate testing framework is [Jest](https://facebook.github.io/jest/). It's unrivaled when it comes to testing React code, and it's quite capable of running tests for backend JS code as well.

If Jest isn't for you, then I believe that both [Mocha](https://mochajs.org/) and [Jasmine](https://jasmine.github.io/) are very good libraries as well. Jasmine is incredibly easy and quick to set up, but Mocha has more flexibility, more power, and a larger ecosystem.


