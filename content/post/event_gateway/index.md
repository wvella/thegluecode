---
title: "Your Event Streams Deserve a Gateway Too"
description: "You wouldn't expose an API without a gateway. What about Kafka?"
date: 2026-07-06T09:03:57+10:00
image:
math:
license:
hidden: false
comments: true
draft: true
categories: ["Data Streaming", "Gateway"]
tags: ["kafka"]
---

You would never expose a REST API without a gateway in front of it. Authentication, rate limiting, observability, access control - these aren't optional, they're mandatory and we've spent the last decade building the API gateway pattern into our muscle memory.

So the question I want you to think about is: **would you expose an event stream on the internet without a gateway?**

---
## First, what is an event?

Before we talk about an Event Gateway, I think it's worth grounding ourselves. What is an event?

> An event is simply a fact of something that has happened.

### Modern integration is a dual-port problem

In modern integration, we have two main ways to connect systems together.

1 - **A Synchronous Request-Response Port** - An application asks for some information, makes a request, and waits for an answer to come back immediately. A large portion of the internet is built this way, wether it's REST, GraphQL or MCP tool calls.

2 - **An Asynchronous Event Port** - An application can produce or consume Events. A user logging in. A product being added to a cart. A sensor reading from a factory floor. Events are things accumulating in your system.


![Dual Port Integration](dual_port_integration.png)


The tool most commonly used to store and serve these events is **Apache Kafka**. Its core data structure is simple: an append-only log. You never overwrite data - you just keep accumulating what happened, so any number of services can react to it.

For years, we've treated the Synchronous Request-Response Port port as the primary external integration surface and the Asynchronous Event Port as an internal implementation detail - something that moves data between your own services, not something you safely expose to the outside world.

## The birthday party

My daughter recently turned one. If you've ever organised a first birthday party, you will come to realise it's somehow more stressful than organising a wedding. Organising a birthday party is a difficult task requiring interactions with a lot of different systems. We need to send invitations, book a venue, collect RSVPs, order a cake, arrange food, pay invoices, monitor the weather, and notify guests of any changes.

One of the very first things I had to do was invite all her friends. So, how do we invite all her friends? As we've just seen, we have two approaches:

**- Approach 1 - Synchronous:** We can call every friend individually. You pick up the phone, you wait for them to answer, you invite them to the party and you wait for their RSVP. It's great for getting an immediate, confirmed answer. But everyone has to be available at the same moment you're calling, and if you have as many friends as my daughter does, you'll be on the phone for days.

**- Option 2 - Asynchronous:** We can also post a message in a group chat. We announce the date, the venue and the invitation **once** - and let people react in *their own* time. Someone may see it immediately and RSVP within minutes. Someone else may come back three days later. The information is there when they're ready to consume it.

So, what option do we choose? Of course, it depends.

## Reactive AI Agents demand events

We expect the systems we use every day to be reactive. If I book a venue, I need to receive a confirmation email immediately. If I pay an invoice, I need to see that transaction appear in my bank account immediately. These experiences are all event-driven.

This expectation is increasing, especially as our industry is shifting from deterministic applications to AI agents. Agents can't sit there polling for updates every minute or wake up at 1am to run a batch job and go back to sleep. Just as humans are constantly listening and reacting to our environment, Agents will need to do the same.

### Brain, ears and hands

A reactive AI agent has three main parts:

- **The language model as the brain.** It reasons, plans, and decides what to do next.
- **Events as the ears.** They let the agent hear what's happening in the world without having to constantly ask.
- **Tools as the hands.** They let the agent reach out and take action - booking a venue, updating a database, sending a notification.

Most of the AI conversation right now is about the brain (which LLM?, which model?, how do I write my prompt?) and the hands (which MCP servers?, which tools?). The ears don't get enough attention.

Naturally, I could build an AI agent to help organise this birthday party. The AI agent needs to handle a lot of things and we can give it access to a large range of MCP tools to take care of all  those actions. But here's where things get interesting.

> Some of the things our AI agent needs to do aren't always things it can _ask_ for. They just _happen_:

For example:

- Send a confirmation email when a guest RSVPs via a form response
- Monitor the weather when the weekend weather forecast shifts dramatically
- Ensure deliveries are on-track when a supplier sends a delivery update

These aren't things you can always request with an MCP tool call. AI agents are placing a high demand on the need for event streams and we are starting to see the following pattern emerging:

> **Events trigger AI Agents, MCP executes**.

## Event Gateways are the natural evolution

The truth is just as everyone wants access to your REST APIs, **everyone wants access to your event data.** They want a stream - a live, feed of what's happening in your system as it's happening. For example:

