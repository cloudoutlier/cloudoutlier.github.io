---
title: "How To Define Microservices Using Domain Driven Design"
categories:
  - Principles and Patterns
tags:
  - Domain Driven Design
  - microservices
---
Size may not matter, but getting the boundaries right certainly does. I’ll show you how to define microservices that align with your business.

This article will help you define your microservices, which is the process of identifying the scope and boundaries of each service. This is distinct from the design of your microservices.

## How Big Is Micro?

What makes a service “micro”? And how small (or big) should a microservice be? The answer is, of course, is “it depends”. The reason is because “service” has been a very overloaded term in the software industry over the years. To precisely answer this question would require a deep dive into the history and various (often vague and subjective) uses of this term.

Fortunately, we can skip past most of this noise and confusion if you simply accept the fact that there is no definitive answer. A service by any name is as big or small as it needs to be. Fortunately, there are principled and methodical approaches for identifying service boundaries, and I'm going to show you one of them.

## Beware The Distributed Monolith Anti-pattern

One pitfall you definitely want to avoid, whether you are refactoring an existing monolith or starting a greenfield project, is defining microservices through the same lens as a monolith.

Any well-designed monolithic architecture will contain conceptual boundaries (let’s assume we’re not talking about a big ball of mud and spaghetti code). These boundaries may consist of layers of abstraction, such as presentation, business, and data integration services. They may even include distinct components within each of these layers.

These components may even be described as services (remember, an overloaded term). However, because these components were designed in the context of a single-process monolith, they will still tend to be relatively highly coupled to one another.

Interface design for in-process component integration tends to favour fine-grained exchange of small messages using shared-memory constructs. Interface design for inter-process service integration is another matter entirely and requires consideration of a host of unique factors, such as network protocols, data marshalling and unmarshalling, communication failures and dropped, duplicate and out of order messages.

If you simply take an existing set of service, component or layer abstractions from a monolithic design viewpoint as the basis for defining your microservices, you are setting yourself up for a painful lesson in the consequences of network latency.

Instead, you need a methodology that excels at identifying the conceptual boundaries of your system based on a top-down and global perspective. This is where Domain Driven Design comes in.

## Why Domain Driven Design?

This is really two questions in one:

1. What is Domain Driven Design (DDD)?
2. How does DDD help me define my microservices?

Domain Driven Design is an approach to software development that strives to close the gap between technical and non-technical stakeholders. It does this by implementing a model of the business domain in a ubiquitous language shared by all stakeholders.

Domain Driven Design is a large - but worthy - discipline to learn. However, it is conveniently divided into two distinct levels of detail:

1. Strategic Domain Driven Design focuses on decomposing a business domain into a conceptual model of distinct subdomains and their relationships to one another.
2. Tactical Domain Driven Design is a set of design patterns and implementation abstractions within each subdomain model. 

As you’ve probably guessed, Strategic Domain Driven Design is the focus of this article and the method I use in order to define microservices.

But first, there are a few more topics I'd like to discuss to set the stage before I get into the details of Strategic Domain Driven Design.

## Managing Complexity

Any non-trivial system is based on the premise of automating and/or transforming existing business processes. When you step back and look at the big picture, you’ll notice that these business processes include the interactions of people serving in different roles and organizations of the business. For example, you may have interactions with Finance and Accounting, Shipping and Receiving, Customer Relations, Information Technology and Security, Marketing and Content Management, Sales and Operations, etc. There may also be interactions and integration with external systems.

Attempting to create a single all-encompassing model of a business domain is the wrong approach.

+ There is too much breadth (moving parts to orchestrate) and depth (areas of specialization) for any one person or team to effectively reason about. 
+ Conflicts will inevitably arise in the model due to the use of terms which have multiple meanings across different areas of the business. I’ll get into this in more detail when I discuss domains and subdomains, but the result is typically a breakdown in communication that limits the quality of the software system.
+ It doesn’t scale very well. There are no identifiable boundaries to delegate modelling and implementation activities as the system grows in size and complexity. This typically results in top-heavy processes and bottlenecks in the throughput of deliverables.

You need an approach to that scales to the complexity of the business domain and to the organization that will be building and maintaining it. In other words, you need to break down a business domain into manageable parts.

