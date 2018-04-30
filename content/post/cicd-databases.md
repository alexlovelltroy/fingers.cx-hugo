+++
title = "Strategies for involving databases in CI/CD Pipelines"

# Optional image to display on homepage (relative to `static/img/` folder).
image_preview = "scaled/plates.jpg"

date = 2018-02-21
lastmod = 2018-02-21
draft = false

tags = ["continuous integration", "databases", "devops"]

[header]
image = "headers/header-plates.jpg"
+++

The following post was originally shared on the [Pythian Blog](https://blog.pythian.com/strategies-maintaining-ci-cd-pipelines/)

Pythian specializes in continuous integration and deployment (CI/CD) pipelines for enterprise applications. Our customers call us when they are ready to decompose their monolithic codebases and we help them implement containerized microservices and speed up deployment.

When we take on these projects, we are often asked the same question: How can we move our database changes as fast as our code changes?

With 20 years of experience in production databases, we’ve seen countless transformations to stable production, and we’re able to make clear recommendations. I’d like to share some common patterns for live schema updates in a CI/CD pipeline. They fall into several categories and none are a silver bullet.

The first and most common pattern is to use a schema migration tool like Liquibase or Flyway. Each support schema changes as part of the continuous deployment pipeline. This pattern is simple, but has some drawbacks. To address these drawbacks, teams routinely adopt feature flags and multistage deployments. Each strategy adds guardrails around the activation of schema changes. Both add complexity to the process, but also protect users from bugs.

Next, I’ll address database views. More the purview of the database administrator (DBA) than the developer, views can simplify many schema changes without changing any code. Teams that embed a DBA early are more likely to be able to take advantage of this pattern. In certain circumstances, it can be the fastest to implement and the easiest to manage of any pattern.

Of course there’s no need to solve a problem unnecessarily, if it can be avoided.

# Liquibase And Flyway

The first strategy that many engineering and operations teams consider is to manage schema changes with a tool, such as Liquibase or Flyway. Both allow developers to store database changes with the code in version control. Both also allow the configuration of rollbacks in the same way. For some applications, this is sufficient, but changing a column name or deleting a column, for example, are hard to reverse. And, regardless of how careful developers are, they still need the flexibility of making these backward incompatible changes. So keep in mind that while this type of tool can be part of your strategy, developers also need more options.

# Feature Flags

Application developers are used to using configuration changes to enable or disable features without having to release a new version. This process is called “feature flagging” and can also be used for database changes. In combination with tools to release new schema, it’s possible to have two totally different data models active at the same time in an application with only one of them active at any time and to control that process via configurations. It’s even normal to store this feature flag in the application database so that a single SQL update can switch the whole application from one data schema to another regardless of the number of servers involved.

# Multistage Deployment

Of course, sometimes it isn’t possible to make a full change in a single step. In fact, breaking down a big change into a set of smaller changes usually makes it easier and safer.

Our deployment engineers have managed the online migrations of applications from traditional RDBMS like Oracle and MySQL to columnar databases like DynamoDB and Cassandra without any downtime. Each of these cases involved a carefully orchestrated set of changes over several weeks. We planned each change independently reverted and had relatively small impact. In a recent example, we used multiple data models, feature flags and schema migrations. All actions were planned in detail ahead of time.

The first steps altered the Oracle database schema to look and act more like the Cassandra model we were moving to. Later steps wrote the same data to both data stores and validated consistency. And final steps moved reads from the old database to the new one and removed the connection parameters from our legacy database engine. Each step was wrapped in functional tests and managed by our 24/7 team of operations experts.

# Database Views

As any Oracle DBA will tell you, mapping data changes to application changes is one of the classic use cases for database views. However, because views are traditionally more the purview of DBAs than developers, most developer-focused strategies miss them. For many simple schema changes, adding a view to rename a field or handle a troublesome join within the database will have the lowest application and developer impact. Since views are read-only, they are only a reasonable solution when the read load is higher than the write load, and if the model doesn’t need to be consistent to be useful.

One common use case is using a view to combine various attributes from multiple tables about a “customer” for reporting. For example, if we have tables of addresses and people, our view can map the latest address to the person as well as combine the first and last name fields into a single name field. This is possible without changing any of the underlying schema.

# Move More To Your Data Analytics Pipeline

Finally, as with anything technological, don’t solve a hard problem unless you absolutely must. Chances are good that instead of managing schema changes on an active application database, you can move the complexity of data model changes to the data warehouse or analytics pipeline. In fact, many of the traditional OLTP and OLAP architectures we transform put much of the application analytics in the database.

We spend a surprising amount of time moving page view records and customer interactions from the OLTP system to a streaming data system. Our model for addressing the transformation separates application state machine data from the analytics pipeline and is biased towards analytics.