- **Partners and third-party developers** who want to integrate with your platform in real time, without running polling loops that overload your infrastructure.
- **Frontend applications** that need to reflect live state - inventory counts, order status, live pricing.
- **External analytics and data teams** who want event feeds directly into their analytical platforms.
- **AI agents** that need to hear what's happening in your environment so they can reason and respond.

Therefore, we are starting to see the natural evolution:

> Just as you would never expose a REST API without a gateway in front of it, you would never expose a Kafka topic on the internet without a gateway.

### The gateway pattern, applied to events

In the Synchronous Request-Response Port, we have already solved the question on how to securely expose a request-response service: we put a gateway in front of it.

The gateway handles authentication, authorisation, rate limiting, schema validation, observability, routing and more. Your internal service doesn't have to care about any of that. The gateway makes it consumable, securely, at scale.

**The same requirement applies to events.** But when you dig into _why_, it goes deeper than just security. There are four fundamental problems you hit the moment you try to expose event infrastructure, especially Kafka to the outside world.

#### **1. External consumers should never see your internals.**

Kafka's connection model requires clients to know about brokers. In a managed cluster, your bootstrap servers are internal addresses - they reflect your topology, your naming conventions, your cloud region layout. The moment you hand those out externally, you've leaked your infrastructure as a public contract. If you ever want to migrate clusters, rebalance brokers, or switch cloud providers, every external consumer breaks.

> An Event Gateway gives you a single, stable entry point. One address. One connection surface. Your back-end infrastructure can change without your consumers ever knowing.

Exposing your event data externally is not the only reason to proxy Kafka with an Event Gateway. These are also some of the reasons why you may want an Event Gateway for both external and internal consumers:

#### **2. You have more than one Kafka cluster.**

Most mature organisations running Kafka at scale don't have just one cluster - they have several. A cluster per environment. A cluster per business domain. Clusters from acquisitions that haven't been consolidated. Managed Kafka from three different vendors because different teams made different choices at different times. A DR cluster.

From the outside, none of that should be visible or relevant. A partner consuming your order events shouldn't need to know or care that orders live on a different cluster than your inventory events. Without a gateway, you're forcing external consumers to manage multiple connections, multiple sets of credentials, multiple bootstrap configurations - and you're exposing your internal domain boundaries as a side effect. Similarly, if you need a seamless failover between a production cluster and a DR cluster without restarting your clients - A single governance plane in front of all of your clusters solves this. Applied once, uniformly, regardless of what's behind it.

#### **3. You need Centralised Governance**

Kafka brokers are intentionally designed to be thin layers. Therefore, you won't find many governance capabilities built into the brokers themselves. These are usually better distributed to either the clients, or in a centralised enforcement point like the Event Gateway. This includes policies such as; Lightweight Header and Payload Transformation, Schema Validation, Caching, Encryption, Decryption and Filtering.

#### **4. Kafka is one protocol. Your consumers speak many.**

Kafka is a brilliant protocol for high-throughput, durable, ordered event storage. But it's not the only protocol that your consumers will need. A B2B partner may demand the Kafka protocol, but a mobile app that needs to display a live order status update wants a WebSocket connection.

>An Event Gateway decouples the storage protocol from the delivery protocol.

Kafka stores and moves your events internally. The gateway translates - serving the same stream over native Kafka to consumers who can handle it, over WebSocket to browser clients, over SSE to simpler HTTP-based integrations. Consumers get the stream in the format that works for them.

It's worth noting, if a consumer _can_ speak Kafka, it _should_. The semantics come for free and the operational surface stays small. WebSocket and SSE are the alternative for the things that genuinely can't - browsers, constrained devices, constrained SaaS applications.

## The bottom line

When we look at a system as a whole we can see that events are already the connective tissue that choreographs your internal systems. They're increasing going to be the thing that triggers your AI Agents too.

For the last decade, we've focused on making REST APIs available inside and outside our organisations in a safe way. The natural evolution is to extend the Gateway pattern to unlock those events that are locked inside your organisation because applications don't just need REST APIs, AI Agents don't just need Tools - they need ears.

Ask yourself an honest question. Are you polling an endpoint on a timer, waiting to hear if something changed just because you couldn't securely expose your Kafka topic? If the answer is yes, you should probably switch that to a stream managed by an Event Gateway. Polling is a tax you pay on every interval, whether or not anything happened.

Your REST APIs have a gateway. Your event streams need one too. These are not two separate platforms for two separate problems, but one unified connectivity layer that understands both interaction styles.

---

_Warren Vella is a Staff Solutions Engineer at Kong, based in Melbourne, Australia. He joined from Confluent, where he spent four years helping teams design and run Apache Kafka and event-driven architectures at scale. This post is adapted from a talk delivered at the Agentic Would Tour, Auckland, April 2026._
