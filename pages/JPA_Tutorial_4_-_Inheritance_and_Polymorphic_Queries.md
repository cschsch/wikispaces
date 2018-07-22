[[toc]]
[[EJB 3 and Java Persistence API|<-Back]]
# Inheritance and Polymorphic Queries
This tutorial picks up at the end of [[JPA Tutorial 3 - A Mini Application]]. If you have not completed that tutorial, you can start with this source base: 
> [[file:JpaTutoria3Solution.jar]]

## Background
Ignoring joints, a typical query essentially hits one table and returns back subset of the records in that single table. With JPA, we are not hitting tables but we are rather dealing with entities. What is the difference? An entity might map to a single table, multiple tables, or it could be involved in some kind of inheritance relationship.

What happens when we perform a query on an entity type that serves as a base class? It turns out that the actual "work" to make this happen is very simple. This tutorial along with the exercises gives you all the experience you'll need to figure out so-called polymorphic queries.
----
## Introduction
[[include page="JPA Tutorial 4 - Introduction"]]
----
## Setup
[[include page="JPA Tutorial 4 - Setup"]]
----
## V3 Requirements: Different Kinds of Resources
[[include page="JPA Tutorial 4 - Different Kinds of Resources"]]
----
## V3 Adding a Second Kind of Resource
[[include page="JPA Tutorial 4 - Adding a Second Kind of Resource"]]
----
## V3 Assignments
[[include page="JPA Tutorial 4 - Assignments"]]
----
## Individual Links
[[JPA Tutorial 4 - Introduction]]
[[JPA Tutorial 4 - Setup]]
[[JPA Tutorial 4 - Different Kinds of Resources]]   
[[JPA Tutorial 4 - Adding a Second Kind of Resource]]   
[[JPA Tutorial 4 - Assignments]]

[[EJB 3 and Java Persistence API|<--Back]]