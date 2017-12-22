---
layout: post
title:  "Repository Pattern"
date:   2017-12-08 10:00:00 -0600
categories: jekyll update
---

The last two weeks I've been pair programming with some of our software crafters. This week's pairing involved a design pattern which I've heard mentioned before, and which I did not yet know: the repository pattern.

### What is a repository?

A repository simply represents a collection of entities.  A repository can hold entities, return them all, filter them.  It serves as an authoritative collection whose entities are understood by other parts of the application. How it does this is an implementation detail. Behind your repository, you could be pulling data from a file or a database.  However, the repository obtains information is an implementation details though.  The repository serves to abstract this storage method.

### Place in the application

**Retrieving Data**

The repository connects the business logic, factories, and persistence of an application.  In reading more theoretical descriptions about repository pattern, I found myself wondering, "Well, what about the database?" The image below was extremely helpful in understanding the connection:

<img src="/images/repo.png" style="width: 25%; margin: auto; display: block;">

<img src="/images/repository-with-interfaces.png" style="width: 25%; margin: auto; display: block;">

Source: [The Repository Design Pattern](https://code.tutsplus.com/tutorials/the-repository-design-pattern--net-35804)

So, the repository sits as a central hub between the client, the gateway (your data persistence), and the factory (which creates objects for your repository collection).

Following the path in our images above, goes something like this:

1. The client queries the repository.
2. The repository in turn queries the gateway (our persistence). In the second image above, dependency inversion is applied such that our repository only depends on an abstraction of our gateway.
3. The gateway returns raw data to the repository.
4. Upon receiving this information, the repository reconstitutes this information to the format needed to create a new object in the factory.  In the second image above, the repository again depends on an abstraction of our factory.
5. Our factory creates the object(s) for the repository.
6. The repository returns the object(s) to the client.

**Persisting Data**

A repository may also be used in a similar fashion for creating persistence.  However, the interactions between our factory and other pieces may be modified. The source mentioned above gives an example where our client uses the factory to create the object it wishes to persistence, which is in turn sent to the repository.

---

This pattern enables us to create a class which can be used by any other business logic for retrieving information in a collection.  Additionally, our application can have as many repositories as needed in our domain.  We could have Users, Products, etc. all in different repositories and even using different gateways.