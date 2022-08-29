

# Domain Driven Design

## Table of Contents
1. [Introduction](#introduction)
2. [Strategic Design](#strategic-design)

	2.1. [Domain](#domain)
	
	2.2. [Domain Model](#domain-model)
	
	2.3. [Ubiquitous Language](#ubiquitous-language)
	
	2.4. [Bounded Context](#bounded-context)
	
	2.5. [Context Map](#context-map)
	
	2.6 [Integration Patterns](#integration-patterns)
	
3. [Tactical Design](#tactical-design)

	3.1. [Value Objects](#value-objects)
	
	3.2. [Entities](#entities)
	
	3.3. [Aggregates](#aggregates)
	
	3.4. [Services](#services)
	
	3.5. [Repositories](#repositories)
	
	3.6. [Factories](#factories)


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

![Example subdomains](https://vaadin.com/static/content/learning-center/learn/tutorials/ddd/01__strategic_domain_driven_design/images/subdomains.png)




### 2.2. Domain Model

- identifies real world entities and the relationships between them.
- _not_  the same as a data model.

![](https://upload.wikimedia.org/wikipedia/commons/2/2d/Domain_model.png)

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
![](https://www.martinfowler.com/bliki/images/boundedContext/sketch.png)

- What a `Customer` means in the **Sales** context might be completely different from the **Support** context.
- That's why we have 2 different `Customer` and `Product` entities, each is tailored for the needs of it's context.

#### Relationships Between Contexts  

- Ideally, bounded contexts are independent, but in the real world, they will have some kind of relationship.
- Identifying these relationships is important to design the system's architecture.
- **Downstream contexts**: Receive data and events from **Upstream contexts**.
- An  **Upstream context** does not depend on any other contexts, but can cause problems downstream.
- A **Downstream context** is restricted by the dependencies, but does not worry about breaking others.
- A context can be both.

![Different ways of documenting context relationships graphically](https://vaadin.com/static/content/learning-center/learn/tutorials/ddd/01__strategic_domain_driven_design/images/context_relationships.png)



### 2.5. Context Map

- _Context Maps_ help in understanding the whole project, being able to show the relationships between the different _Bounded Contexts_.
![Context Maps](https://thedomaindrivendesign.io/wp-content/uploads/2019/03/ContextMap.png)

### 2.6 Integration Patterns

## 3. Tactical Design

### 3.1. Value Objects

### 3.2. Entities

### 3.3. Aggregates

### 3.4. Services

### 3.5. Repositories

### 3.6. Factories

**[⬆ back to top](#table-of-contents)**
