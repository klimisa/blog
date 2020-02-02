---
layout: post
title: CQRS Projections
---

The checkpoint is only updated when a new event has been successfully projected, so we guarantee that each event is projected at least once.

There are two types of subscriptions in the Event Store

- catch-up
- persistent subscriptions, also known as competing consumers.

The main difference is the checkpoint ownership.

For the catch-up subscriptions, the best practice here is that the checkpoint is stored in the same place (that is, the database) as the read model itself.

There are two ways to store the checkpoint via:

- a client-based checkpoint
- a server-based checkpoint.

We can logically conclude that a client-based checkpoint is maintained and stored by the client (subscription), and when using a server-based variant, the Event Store is responsible for this instead.

> Info: In Apache Kafka, the term offset is used for the same concept and, by default, the offset is maintained by the server.

\$ce-class/agreegate stream contains all events that happened in a specific class/agreegate are known as category streams ($by-category projection)
\$et-event-type stream contains all events of a specific type from all other streams in the store.

Query options

- Some sort of joins
- In more complex scenarios remote calls

  Problems

  - failures of any service
  - networking issues
  - join each time the API endpoint is called.

_Slim events_ containS the minimal amount of data that is necessary to explain what happened.
Any other information on the make it of what we refer as _fat event_.
