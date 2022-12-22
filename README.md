# Intro

<img src=assets/monolith_to_microservices.png align="right" width=150 height=200>

The book “[Monolith To Microservices](https://samnewman.io/books/monolith-to-microservices/)” by Sam Newman is one of the “must-read” for a developer.

This book explains, from the author's experience, how to break down a monolith to microservices in an incremental way. 
Describes the entire migration process, including strategies with practical examples that help solidify what has been learned.  
The author of the book discusses the pros and cons of Microservices, and makes you think about whether you really need to adopt the microservices architecture for what you are trying to solve.

# Disclaimer

This is my summary of the concepts that I have highlighted from each chapter. I have written these notes as a quick reference guide.  
This summary is not intended to be a replacement for the book, in the book are explained concepts with examples and develops deeply more concepts, with really useful images to understand these concepts, with extense examples, and much more.

So please, if you are interested, get the [original book](https://samnewman.io/books/monolith-to-microservices/) for the full overview. I think it is a book that must have at home, underline and reread. 🤓

If the publisher thinks this guide should be private, please let me know (saralerma2@gmail.com) and I will make it private.

Feel free to send suggestions, I will be really grateful 🤍

# Contents

- [Chapter 1. Just Enough Microservices](#chapter-1-just-enough-microservices)
  - [What Are Microservices?](#what-are-microservices)
  - [The Monolith](#the-monolith)
  - [On Coupling And Cohesion](#on-coupling-and-cohesion)
  - [Just Enough Domain-Driven Design](#just-enough-domain-driven-design)
- [Chapter 2. Planning A Migration](#chapter-2-planning-a-migration)
  - [Understanding The Goal](#understanding-the-goal)
  - [Why Might You Choose Microservices?](#why-might-you-choose-microservices)
  - [When Might Microservices Be A Bad Idea?](#when-might-microservices-be-a-bad-idea)
  - [Trade-offs](#trade-offs)
  - [Taking People On The Journey](#taking-people-on-the-journey)
  - [Changing Organizations](#changing-organizations)
  - [Importance Of Incremental Migration](#importance-of-incremental-migration)
  - [Cost Of Change](#cost-of-change)
  - [Domain-Driven Design](#domain-driven-design)
  - [Reorganizing Teams](#reorganizing-teams)
  - [How Will You Know If It's Working?](#how-will-you-know-if-its-working)
- [Chapter 3. Splitting The Monolith](#chapter-3-splitting-the-monolith)
  - [To Change The Monolith, Or Not?](#to-change-the-monolith-or-not)
  - [Pattern: Strangler Fig Applications](#pattern-strangler-fig-applications)
  - [Changing Behavior While Migrating Functionality](#changing-behavior-while-migrating-functionality)
  - [Pattern: UI Composition](#pattern-ui-composition)
  - [Pattern: Branch by Abstraction](#pattern-branch-by-abstraction)
  - [Pattern: Parallel Run](#pattern-parallel-run)
  - [Pattern: Decorating Collaborator](#pattern-decorating-collaborator)
  - [Pattern: Change Data Capture](#pattern-change-data-capture)
- [Chapter 4. Decomposing The Database](#chapter-4-decomposing-the-database)
  - [Pattern: The Shared Database](#pattern-the-shared-database)
  - [Pattern: Database View](#pattern-database-view)
  - [Pattern: Database Wrapping Service](#pattern-database-wrapping-service)
  - [Pattern: Database-as-a-Service Interface](#pattern-database-as-a-service-interface)
  - [Transfering Ownership](#transfering-ownership)
  - [Data Synchronization](#data-synchronization)
  - [Pattern: Synchronize Data In Application](#pattern-synchronize-data-in-application)
  - [Pattern: Tracer Write](#pattern-tracer-write)
  - [Splitting Apart The Database](#splitting-apart-the-database)
  - [Pattern: Split Table](#pattern-split-table)
  - [Pattern: Move Foreign-Key Relationship To Code](#pattern-move-foreign-key-relationship-to-code)
  - [Transactions](#transactions)
  - [Sagas](#sagas)
- [Chapter 5. Growing Pains](#chapter-5-growing-pains)
  - [More Services, More Pain](#more-services-more-pain)
  - [Ownership at scale](#ownership-at-scale)
  - [Breaking Changes](#breaking-changes)
  - [Reporting](#reporting)
  - [Monitoring and Troubleshooting](#monitoring-and-troubleshooting)
  - [Local Developer Experience](#local-developer-experience)
  - [Running Too Many Things](#running-too-many-things)
  - [End-to-end Testing](#end-to-end-testing)
  - [Global Verses Local Optimization](#global-verses-local-optimization)
  - [Robustness and Resiliency](#robustness-and-resiliency)
  - [Orphaned Services](#orphaned-services)

# Personal notes - concepts
"Keep the pipes dumb, the endpoints smart"

# Chapter 1. Just Enough Microservices
## What Are Microservices?
Microservices are modules that communicate via networks and can be independently deployable. Type of SOA(Service-Oriented Architecture).  
Microservices are:
- **independently deployable**: able to make a change to your service and deploy the service into production without having to change anything else.
- **modeled around a Business Domain**: the business domain drives the system architecture. Alignment between IT artifacts(microservices) and business domain. 
- **own their own data**: encapsulate data storage and retrieval, exposing data, via well-defined interfaces. So databases are hidden inside the service boundary.
- **technology agnostic**: you can use different development stacks. Each service can be implemented with the most suitable development stacks for itself (for example, Python, Java, Go and so on).
> Tip: Start with the technology that you know.  

Microservices goal is to have "as small an interface as possible".

### Advantages
- Improving the scale and **robustness** of systems - Isolated modules will prevent a bug in one module from bringing down the entire service.
- **Developers can focus** on just one service, just part of the system.
- Allow **mix** and match **technology**
- **Flexibility** - give you many options regarding how you can solve problems in the future.  

Check [Why Might You Choose Microservices?](#why-might-you-choose-microservices)
### Disadvantages
- Increase **latency** due to communication between computers over networks.
- Get **consistent view of data** across multiple machines.

### User interfaces
Don't leave UI as a monolith layer, microservices are no just in server side.

### Size
Don't worry about size, focus on:
- *How many microservices can you handle?* As you have more services, the complexity of your system will increase. Better incremental migration to a microservice architecture.
- *How to define boundaries to get the most out of the microservices, without everything becoming a horrible coupled mess?*

## The Monolith
A monolith app is an application with all the functionalities built in a **single unit**, so all functionality in a system had to be deployed together, is a **unit of deployment**.  
However, microservice applications are the smaller services that break down the single unit. They run independently and work together.

Types of monolithic systems:
1. **The Single Process Monolith**: system in which all of the code is deployed as a single process. These systems can be simple distributed systems because they nearly always end up reading data from or storing data into a database.  
*Modular monolith* is a variation of the single process monolith, where the code inside the process is separate into modules, each of which can be worked on independently, but which still need to be combined for deployment.  
Advantage: It allows a high degree of parallel working.  
Challenge: The DB tends to decompose along the same line as the modules. 
2. **The Distributed Monolith**: system that consists of multiple services, but for whatever reason the entire system has to be deployed together.
3. **Third-Party Black-Box Systems**: software developed by other people, and you don’t have the ability to change the code.  
Example: payroll systems, CRM systems, HR systems, etc.

### Advantages
- Simpler developer workflows and code reuse.
- Easier monitoring and troubleshooting.
- Faster run end-to-end testing.
### Disadvantages
- Implementation and deployment coupling.
- Delivery contention - microservices give you more concrete boundaries in a system around ownership.
## On Coupling And Cohesion
These concepts are directly linked.  
**Coupling**: how changing one thing requires a change in another.  
There are different types of coupling; *Implementation coupling*, *Temporal coupling* (in the sense of sync calls), *Deployment coupling* (release trains) and *Domain coupling*.  

**Cohesion**: how you group related code. "The code that changes together, stays together".  
Ensure a change involves modifications to as few services as possible to keep the cost of change low.
## Just Enough Domain-Driven Design
In DDD(Domain-Driven Design):
- **Aggregate** is a representation of a real domain concept, a self-contained unit that have a life cycle around them. Example: an Order, Invoice, etc.
- **Bounded context** is a larger organizational boundary inside the organization. Hide implementation and internal details.  

The **Aggregate** is a self-contained state machine that focuses on a single domain concept in your system, with the **Bounded Context** representing a collection of associated aggregates, again with an explicit interface to the wider world.

# Chapter 2: Planning A Migration
## Understanding The Goal 

⚠️
**Microservices are not the goal. And "reuse" is not a goal for microservice migration.**   

Key questions:
- **_What are you hoping to achieve?_** What are you going to achieve that you cannot with your current architecture? This should be aligned to your business and has a benefit to the end users of the system.
- **_Have you considered alternatives to using microservices?_** There are alternatives to achieve some of the same benefits that microservices bring.
- **_How will you know if the transition is working?_**
## Why Might You Choose Microservices?
1. **Improve team autonomy**: Keep organizational groups small, allowing them to build close bonds and work effectively together without bringing in too much bureaucracy -> helped many organizations grow and scale more effectively.  
Alternatives:
    - Modular monoliths allow giving ownership to parts of the code to different teams.
    - Assign responsibilities based on functional grounds.
    - Self-servicing approaches like provisioning machines environments, avoiding the need for central operations teams.
2. **Reduce time to market**: Release functionalities is more quickly because you can deploy changes to individual microservices without having to wait for coordinated releases.   
Alternatives:
    - Carry out some sort of path-to-production modeling exercise to find the biggest blocker. Value stream mapping to identify bottlenecks.
3. **Scale cost-effectively for load**: you can cost-effectively scale, scaling up only those parts of your processing that are currently constraining your ability to handle load. Giving you more control over operational costs.   
Alternatives:
    - Vertical or horizontal scaling.
4. **Improve robustness**: like functionality is decomposed, an impact on one area of functionality need not bring down the whole system. 
Focus on critical parts of your system remain operational.
Have in consideration that microservices don't guarantee improved robustness for free, it may increase the surface area of failure.   
Alternatives:
    - Adding redundancy - Running multiple copies of your monolith behind a load distribution mechanism.
    - Distributing instances of the monolith across multiple failures planes - use reliable SW and HW(have machines in different racks/data centers).
    - Automate manual processes.
    >  **Resilience vs. Robustness**: **Robustness** is the ability to have a system that is able to react to *expected* variations. **Resilience** is having an organization capable of adapting to things that haven’t been thought of, which could well include creating a culture of experimentation through things like chaos engineering.
5. **Scale number of developers**: In sw, often the output from one piece of work is needed as the input for another.  
Alternatives:
    - Modular monolith (different teams own each module, but act of deployment still requires coordination between teams)
6. **Embrace new technology**: microservices choices technologies for each service.
## When Might Microservices Be A Bad Idea?
1. **Unclear domain**: Getting services wrong can lead to a large number of cross-service changes and overly coupled components.
2. **Startups**: Microservices can be a great option for companies that have established at least the fundamentals of their product/market fit, and are now scaling to increase profitability.   
Startups, are often experimenting with various ideas in an attempt to find a fit with customers - resulting in huge shifts in the product domain.
3. **Customer installed and managed software**: If you create software that is packaged and send to customers who then operate it themselves, typically you target a specific platform/deployment environments, and you are quite possibly packaging your monolithic software using mechanisms that are well understood by people who manage these systems.
4. **Not having a good reason**: don't have a clear idea of what exactly it's that you are trying to achieve.

## Trade-offs
- Trying to change many things at once can lead to confusing priorities.  
- Use sliders to balance the competing priorities you may have.  
- Like priorities can change, it can help you to make a decision.

## Changing Organizations
Kotter’s eight-step process for implementing org change:
1. **Establish a sense of urgency**.
2. **Creating the Guiding Coalition**: gather people inside your organization to help you to drive this change forward.
3. **Developing a Vision and Strategy**:  
Vision: **what** change you are hoping to bring. It must balance between realistic and aspirational.  
Strategy: **how** you are going to get there.
4. **Communicating the change vision**: share messages face to face will be more effective.
5. **Empowering employees for broad-based action**: it means remove roadblocks.
6. **Generating short-team wins**: to see the progress being made, if not they will lose faith in the vision.
7. **Consolidating gains and producing more change**: Keep pushing on but it's important to pause and reflect after successes and failures to think about how to keep driving the change.
8. **Anchoring new approaches in the culture**: Communicate successes and failures.
## Importance Of Incremental Migration
Small changes, little steps. In this way, if you make a small change, it's much easier to spot and fix a problem you create.  
Choose one or two areas of functionality, implement them as microservices, get them deployed into production, and reflect on whether it worked. 
## Cost Of Change
You should understand the impact of each alteration you make and change course if required. This allows you to better mitigate the cost of mistakes.  
Difference between *Reversible* and *Irreversible* decisions with examples along the spectrum.   

<img src=assets/reversible_and_irreversible_decisions_spectrum.png width=490 height=200>  

To manage the risk try to make mistakes where the impact will be lowest, like in a whiteboard.
## Domain-Driven Design
Start with DDD, which helps you to define boundaries for your services. Develop a domain model help you to prioritize the decomposition too.  
What you need from a domain model is just enough information to make a reasonable decision about where to start your decomposition.  
Maybe you can use *Event Storming* -> a collaborative exercise involving technical and nontechnical stakeholders who together define a shared domain model.
### Using a Domain Model for Prioritization 
Based on the number of upstream or downstream dependencies, you can extrapolate a view regarding which functionality is likely to be easier or harder to extract.  

<p align="center">
  <img src=assets/bounded_contexts_and_relationships.png width=300 height=150> 
</p> 

Note that domain model represents a *logical view* of an existing system. There is no guarantee that the underlying code structure of your monolith is structured in this way.  
 
You can use a simple two-axis model with the value that you think the decomposition will bring and order things based on their difficulty.   
And stuff in the top right represents functionality you think should be easy to extract, and will also deliver some benefit.   

<p align="center">
<img src=assets/priorization_quadrant.png width=400 height=300>  
</p> 

## Reorganizing Teams
Align architecture and organization to get the most out of a microservice architecture. Avoid silos and build independent, autonomous teams, able to be responsible for more of the end-to-end delivery cycle than ever before. Their focus is on different areas of the product, rather than a specific technology or activity.  
Don't copy business model from other successful companies, you must understand your organization. Understand if and how your organization needs to change needs to be based on your context, your working culture, and your people.  
Asset the skills that people from your team need/want improve, you can use skills chart.
## How Will You Know If It's Working?
Having regular checkpoints. Make sure you cover:
1. Restate what you are expecting the transition to microservices to achieve. Check if it still make sense.
2. Review quantitative metrics, for example, cycle time, failure rates, etc.
3. Ask for qualitative feedback.
4. Decide if any change is needed.  

Avoid the sunk cost fallacy: sunk cost fallacy occurs when people become so invested in a previous approach to doing something that even if evidence shows the approach isn’t working, they’ll still proceed anyway.
# Chapter 3: Splitting The Monolith
Remember that the migration should be done in an **incremental** way. You should migrate over to a microservice architecture in small steps, allowing you to learn from the process and change your mind if needed.
## To Change The Monolith, Or Not?
Better if you copy the code from the monolith, and at this stage, at least, you won't remove this functionality from the monolith itself. Because leaving the functionality in the monolith for a period of time gives you more options. It may give you a rollback point, or perhaps an opportunity to run both implementations in parallel. 
**Challenge**: The existing codebase are traditionally not organized around business domain concepts. Technical categorizations are more prominent, for example MVC(Model-View-Controller).

Consider first break its monolith apart into a **modular monolith** and later check if it's needed migrate to a microservices architecture.

Next, **migration patterns** will be shown, techniques that allow you to migrate the architecture of a monolith to a microservices architecture.
## Pattern: Strangler Fig Applications
Technique where the new system initially be supported by, and wrapping, the existing system. The idea is that the old and the new can coexist, giving the new system time to grow and potentially entirely replace the old system.  
### Advantages
- Allow incremental migration.
- Gives you the ability to pause and even stop the migration.
- Safe and quick rollback by redirections.

## Steps
1. Identify functionality to migrate.
2. Move functionality to microservice.
3. Redirect calls to new microservice.

> Deployment vs. release: Just because software is *deployed* into a given environment doesn’t mean it’s actually being used by customers(*released*).  
### When use this pattern?

The strangler fig pattern doesn’t work too well when the functionality to be moved is deeper inside the existing system, there must be a clear way to redirect the calls to the new service.   

Techniques depending on the protocol:
1. **HTTP Reverse Proxy**: 
  Introduce a reverse HTTP proxy in front of the monolith (between the upstream calls and the downstream monolith).   
  Steps: Insert proxy, migrate functionality and redirect calls.  
  Consider implement the redirection around URI paths (/invoice), and use feature toggle(aka. feature flag) to change the redirection.  
  Proxy options:
    - NGINX with Lua if you need customize behavior.
    - If your microservice is going to use a different protocol that existing in monolith, you can use your proxy to transform the protocol. But it will add complexity and logic in the proxy, remind "Keep the pipes dumb, the endpoints smart".
    Better solution:
      - Implement it in the microservice, in this way the service expose its capabilities over multiple types of protocol.
      - Use service meshes, where each service instance communicates with other service instances via its own, dedicated local proxy.
2. **FTP**: Intercept FTP uploads (by detecting changes in the FTP server log), and direct newly uploaded files to an adapter that converted the uploaded files into requests to the new REST API.
3. **Message interception**: Methods to intercept the calls, and to redirect them to your new microservice.
    - **Content-based routing**: intercept all messages intended for the downstream monolith, and filter the messages on to the appropriate location.   
    This technique allows you to leave the monolith untouched, but you are placing another queue on your request path, which could add additional latency.  
    Remind "Keep the pipes dumb, the endpoints smart", try to not add complexity in terms of how calls are routed between your systems.
    - **Selective consumption**:  change the monolith and have it ignore sent messages that your new service should receive. Here, the new service and the monolith share the same queue and locally use some sort of pattern matching process to listen for messages of interest to them.  
    With this technique, you don't need to create an additional queue, but messaging technology can't allow you to share a simple queue subscription, and to redirect calls, it requires 2 coordinated changes (same for rollback).

    So, use *Selective consumption* with only a small number of consumers and/or with a simple set of filtering rules and *Content-based routing* when number of types of consumers increases.
## Changing Behavior While Migrating Functionality
When migrating functionality, avoid changes in the behavior being moved, delay new features or bug fixes until the migration is complete, if you can. Otherwise, you may reduce your ability to roll back changes to your system.  
## Pattern: UI Composition
In the UI, you can also splice functionality provided in part from an existing monolith or new microservice architecture.
- **Page Composition**: page-based migration in order to allow a brand-new look and feel to go live. You can use Content Delivery Network (CDN) to implement the new routing rules, effectively using the CDN much as you might use an internal proxy.
- **Widget Composition**: For widget-based web UIs, the goal is deploy a single widget. You can use Edge-Side Includes (ESI) technique, using Apache, to define a template in your web page, and a web server splices in this content. 
Mobile applications are monoliths, unless you can make changes without resubmitting them.
- **Micro Frontends**: break down a user interface into different components that can worked on independently.
## Pattern: Branch by Abstraction
Often people will do that work on a separate source code branch but later it's a challenge merged the branch (conflicts and things don't match).  
**Solution**: With Branch by Abstraction you can make changes to the existing codebase allowing implementations to safely coexist alongside each other, in the same version of code, without causing too much disruption.  
Steps:
1. Create an abstraction for the functionality to be replaced.
2. Change clients of the existing functionality to use the new abstraction.
3. Create a new implementation of the abstraction with the reworked functionality. In your case, this new implementation will call out to your new microservice. Here, although you have two implementations of the abstraction in the codebase at the same time, only one implementation is currently active in the system.
4. Switch over the abstraction to use your new implementation. You can use a Feature toggle (aka. Feature Flag) to change the implementation being used without change code.
5. Clean up the abstraction and remove the old implementation.

Fallback mechanism: "Verify branch by abstraction" is a pattern where also implements a live verification - if calls to the new implementation fail, then the old implementation could be used instead.  

Challenge? add code complexity, harder reasoning about the system and data consistency (challenge when switching between implementations).

### When use this pattern?
When you need to make changes in the monolith maybe because the functionality you want to extract is deeper inside your existing system.   
When changes to the existing code are going to take time to carry out. 

## Pattern: Parallel Run
Both the [Pattern: Strangler Fig Applications](#pattern-strangler-fig-applications) and [Pattern: Branch by Abstraction](#pattern-branch-by-abstraction) allow old and new implementations of the same functionality to *coexist* in production at the same time.  
When using a parallel run, rather than calling either the old or the new implementation, instead you call **both**, allowing you to compare the results to ensure they are equivalent. But only one is considered the source of truth at any give time.   
This technique can be used to verify:
- new implementation is giving the same answers as the existing implementation
- if it's operating within acceptable nonfunctional parameters (example: with acceptable failure rate, time to response, many time-outs, etc.).

Verification techniques:
- Use Spy in microservices during parallel run to record what will do, but without doing it. Use it when duplicated side-effects are risky. For example, to check you are not sending same message twice to the customer.
- Github scientist library - It’s a Ruby library that allows you to run implementations side by side and capture information about the new implementation to help you understand if it is working properly.

> Note parallel run is different from *canary releasing* (involves directing some subset of your users to the new functionality, an the rest of users seeing the old implementation) and *dark launching* (where you deploy the new functionality and test it, but the new functionality is invisible to your users).

### When use this pattern?
Use this pattern just when the functionality being changed is considered to be high risk.
## Pattern: Decorating Collaborator
Allows you to attach new functionality to something without the underlying thing knowing anything about it.  
You are going to use a decorator to make it appear that your monolith is making calls to your services directly, even though you haven’t actually changed the underlying monolith.
Proxy works as a decorator that calls new microservice in addition to tunnel responses back to the customer.  
**Challenge**: When request/response don't have enough information requires for new service, then the service needs to call back into the monolith to extract the required information. It generates additional load.  
### When use this pattern?
Use when cannot or don’t want to change monolith. 
## Pattern: Change Data Capture
With change data capture, rather than trying to intercept and act on calls made into the monolith, you react to changes made in a datastore.   
**Challenge**: For change data capture to work, the underlying capture system has to be coupled to the monolith’s datastore.   
Techniques:
1. **Database triggers**: When an operation is done in a table, trigger custom behavior. Use triggers very sparingly because if there are many triggers will be hard understand how the system works.
2. **Transaction log pollers**: Inside the DB exist transaction logs where is written a record of all the changes made in DB. 
3. **Batch Delta copier**: program that on a regular schedule scans the DB in question for what data has changed, and copies this data to the destination. Maybe you can use, for example, *cron* to run these jobs.

### When use this pattern?
Use it when you need to replicate data.
# Chapter 4: Decomposing The Database
Microservices work better when encapsulate fully their own data storage and use retrieval mechanisms.  
The first four patterns don't directly fix the shared database problem, they are a patch for when the DB cannot be divided:
## Pattern: The Shared Database
The major issue related to sharing a single database among multiple services is that you deny yourself the opportunity to decide what is shared and what is hidden. So, it can be difficult to understand what parts of a schema can be changed safely and it's unclear who "controls" the data.

> Thinking about a microservice as a combination of behavior and state, encapsulating one or more state machines. 
If the behavior that changes this state is now spread around the system, making sure this state machine can be properly implemented is a tricky issue.
 
So, when splitting the database apart to allow for each microservice to own its own data is not possible, you can use database view pattern or database wrapping service pattern.
### When use this pattern?
Cases where multiple services accessing the same DB can be appropriate: 
- Read-only static reference data that is stable and change control of this data is typically handled as an admin task.
- A service is exposing a DB as a defined endpoint ([Pattern: Database-as-a-Service Interface](#pattern-database-as-a-service-interface))

> Schema is a logically separated set of tables that hold data. Multiple schemas can then be hosted on a single DB engine.
## Pattern: Database View
With a view, a service can be presented with a schema that is a limited projection from an underlying schema.  
This projection can limit the data that is visible to the service, hiding information that it shouldn’t have access to. Giving you control over what is showed and what is hidden.  

<p align="center">
  <img src=assets/database_view_pattern.png width=400 height=200>
</p>
Limitations:
- View is just **read-only**, this limits their usefulness.
- Not all DB support views or limitations like the need to both (source schema and view) to be in the same DB engine -> SPOF (Single Point Of Failure)
- Tied to a particular technology stack.

### When use this pattern?
- you want a single source of data for multiple services.
- when is impractical to decompose the existing monolithic schema.
- the effort of full decomposition is too great.

## Pattern: Database Wrapping Service
Hide the DB behind a service that acts as a thin wrapper, moving DB dependencies to become service dependencies.
Reduce dependence on a central DB.
It allows you control over what is showed and what is hidden because it presents an interface to consumers that can be fixed.
The wrapping service can also take writes (via API calls) but it require upstream consumers to make changes; they have to shift from direct DB access to API calls.

<p align="center">
  <img src=assets/database_wrapping_service_pattern.png width=400 height=200>
</p>

### When use this pattern?
- Underlying schema is just too hard to consider pulling apart. By placing an explicit wrapper around the schema, and making it clear that the data can be accessed only through that schema.

## Pattern: Database-as-a-Service Interface
Dedicated DB designed to be exposed externally as a read-only endpoint, and have this database populated when the data in the underlying DB changes.  
It exposes a read/write endpoint via an API and a DB as a read-only interface.  
A mapping engine acts as abstraction layer between internal and external DB, it takes changes in the internal DB, and works out what changes need to be made in the external DB. It allows remain hidden the internal DB exposing a dedicated DB as an endpoint.

You can implement a Mapping engine through several mechanism like change data capture(CDC) system, batch process just copying the data over or listening to events fired from the service and use that to update the external DB. 

**Pros**: With this approach the DB can be different technology stack.  
**Cons**: Restrictions on how this interface can evolve. 
### When use this pattern?
- Clients just need a DB to query, just need read-only access. 

## Transfering Ownership
Transfer ownership of data to corresponding microservice.  
The microservice encapsulates the logic associated with one or more aggregates, so you also need to move the management of its state and associated data into the microservice schema itself.  
And if your new microservice needs to interact with an aggregate that is still owned by the monolith, you need to expose this capability via a well-defined interface.
### Pattern: Aggregate Exposing Monolith
Exposing information from the monolith via a proper service interface, and through this interface, your microservice can access to that information.  
You are exposing data and allowed operations to external parties, so you can restrict what state of an aggregate is exposed from your service boundary and to limit what state transition operations can be requested from the outside.  
As a pathway to extract more services -> Extract from the monolith a service for the Aggregate exposing an API aggregate related data.

### When use this pattern?
- When a new X service needs to access data that is owned by other functionality, where you need to access Y data.

### Pattern: Change Data Ownership
When the data that is currently in the monolith and should be under the control of your newly extracted service (business logic extracted):  
The new service takes the ownership of the related data, and the monolith accesses  that data by a service endpoint.  
If monolith only needs read access, you might consider projecting a view from the new microservice DB.

## Data Synchronization
**Challenge** - when the service in question manages data that will need to be kept in sync, in a consistent way, between both the monolith and the new service.  
Approaches depending on the degree of consistent data between the two views:
- If either set of code needs to always see a totally consistent view - data should keep in one place. New service read data from the monolith for a short space of time (for example, using a view), and once switch is done, migrate the data.
- If is a big-bang switchover (migrate both, the app code and the data at same time - try avoid!), you could use a batch process to copy the data over in advance of switching to the new microservice. Once related data is copied into new microservice, start serving traffic. But if you need to fall back, you could end up losing state.
- Keep both DBs in sync via the code, so either the monolith or the new service can make writes to both DBs. 
### Pattern: Synchronize Data In Application
Steps:
1. **Bulk synchronize data**: Batch data migration from old system to new DB. If existing system kept running, as the data in the source system could change, once batch import completed, you can implement CDC to copy data since snapshot created.
2. **Synchronize on write, read from old schema**: write all data in both DBs to ensure the app write correctly in both sources and new DB behaves within acceptable tolerances. 
3. **Synchronize on write, read from new schema**: read from new and old DB still keep in sync(write in both DB), so if there is an issue, you can fallback.
4. **Remove old schema** when new system is bedded in enough.
### When use this pattern?
- Split schema before split the app code.
- Inability to take the app offline for any length of time.
### Pattern: Tracer Write
Same as [Pattern: Synchronize Data In Application](#pattern-synchronize-data-in-application), but migrating data in an incremental way - one table at a time (instead of the whole context) using the new microservice API.  
You can start with a small set of data being synchronized and increase this over time, while also increasing the number of consumers of the new source of data. It allows you to reduce the impact of each release.  

**Challenge**: duplicated-consistency  
**Solutions**: 
1. **Write to one source**: data is sync after write.
2. **Send writes to both sources**: it requires clients make call to each source or an intermediary to broadcast the request.
3. **Seed writes to either sources**: it requires data sync in two-way between the systems (try avoid, it's difficult to achieve).  

Depends on window if inconsistency (appropriate time can be one of the systems without be updated). Long window of inconsistency -> eventual consistency, eventually, both sources of truth will have the same data.
#### When use this pattern?
If you are already making use of an event-driven system, or have a change data capture (CDC) pipeline available, then you probably already have a lot of the building blocks available to you to get the sync working.
## Splitting Apart The Database
### Physical vs. Logical DB Separation
For breaking apart the databases, you first try *logical separation* (one DB engine hosting different logically separated schemas).
And a further step is *physical separation* too, where each logical schema are hosting in separate DB engines.  
**Pros**
+ *Logical decomposition* allows for simpler independent change and information hiding. 
+ *Physical decomposition* potentially improves system robustness, and could help remove resource contention, allowing for improved throughput or latency.  

**Cons**
- *Logical decomposition* may be required to expose views of your DB. And 1 DB engine is a potential Single Point Of Failure (SPOF). But nowadays many database engines have mechanism to avoid SPOF.

### Splitting the Database First, or the Code?
1. **Split the database first, then the code**  
    **Cons**:
      - With a separate schema, you’ll be potentially increasing the number of database calls to perform a single action. 
      - You end up breaking transactional integrity when you move to two schemas.  

    **Pros**:  
      + Ability to revert or continue to modify things without impacting any consumers of your service if you realize you are going the wrong way.  
      
    When concern about performance or data consistency issues. But if the monolith itself is a black-box system, you can't use this option. 

    **Pattern: Repository per Bounded Context (BC)** - A repository layer binds your code to the DB, making it easy to map objects or data structures to and from the DB. Break down these repositories along the lines of BC -> understand dependencies first.  

    **Pattern: Database per Bounded Context (BC)** - Separate DB around the lines of identified BC(each BC has its own separate DB schema). Bet for future split of each microservice owning own data.  

2. **Split the code first, then the database**  
    Most common - It becomes easier to understand what data is needed by the new service.   
    A concern with this approach is that the teams may get this far and then stop, leaving a shared database.  
    **Pattern: Monolith as data access layer** - Same pattern as “aggregate exposing monolith”. Exposing an API on the monolith allows the service to avoid direct data binding. 
    #### When use this pattern?
    Use when data is going to stay in the monolith or when code managing this data is still in the monolith.  

    > Microservice - encapsulation of the state, the code that manages and the transitions of the state.  
    
    **Pattern: Multischema storage** - New microservice uses new schema for new functionality/data (not in old DB), but still accesses old schema for existing data. 
    #### When use this pattern?
    Use when adding a new functionality to microservices that requires the storage of new data, which is not needed in monolith (new functionality is not in the monolith). 
3. **Split them both at once** - Try to avoid - much bigger and longer step to take.
## Schema Separation Examples
Low level data decomposition patterns:
### Pattern: Split Table
When a table is owned by two or more service boundaries (bounded context) in your monolith, and you need to split the table -> You can declare a *foreign-key* relationship between both tables but splitting tables like this you will lose the safety given to you by database transactions.   
If you find specific columns in that table that seem to be updated by multiple parts of your codebase, you need to make a judgment call as to who should “own” that data.
### Pattern: Move Foreign-Key Relationship To Code
Split services code and their data in different schemas but same DB engine.
- No possible 1 simple join query to 1 select + n services - calls increases latency. First understand acceptable latency.  
Solution - caching.
- Data consistency: with 1 schema you wouldn't be able to delete a row if there was a reference to another table, but with separate schemas you can have this type of inconsistency.  
Solutions: 
  - check with all services before deleting (coupling), 
  - handle deletion gracefully in dependant service (returning 410/404 code) or 
  - don't allow deletion (soft deletion or create "graveyard" table). 
### Example: Shared Static Data
Static reference data, which changes infrequently, yet is typically critical.  
Approaches for managing it:
#### **Pattern: Duplicate static reference data**
Needed change in multiple places but as it changes infrequently maybe it's acceptable.   
#### When use this pattern?
For large volume of data, when it’s not essential for all services to see the exact same set of data.
#### **Pattern: Dedicated reference data schema**
To have 1 source of truth, you could relocate static data to a dedicated schema. 
+ No duplication and always updated. 
#### When use this pattern?
For large volumes of data, or when you want the option of cross-schema joins.
#### **Pattern: Static reference data library**
Store static data in a library that can be shared between services and statically linked by any services that want this data.
- Not be able to share a single shared library if you have a mix of technology stacks.
- Services deployable dependant of library changes. And accept have different versions of the library in microservices.
#### When use this pattern?
For small volumes of data, where accept different services seeing different versions of that data.
#### **Pattern: Static reference data service**
Create a dedicated service to serve static data. And this data can be cached on the client side.
Consider use events to let consumers know that data has changed.
#### When use this pattern?
When creating a new microservice is cheap.
## Transactions
When you split data across databases, you lose the benefit of using a database transaction to apply changes in state in an atomically.  

> A database transaction is a sequence of multiple operations performed on a DB, and all served as a single logical unit of work — taking place wholly or not at all.  

ACID is an acronym that describes the key properties of database transactions that lead to a system you can rely on to ensure the durability and consistency of your data storage:
1. **Atomicity**: Ensures that all operations completed within the transaction either all complete or all fail. If any of the changes you are trying to make, fail for some reason, then the whole operation is aborted, and it’s as though no changes were ever made.
2. **Consistency**: When changes are made to your database, you ensure it is left in a valid, consistent state.
3. **Isolation**: It allows multiple transactions to operate at the same time without interfering. This is accomplished by making sure that any intermediate state changes made during a transaction are invisible to other transactions.
4. **Durability**: It makes sure that once a transaction has been completed, you are sure that the data will not be lost in the event of some system failure.

Atomicity tends to be the first issue you hit when splitting transactional boundaries. Because since the tables are in different databases, now the changes are done in the scope of two different transactions -> **distributed transactions** (see two-phase commit algorithm for implementing distributed transactions).  
Avoid using distributed transactions because you need to lock resources for long periods of time. 
Alternatives - not split DB or sagas.

## Sagas
Algorithm that can coordinate multiple changes in the state, modeling transactions as business processes (activities that can be executed independently).  
Saga is a **long operations**, and you should break down Long Lived Transactions(LLTs) into a sequence of transactions, each of which can be handled independently.  
The idea is that the duration of each of these “sub” transactions will be shorter and will modify only a part of the data affected by the entire LLT.  
Note: you don’t have atomicity at the level of the saga itself, you only have atomicity for each "subtransaction" inside the LLT.

Saga Failure Modes:
- *Backward recovery* involves reverting the failure, and cleaning up afterwards (a rollback). For this to work, you need to define compensating actions that allow you to undo previously committed transactions. 
- *Forward recovery* allows you to pick up from the point where the failure occurred, and keep processing. For that to work, you need to be able to retry transactions.

### Saga rollbacks
With an ACID transaction, a rollback occurs before a commit. But with saga, you have multiple transactions involved, and some of those may have already committed before you decide to roll back the entire operation.  
**Solution**: A compensating transaction is an operation that undoes a previously committed transaction. But these compensating transactions are **semantic** rollbacks because you can’t have exactly the same behavior as that of a normal database rollback since the transaction did happen, you are just creating a new transaction to revert original transaction.  
Two ways of Saga implementation:
1. **Orchestrated**:
- Central coordinator (*orchestrator*) to define the order of execution and to trigger any required compensating action.
- **Pros**: easier to understand.
- **Cons**: coupling and centralized logic (logic that should otherwise be pushed into the services can start to instead become placed in the orchestrator).  
- Solution for centralized logic - different services can play the coordinator role for different flows.
- Business Process modeling (BPM) tools are designed for non-dev to define business process flows, the issue is that they are non-dev tools that end up being used by devs 🥲.
2. **Choreographed**:
- Distributed responsibility for the operation of the saga among multiple collaborating services. Trust but verify!
- Usually event based for collaboration between services.
- **Pros**: decoupled.
- **Cons**: harder to understand whole flow.
- Use correlation Id to build/know the state of the saga, and a service to read all events to show an overview.

**Recommendation**:  
Use *orchestrated sagas* when one team owns implementation of the entire saga.   
And use *choreographed saga* when multiple teams are involved, as it is easier to distribute responsibility for implementing the saga to the teams, with the more loosely coupled architecture allowing these teams to work more in isolation.
# Chapter 5: Growing Pains

## More Services, More Pain
The number of services could be an indicator when certain issues can manifest. 
Based on the author's experience, these are the issues often manifest depending on the number of services;
2-10 services (single team or a few teams):
- Breaking changes.
- Reporting.
10-50 services:
- Ownership at scale.
- Developer experience.
- Running too many things.
50+ services (more teams/developers):
- Global vs local optimization.
- Orphaned services.
In all:
- Robustness and resilience.
- Monitoring and troubleshooting.
- End-to-end testing.
More coupling architecture, more issues about robustness, testing and tracing will appear earlier.
## Ownership at scale
Looking at ownership from a point of view of making changes in the code, you can differentiate between strong, weak and collective ownership.   
- In strong, all services have owners (just one team can change service, other teams can do pull requests to propose changes),   
- in weak, most of the services are owned by someone and   
- in collective, no one owns anything.  

Without strong code ownership a microservices’ architecture will grow into a distributed monolith.
## Breaking Changes
A microservice is part of a wider system, it consumes or/and provides functionality to other microservices. So, there is a "contract" defining the expected behavior of the microservice, and if in any microservice is changed some functionality that affects that contract -> breaking changes.  

Avoiding accidental breaking changes:
1. Eliminate accidental breaking changes:
    1. Have an **explicit schema** for your microservice to detect structural breakages in your contract. [Protolock](https://github.com/nilslice/protolock) to prevent incompatible changes.
    2. To avoid semantic breakages: **testing**.
2. If you can, **expand the changes to your contracts**. Try to find ways to support old while still supporting new. 
2. Give **consumers** time to **migrate**: Run two versions of the microservice or run one version but support old and new APIs in the service.
## Reporting
With a microservice architecture, you don't have a monolithic database to get reports, now you will gather the data for the report across multiple logically isolated schemas.  
**Solution**: create a single DB/schema to store data for reporting purposes, and all microservices can copy the appropriate data.
 
## Monitoring and Troubleshooting
Mechanisms to monitor and troubleshoot problems:
- **Log aggregation**: capture all your logs and forward them to a central location. First thing to do when implementing microservices!
- **Tracing**: Collate a series of flows and look at them as a whole. For that you can generate a *correlation ID* for all calls coming into your system.
- **Test in production**: Use ***Synthetic transactions*** for simulate user's behavior into your system, defining the behavior you expect and defining alerts. Use end-to-end test cases in production. 
- **Toward observability**: Handle known causes of problems - disk space running out, a service instance not responding, latency, etc. Read [ Distributed Systems Observability](https://www.oreilly.com/library/view/distributed-systems-observability/9781492033431/).

## Local Developer Experience
More services, more resource-intensive runtimes (like JVM) in developer's computer. It's needed to limit how many things devs should run locally.   
Solutions:
- "stub out" the services that you don't want to run yourself.
- point to instance that is running elsewhere (for example point to beta environment).
- mix local and remote - example [Telepresence](https://www.telepresence.io/) for Kubernetes.
## Running Too Many Things
**Desired state management** is the ability for you to specify the number and location of service instances that you require and ensure that this is maintained over time. It's difficult manage this if each microservice has a different desired state.  
Deployment, configuration and management of instances becomes more difficult.   
**Solution**:
- Kubernetes for manage the deployment of your service instances across multiple machines, ensuring you can scale to improve robustness and handle load. 
- Function-as-a-Service (preferred).
## End-to-end Testing
With microservices, you have to run tests across multiple services, all of which need to be deployed and appropriately configured for the test scenarios.  
You also have to be prepared for the false negatives that occur when environmental issues.  
**Solutions**:
1. **Limit scope** of functional automated tests: avoid larger-scoped tests that cross team boundaries.
2. Use **consumer-driven contracts** (CDCs) where the consumer of your microservice define their expectations of how your service should behave in terms of an executable specification (a test).
3. Use **automated release remediation** and **progressive delivery**: By defining acceptable measures for how your service should behave, it then becomes possible to control the progressive delivery in an automated way.
4. Continually **refine your quality feedback cycles**.
## Global Versus Local Optimization
Every organization needs to find the right balance between global and local decision making, perhaps the solution that is best globally, is good enough locally.  
The higher the cost of change, the greater the impact, and the more you want a broader consensus behind decision making.  
The smaller the impact, the easier it is to roll back, and the more decisions can be left to local teams.
## Robustness and Resiliency
The number of services increases, and the number of service calls increase, you will become more and more vulnerable to resiliency issues.  
The more interconnected/coupled your services are, the more likely you will suffer from things like cascading failures and back pressure.  
Solutions:
- **Isolating services**, you can use async communication to avoid temporal coupling.
- Run **multiple copies** of services can help with instances dying.

For resiliency you can document production issues when they arise and keep a record of what you learned. 
## Orphaned Services
Orphaned services are services that no one in the company takes ownership of or responsibility for.

---
I have learned many things from this book and I hope it helps you as much as it helped me! 🤗 🤍

---

Content from Monolith To Microservices by Sam Newman.   
Visit https://samnewman.io/books/monolith-to-microservices/ .  
Copyright ©️ Sam Newman, 2020.