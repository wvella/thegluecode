---
title: "Beyond Real-Time"
description: "Building a Shared Truth"
date: 2026-02-17T21:52:33+11:00
image:
math:
license:
hidden: false
comments: true
draft: false
params:
  math: true
categories: ["Data Streaming"]
tags: ["kafka", "flink"]
---
The discourse around data streaming has been stuck on a single word for too long: **real-time.**

While it's true that data streaming allows us to build reactive systems, a singular focus on real-time overlooks its broader, more transformative value. After all, "real-time" is a nuanced term, carrying different meanings in different contexts.

This narrow focus often leads to a common question:

> **Do I really need data streaming if my use case is not real-time?**

All things being equal, fast data beats slow data. Early on, when software was merely a tool, slow data was often acceptable for tasks such as online forms or reporting systems. But even by 2019, as Jay Kreps accurately [wrote](https://www.confluent.io/blog/every-company-is-becoming-software/), “every company is becoming software” - software now powers most of our daily interactions. It can no longer be slower than our reality. Practically speaking, when I tap a button in my smart home app (I love [Home Assistant](https://www.home-assistant.io/)), the light should turn on immediately, not in 15 minutes. Companies like Uber transformed entire industries by delivering a real-time experience.

Allow me to illustrate further with a frustrating example. The system I use to book flexible working spaces only allows me to book a desk *24 hours in advance*. This requires me to plan ahead and means I cannot easily book a desk based on my daily movements. More often than not, I end up unable to book a desk. The number of times this has happened to me alone must cost thousands of dollars in missed revenue.

Data streaming is undeniably a foundation for real-time use cases and even if you’re convinced you don’t have any “real-time” requirements, I invite you to widen your perspective. Data streaming extends beyond speed; it's fundamentally about capturing a **shared truth**. Data streaming is about establishing a reliable, consistent, and immutable record of every event that happens in your system and making this truth universally accessible across all applications and data platforms.

## What is Data Streaming?

At its core, data streaming is an architecture for recording *exactly* what is happening as a continuous flow of ordered events. Because these events are captured as they occur, rather than on a schedule, we can build systems that react to them immediately.

To fully appreciate the significance of this shared truth, let's begin by revisiting the three ways we interact with data today.

---

## Three Common Interaction Patterns

* **Batch Processing: The History** - Batch processing stands as a time-tested workhorse, a technique originating in the 1960s when computers could only run one job at a time. Although we still use it today, batch processing inherently looks backward and is typically implemented using an Extract-Transform-Load (ETL) or Extract-Load-Transform (ELT) flow. It provides a view of past events where data is processed in snapshots; it has a defined start and end and is often minutes, hours, or even days old. Batch processing is like reviewing your bank statement at the end of the month. It's helpful for understanding past spending habits, but it won’t tell you if there is fraudulent activity happening right now.

* **Request-Response: The Current State** - Request-response is the integration style that underpins the majority of our daily digital interactions. When you initiate a payment or check your bank balance, you are sending a request and waiting for a response. It's a quick, singular interaction that returns the current state, usually via a SQL query or a synchronous REST API. While you receive an immediate response, request-response expects the responding system to be available. This creates a temporal dependency that worsens as the number of connections between systems increase. Usually, the response only contains the current state. You might see the contents of a shopping cart, but you won’t capture the story of *why* a user added an item and then removed it moments later.

* **Data Streaming: The Story** - Data streaming takes a completely different approach: it **records a story**. Instead of looking backward (batch) or returning only the current state (request-response), data streaming continuously records and processes the entire sequence of events that *make up* the current state. This continuous capture of every action and every change, in the exact order it occurred, is profound. This chain of events represents the closest thing we have to a truly objective, replayable, and auditable record of truth.

---

## The Significance of Truth

In 2026, building systems that react instantly is no longer a "nice-to-have"; it is the expectation.

The technology to build systems that react in real time is already mature. The quiet breakthrough of data streaming is that we can replay everything that has ever happened - every click, every trade, every sensor reading - as many times as we like, from any vantage point we choose. This isn’t just a database query; it’s a time machine.

### The Humble Shopping Cart
If you look at a shopping cart at checkout through a traditional request–response lens, you see one thing: a soft drink. The story of why the customer added and later removed the apples is gone. The system has correctly given you the final state but silently destroyed the narrative that led there.

But if every interaction is captured as an event in a stream, a different story emerges. Now you can ask: *Why* did the customer not purchase the apples? Was it price sensitivity? A promotion? A pattern that emerges every Tuesday night? The cart is no longer a static object; it’s a behavioural timeline.
## The Architecture of Shared Truth

An immutable data stream becomes a forensic instrument in your architecture. You can rewind outages to the exact millisecond they began, reconstruct the chain of causality, and prove what really happened. You can surface faint, slow-moving trends that would never survive the compression of nightly batch jobs. Perhaps most pronounced, you can **retrofit entirely new applications onto data that was generated years before those applications were even imagined.** This is the real promise of an append-only shared truth: by representing your business as data streams, you establish a foundation that allows batch, request-response, and streaming systems to align with the same shared reality.
### Isn’t data streaming similar to other integration platforms?
You might be tempted to compare data streaming to traditional Message Queues, ESB/iPaaS, or batch ETL platforms. However, those platforms are primarily designed to move data ephemerally from Point A to Point B.

In data streaming, storage is embraced as central to the architecture. This persistent storage becomes the memory that holds the shared truth. Fundamentally, a data streaming platform is a storage platform which truly addresses the challenge of quadratic growth in bidirectional point-to-point integrations, represented by the formula:

$$
2N(N-1)
$$

You might also be tempted to build this shared truth in a centralised database. While a  database might seem like an option, it's fundamentally misaligned for a data stream log and you will inevetibably end up re-building the streaming protocol (offsets, partitions, etc). Databases prioritize read performance, while data streams excel at handling massive write throughput - often measured in MBps or GBps. If you squint hard enough, you'll recognise that data streaming shares deep roots with write-ahead logging (WAL), a technique that databases themselves rely on for durability and consistency. See [Turning the database inside-out with Apache Samza](https://www.confluent.io/blog/turning-the-database-inside-out-with-apache-samza)

Finally, when you encounter the word "truth," you might also turn to the common debate of "Sources of Truth (SoT)" vs "Systems of Record (SoR)." However, I deliberately use the term *Shared Truth*. While a shared truth can indeed fall within the SoT category, the emphasis highlights that data streams are intrinsically designed to be shared - not locked within a siloed system.

![Building a Shared Truth](building_a_shared_truth.png)

---

## Realising the Shared Truth

Constructing systems around a shared truth requires a different mindset - a focus on "thinking in events", ensuring data quality at the source, and providing scalable infrastructure. Technologies such as Apache Kafka and Apache Flink have emerged as foundational tools for this journey. Get this right, and the payoff is immense. It moves enterprises beyond real-time processing to a unified view of the entire business.

So, the next time you fall back to "I don't have a real-time use-case", I encourage you to look beyond speed. Yes, data streaming is fast. But, its deeper value lies in its ability to remember exactly what happened. This shared truth becomes the foundation for a decoupled architecture.

**Ask yourself: "What if I could see the complete, exact truth of how my systems arrived at their current state?"**

When you can answer that, you’re not just building for speed - you’re building trust.