## Divide and Conquer

Let’s take a lesson from Cognitive Load Theory. The human brain relies on what is called working memory to make decisions and solve problems. Cognitive load is the processing demand placed on working memory. The capacity of working memory (our mental bandwidth) is limited to around seven elements at a time. An effective strategy for solving a complex problem is to recursively break it down into two or more sub-problems until each problem is simple enough (cognitive load is within the capacity of our working memory) to solve directly.

## Organizational Alignment

[Conway’s Law](http://www.melconway.com/Home/Conways_Law.html) provides some clues on what to consider when dividing a complex system up into manageable parts.

> Any organization that designs a system (defined broadly) will produce a design whose structure is a copy of the organization's communication structure.

What word would you use to describe the optimal communication structure when taking a divide and conquer approach to modeling and building a software system?

How about this one?

> [Autonomy](https://www.oxfordlearnersdictionaries.com/definition/english/autonomy) - the freedom for a country, a region or an organization to govern itself independently. 

Autonomy allows teams to operate with a minimum of external dependencies and oversight. There will be interactions, dependencies, and collaboration between teams, to be sure, but it’s the boundaries between these teams that will determine how effective, efficient, and scalable these interactions are as you add more and more components to the system. 

If a boundary between two sets of services are too coupled, each of the responsible teams will frequently be impacted by changes the other makes. This will require a lot of communication overhead to coordinate changes in lock step with one another during development and deployment, slowing the velocity of both teams in the process. As you add more services and teams to the picture, the number of potential boundaries multiplies, and the impact of changes to any one service grows exponentially.

How do you mitigate the likelihood of this outcome? By defining microservices which are loosely coupled to one another, especially those managed by separate teams.

## Strategic Domain Driven Design

Strategic Domain Driven Design is all about boundaries: the boundaries within a business domain, the boundaries within a  software system, and the boundaries within the organizations that are stakeholders of the software system.

## Domains

A Domain can have many meanings, but in the context of a software system, it refers to the overall domain of a business, such as an insurance provider, hotel chain, or an airline.  A software Domain will typically consist of multiple Subdomains that focus on different functional areas of the business.

As a proud Canadian, I'm going to use a national television sitcom, [Kim's Convenience](https://en.wikipedia.org/wiki/Kim%27s_Convenience), as an analogy.

Imagine a small family owned convenience store in Toronto. Mom and Dad arrived 20 years ago from Korea and have two teenage kids. Mom and Dad may run the bulk of the business operations, with their children chip in on weekends and the odd evening after school. Mom manages the books and Dad takes care of most of the unloading and stocking of inventory as it arrives. They split time at the retail counter with help from the kids. Otherwise, everyone shares hats to do whatever needs to get done to keep the business thriving. The kids are fluent in both languages, but English is a second language for the parents, so they tend to speak their native language for efficiency, and only conversing in English when interacting with customers and external business partners.

If this were a software system, it would be a monolith. This model works well enough for a business this size but, as you can imagine, the cracks would show as the number of employees increased. It would break down entirely if you attempted to scale this same model up to franchise levels.

## Subdomains

Any reasonably sized business has different departments, such as Finance, Human Resources, Shipping and Receiving, Order Fulfillment, Inventory, etc. Each of these departments are teams that specialize in a specific functional area that defines their boundaries of expertise. They will interact with other departments for relatively infrequent and well-defined reasons, but the interactions within the department will be more frequent and related to their internal functions. You certainly wouldn’t expect Jane from Finance to be called into the warehouse with Jim from Order Fulfillment and Jackie from Inventory to load a pallet onto a truck in order to ensure the specifics of a contract are fulfilled. 

It is really so surprising that a well-designed enterprise software system would adopt similar principles?

Not all Subdomains are equal. Domain Driven Design classifies them as follows:

* Any successful business understands and prioritizes its core competencies. recognizes the value in this principle and stresses the importance of identifying the Core Domain. This domain deserves the highest priority and resourcing to ensure it is afforded the best opportunity to excel.
* Supporting Subdomains are essential to the business, but secondary to the Core.
* Generic Subdomains are required, but not distinct or specialized to the business and are the likeliest candidate to acquire or externalize rather than build.

## Bounded Context

In Domain Driven Design, a Bounded Context is a representation of a subdomain. What are the differences between a Bounded Context and a subdomain? 

* A Bounded Context is an explicit boundary of a software domain model of a real world subdomain.
* The domain model within a Bounded Context expresses a Ubiquitous Language in which all of the terms and phrases have specific meaning.

Bounded Contexts allows teams to effectively work within a large business domain by providing explicit boundaries in which similar terminology may have completely different meaning.

For example, consider a business domain that includes both Accounting and Legal subdomains. The term Settlement may be used in both of these subdomains, but they represent entirely different concepts. Accounting refers to a settlement as the payment of an outstanding balance that brings the account balance to zero. In a Legal context, a settlement is a resolution between disputing parties about a legal case. 

An attempt to create a single comprehensive model of this business domain would result in a conflict over how to model a Settlement. Concessions would have to be made to the expressiveness of the model on one side or the other - or both - to minimize confusion, especially when communicating between the two subdomains.  

Separate Legal and Accounting Bounded Contexts would allow both teams the freedom to model their subdomains using their preferred Ubiquitous Language. There are now clear boundaries and reference points for modelling and communication within, and between, each Bounded Context.

## Problem Space vs. Solution Space

There are two important, but discrete steps when applying the concept of a Bounded Context to defining microservices. 

First, you need to assess the problem space. Identify the business challenge you are attempting to solve. This will usually highlight the parts of the Domain that form a Core Domain. Then look for business functions that support this Core Domain. Do any Subdomains stand out? Distinguish if they are Supporting Subdomains or Generic Subdomains. What skills and personnel are needed for each of these Subdomains and if they are available to contribute to the appropriate teams.  The combination of Core Domain, Subdomains, and the organizational resource within them are your problem space. 

Second, model the problem space as a set of Bounded Contexts. This model-oriented view of the problem space is the solution space.  You want to align your Bounded Contexts as closely as possible with your Subdomains.

The existing systems and the technologies in place will factor significantly into your solution space. If you are building a set of greenfield microservices, this is the template you want to follow, and it is pretty straightforward to model your core and supporting Bounded Contexts within your Domain.

If you are working with an existing system, it’s unlikely to have such well defined boundaries between it’s components, and may even include functionality outside your problem space. What I suggest is to apply the same process. At a high level,

* Model your bounded contexts within your problem space Domain, and include the legacy system. Be clear whether it fits entirely within your problem space and domain or if it merely intersects. 
* Draw the legacy in your model so that it overlaps with the subdomains of your problem space that it supports
* Draw associations between each of the solution space Bounded Contexts and intersections of the legacy system within each Subdomain.

Keep in mind the majority of effort should be dedicated to the Core Domain. Then, consider when any of the Supporting Domains are Generic Domains and can be satisfied with an existing system or an off the shelf solution.

Now let’s talk about some other aspects of Strategic Domain Driven Design that will factor heavily in your solution space.

## Are We There Yet?

Almost. Kudos if you’ve made it this far. If you haven’t figured it out yet, we’ve covered enough information to answer the question this article poses. Before I spell it out, however, be sure to keep reading as the following sections provide critical strategic information on for integrating your microservices with one another. Now, on to the answer.

How can I use Domain Driven Design to define my microservices? Start by defining your Bounded Contexts. They serve as  conceptual containers for your microservices. Where you go from there will depend on the situation. Start with the premise of a single microservice within each Bounded Context and explore the needs of your problem space and solution space. You may find a need to break things down further. Some clarification and things to consider:

* A Bounded Context is not a microservice, but it may contain one.
* A microservice is not a Bounded Context, but in the context of DDD, it may exist in one.
* A Bounded Context may contain one or more monoliths or microservices, or both. For example, a microservice may serve as a facade to a legacy monolith to express functionality as part of a model of the Bounded Context in the Ubiquitous Language.
* Consider Conway’s Law and the organizational alignment with your bounded contexts and microservices.
* Consider the lessons from Cognitive Theory and whether you need to break down a microservice further, in terms of how it is composed internally, or whether it is appropriate to factor out additional microservices. 
* Favour high cohesion over tight coupling. Be wary of creating a Distributed Monolith or introducing unnecessary network latency.
* Seek to maximize autonomy within each Bounded Context.

 


