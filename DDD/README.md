

# Domain Driven Design

## Table of Contents
1. [Introduction](#introduction)
2. [Strategic Design](#strategic-design)

	2.1. [Domain](#domain)
	
	2.2. [Domain Model](#domain-model)
	
	2.3. [Ubiquitous Language](#ubiquitous-language)
	
	2.4. [Context](#context)
	
	2.5. [Bounded Context](#bounded-context)
	
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

![](https://upload.wikimedia.org/wikipedia/commons/2/2d/Domain_model.png)

### 2.3. Ubiquitous Language

- You need to be able to discuss the domain with both **developers** and also **domain experts**.
- It is a language that is consistently used by domain experts and developers to describe and discuss the domain.
- **active participation**  from the domain experts is absolutely essential for domain driven design to succeed
- The **domain model** is the embodiment of the ubiquitous language.

### 2.4. Context

### 2.5. Bounded Context

### 2.6. Context Maps


## 3. Tactical Design

### 3.1. Value Objects

### 3.2. Entities

### 3.3. Aggregates

### 3.4. Services

### 3.5. Repositories

### 3.6. Factories

**[⬆ back to top](#table-of-contents)**
