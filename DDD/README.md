# Domain Driven Design

## Table of Contents
1. [Introduction](#1-introduction)
2. [Strategic Design](#2-strategic-design)

	2.1. [Domain](#21-domain)
	
	2.2. [Domain Model](#21-domain-model)
	
	2.3. [Ubiquitous Language](#23-ubiquitous-language)
	
	2.4. [Bounded Context](#24-bounded-context)
	
	2.5. [Context Map](#25-context-map)
	
	2.6 [Integration Patterns](#26-integration-patterns)
	
3. [Tactical Design](#3=tactical-design)

	3.1. [Value Objects](#31-value-objects)
	
	3.2. [Entities](#32-entities)
	
	3.3. [Aggregates](#33-aggregates)
	
	3.4. [Services](#34-services)
	
	3.5. [Repositories](#35-repositories)
	
	3.6. [Factories](#36-factories)

4. [Architecture](#4-architecture)

5. [Event Storming](#5-event-storming)

	5.1. [Why Event Storming?](#51-why-event-storming)
	
	5.2. [Vocabulary](#52-vocabulary)

	5.3. [How does it work?](#53-how-does-it-work)

## 1. Introduction

**Domain-Driven Design**  (DDD) has been around since  **Eric Evans**  published his book about the subject in 2003. 

It is an approach for architecting software design by looking at software in top-down approach.

- End users decide whether our system is great or not.
- If the system does not solve the business needs, then it is of no use to anyone.
- Focus should not be primarily on technology, rather it should be primarily on business

The **DDD** methodology can be divided into two main parts: strategic design and tactical design.
- Strategic DDD:
	- what software we are building and why we are building it?
	- the entire project team are involved, which are the domain experts and the technical team.
	- similar to Object-oriented design where we are forced to think in terms of objects.
- Tactical DDD:
	- how each component is implemented.

**[⬆ back to top](#table-of-contents)**

## 2. Strategic Design

### 2.1. Domain

#### What is a Domain?

- A specified sphere of activity or knowledge.
- _Activity_  is whatever an organization does and  _knowledge_  is how the organization does it.  
- Usually comprehended as the main business of a company.

#### Subdomains

- The domain concept is very broad and abstract.
- To make it more concrete, we can split it up into smaller parts called  _subdomains_.
- subdomain categories:
	1.  Core domains:
		-  is what makes an organization special and different from other organizations
		-  it should receive the highest priority.
		-  features of the core domain should be developed from scratch.
	
	2.  Supporting subdomains:
		- necessary for the organization to succeed, mostly generic, but contains some special features.
		- can start with an existing solution and tweak it to the organization specific needs
		
	3.  Generic subdomains
		- does not contain anything special to the organization, but still needed
		- try to use off-the-shelf software

#### Example

Let’s say we are building an **EMR** (**Electronic Medical Records**) system for smaller clinics. We have identified the following subdomains:

**Core:**
- **Patient Records**  for managing patient medical records (personal information, medical history, etc.).   

**Supporting:**
-  **Lab**  for ordering lab tests and managing test results.
-  **Scheduling**  for scheduling appointments.
 
 **Generic:**   
-  **File Archive**  for storing and managing files that are attached to the patient records.   
-  **Identity Management**  for making sure the right people have access to the right information.

![Example subdomains](https://github.com/OSD-Services/clean-code-csharp/blob/master/DDD/resources/subdomains.png?raw=true)

### 2.2. Domain Model

- identifies real world entities and the relationships between them.
- _not_  the same as a data model.

<img src="https://upload.wikimedia.org/wikipedia/commons/2/2d/Domain_model.png" width="750"/>

### 2.3. Ubiquitous Language

- It is a language that is consistently used by domain experts and developers to describe and discuss the domain.
- You need to be able to discuss the domain with both **developers** and also **domain experts**.
- In order for you to develop an efficient _Ubiquitous Language_ you need to understand the business.
- **active participation**  from the domain experts is absolutely essential for domain driven design to succeed
- The **domain model** is the embodiment of the ubiquitous language.
- When it's hard to explain something, the language is incorrect or incomplete and also the domain model.
- In such case, a domain expert should fill in the gaps. The model then should be corrected, or completely redone.

### 2.4. Bounded Context

- Ideally, there would be a single ubiquitous language and a single model that explain a domain.
- In the real world, business processes may overlap or even conflict: the same word may mean different things or multiple words may mean the same thing.
- To solve this we need **Bounded Contexts**: part of the domain where a particular dialect of the ubiquitous language is used.
- **Bounded Contexts** have both unrelated concepts (such as a support ticket only existing in a customer support context) but also share concepts (such as products and customers). 
- Different contexts may have completely different models of common concepts with mechanisms to map between these polysemic concepts for integration.
- A **Bounded Context** can be implemented as it's own **microservice** or as a part of a **monolithic** system.

#### Example

<img src="https://github.com/OSD-Services/clean-code-csharp/blob/master/DDD/resources/bounded-context.png?raw=true" alt="Conformist" width="650"/>

- What a `Customer` means in the **Sales** context might be completely different from the **Support** context.
- That's why we have 2 different `Customer` and `Product` entities, each is tailored for the needs of it's context.

#### Relationships Between Contexts  

- Ideally, bounded contexts are independent, but in the real world, they will have some kind of relationship.
- Identifying these relationships is important to design the system's architecture.
- **Downstream contexts**: Receive data and events from **Upstream contexts**.
- An  **Upstream context** does not depend on any other contexts, but can cause problems downstream.
- A **Downstream context** is restricted by the dependencies, but does not worry about breaking others.
- A context can be both.

![context-relationships.png](https://github.com/OSD-Services/clean-code-csharp/blob/master/DDD/resources/context-relationships.png?raw=true)

### 2.5. Context Map
 _Context Maps_ help in understanding the whole project, being able to show the relationships between the different _Bounded Contexts_.
 
![Context Maps](https://thedomaindrivendesign.io/wp-content/uploads/2019/03/ContextMap.png)

### 2.6 Integration Patterns
There are several ways to relate  _Bounded Contexts_:

#### Shared Kernel
A shared context between two or more teams, which reduces duplication of code, however, any changes must be combined and notified between teams.

<img src="https://www.oreilly.com/library/view/learning-domain-driven-design/9781098100124/assets/lddd_0402.png" alt="Partnership" width="400"/>

#### Customer / Supplier
It is a relationship between client (downstream) and server (upstream), where the teams are in continuous integration.

<img src="https://www.oreilly.com/library/view/what-is-domain-driven/9781492057802/assets/widd_0403.png" alt="Partnership" width="500"/>

#### Partner
It is the scenario where teams are dependent and need to have a cooperative relationship so that they can meet the development needs of both systems.

<img src="https://www.oreilly.com/library/view/what-is-domain-driven/9781492057802/assets/widd_0401.png" alt="Partnership" width="500"/>

#### Conformist
It is the scenario that involves the upstream and downstream teams, but in this model the upstream team has no motivation to meet the needs of the downstream team.

<img src="https://www.oreilly.com/library/view/what-is-domain-driven/9781492057802/assets/widd_0404.png" alt="Conformist" width="500"/>

#### Anti Corruption Layer
It is the scenario where the client (downstream) creates an intermediate layer that communicates with the upstream context, to meet its own domain model.

<img src="https://www.oreilly.com/library/view/what-is-domain-driven/9781492057802/assets/widd_0405.png" alt="Conformist" width="500"/>

#### Open Host Service
Communication achived by defining protocol from a bounded context. So, who need to integrate with with this context will use this protocol.

<img src="https://www.oreilly.com/library/view/what-is-domain-driven/9781492057802/assets/widd_0406.png" alt="Conformist" width="500"/>

#### Published Language
Published language is often combined with Open Host Service. Communication between bounded contexts achived via well documented shared language such as XML, gRPC etc.

## 3. Tactical Design

### 3.1. Value Objects
- A value object has no identity. It is defined only by the values of its attributes.
- Value objects are immutable i.e. they can't be modified.
- To update a value object, you always create a new instance to replace the old one.
- Value objects can have methods, but those methods should not change the object.
- Typical examples of value objects include colors, dates and times, and currency values.

### 3.2. Entities
An entity is an object with a unique identity that persists over time. For example, in a banking application, customers and accounts would be entities.

- Has a unique id. Doesn't have to always be exposed to users.
- An id may span multiple bounded contexts.
- An entity can hold references to other entities.

Example:

![UML diagram of the Delivery aggregate](https://docs.microsoft.com/en-us/azure/architecture/microservices/images/delivery-entity.png)

`Delivery` is an **Entity**, `REF`, `Location`, and `Confirmation` are **Value Objects**.

### 3.3. Aggregates
- An aggregate defines a consistency boundary around one or more entities.
- Exactly one entity in an aggregate is the root. Lookup is done using the root entity's id.
- Any other entities in the aggregate are children of the root, and are referenced indirectly using the root.

#### Aggregate characteristics
- Same Lifetime: If the aggregate root is deleted, all other entities in the aggregation should be deleted at the same time.
- Same Domain: Objects that do not belong to the same problem domain should not appear in the same aggregation.
- Same Transaction: Objects that are often operated on at the same time often belong to the same aggregation.

![How to Design & Persist Aggregates - Domain-Driven Design w/ TypeScript |  Khalil Stemmler]()

<img src="https://d33wubrfki0l68.cloudfront.net/d7af329ed65f5754cc17833a4609154febf70c98/a3fa0/img/blog/ddd-aggregates/aggregate-clump.svg" alt="Conformist" width="450"/>

### 3.4. Services
- A service is an object that implements some logic without holding any state.
- Domain Service: contains domain logic, usually interaction between multiple entities/models.
- Application Service: contains technical functionality, usually database and API calls.

### 3.5. Repositories
- A Repository  is in charge of persistence: Lookup and Saving.
- They centralize common data access functionality, providing better maintainability and decoupling.
- Define one repository per aggregate.
- Repositories are not mandatory, use them when the persistence logic is complex and worth separating.

<img src="https://docs.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/media/infrastructure-persistence-layer-design/repository-aggregate-database-table-relationships.png" alt="Conformist" width="750"/>

### 3.6. Factories
- A Factory is an object that has the sole responsibility to create other objects.
- Standardizes the instantiation of an object.
- Objects should not be responsible for the creation of other objects.
- Factories are most needed when constructing complex entities, or to choose a particular implementation of an interface based on some state.

## 4. Architecture

<img src="https://domaindrivendesign.org/wp-content/uploads/2021/06/Architecture.png">

[For further reading:](https://herbertograca.com/2017/11/16/explicit-architecture-01-ddd-hexagonal-onion-clean-cqrs-how-i-put-it-all-together/)

## 5. Event Storming

### 5.1 Why Event Storming?

1.  It adds  _Time as_  an underlying axes when describing the processes/workflows happening within the business which help exposing the  _Contexts_.
2. It identifies the  _Capabilities and Relationships_ between _Contexts_.
3. It identifies the _Domain Model_ belonging to the  _Capability._
4. It identifies the  _Messages_  and  _Commands_ that are passed across the  _Contexts_ boundary.
5. Event Storming  _is not_ Domain-Driven Design_._  While event storming is based on several DDD concepts — including bounded contexts and aggregates, event storming focuses on an interactive collaborative whiteboard exercise that engages all domain experts.

### 5.2 Vocabulary

1.  **Commands** (represented with blue colored sticky notes): These are instructions issued to the system so that it takes some action. The system reaches a new state after the action/operation has happened. These can originate from a user or system or by another event. As an example  _Create New Loan Application_ is a command.
2.  **Events** (represented with orange colored sticky notes): These are something significant that happened within the system when a command has been processed. Typically a domain event causes the generation of new instances of the domain model or alteration of state for existing instances. As it is an  _Observed the Reported_ phenomenon (like Application_Created_, Account_Withdrawn_  and so on) - these are often represented in the past tense. Identification of Events  _is the key._  Often Domain Experts and SMEs spearhead the discovery process of Events.
3.  **Aggregate** (represented as thin border): These are “things” that a group of events operate on or generate. Typically aggregates are higher-order business entities represented as nouns. As an example  _Loan Application_ is an Aggregate.
4.  **System**  (represented as pink colored sticky notes): These represent systems involved in the domain. They may issue commands or receive commands along with triggering events. As an example, an external brokering solution which creates Loan Application inside the system, is a system. Systems in this context can be 3rd party services or an internal Self-Contained service that happens to interact with the application in question.
5.  **User** (represented as yellow colored sticky notes): These are human users involved in the process. They may be a single person or a department/team. Yellow sticky notes help show how complicated the workflow of a business process can be based on the number of departments involved and the amount of back and forth.
6.  **Read Model**  (represented as green colored sticky notes): This represents data that may be critical for a user or system to make a decision. It can be helpful when there needs to be an emphasis on what data the user sees.
7.  **Policy** (represented as purple colored  sticky notes): These represent standards or rules that may need to be executed, such as rules for a compliance policy.

### 5.3 How does it work?

<img src="https://virtualddd.com/static/0f36aa95d3ea73c85b66651576f53328/a2510/process-picture.jpg">

1. Event Storming sessions are collaborative workshops among significant stakeholders.
2. Usually, product owners and development teams meet in a room or online and participate in the session.
3. Anyone who has a clear understanding of  _what happens within part/all_ of the system should be invited to this meeting.

During the Event Storming process we perform the following steps in sequence:

1.  Identification of Domain Events

<img src="https://github.com/OSD-Services/clean-code-csharp/blob/master/DDD/resources/1.png?raw=true">

2.  Time Sequencing of Domain Events

<img src="https://github.com/OSD-Services/clean-code-csharp/blob/master/DDD/resources/2.png?raw=true">

3.  Identification of Triggers/Commands

<img src="https://github.com/OSD-Services/clean-code-csharp/blob/master/DDD/resources/3.png?raw=true">

4.  Identification of Aggregates

<img src="https://github.com/OSD-Services/clean-code-csharp/blob/master/DDD/resources/4.png?raw=true">

5.  Identification of Bounded Context

<img src="https://github.com/OSD-Services/clean-code-csharp/blob/master/DDD/resources/5.png?raw=true">

**[⬆ back to top](#table-of-contents)**
