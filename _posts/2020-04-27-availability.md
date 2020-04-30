---
layout: post
title: Availability
---

# Availability

A good availability metric should be:

- Meaningful
- Proportional
- Actionable

`Meaningful` captures the users' experience.
`Proportional` captures a change in the metric that should be proportional to the user-perceived change.
`Actionable` captures insights into why the availability for a period was so low.

### Quantify Availability

The two most common approaches to quantify availability are _Success ratio_ and _Incident ratio_.

#### Success ratio (%)

The fraction of the number of successful requests to total requests _over a period of time_

The period of time is not important.

#### Incident ratio (%)

The ratio of `up minutes` to `down minutes`

"up minutes" are based on the duration of _known incidents_

### Availability Metrics

#### Time-based

The time between failures is uptime and the time to recover from failure is downtime.

availability = uptime / (uptime + downtime)

Requirements:

- Manual labeling of uptime and downtime
- Use of a given threshold

#### Count-based

It uses success ratio, which is the number of successful requests to total requests.
It perceives what the user perceives over time.

Characteristics:

- Easy to implement.
- Prone to bias: by a highly active user

#### Synthetic Probes

Synthetic monitoring means that a script is used to create an activity that is monitored,
the activity can be anything from a simple ping to determine if a server is up, to an emulated user transaction which uses a real browser.
Probes the system automatically at regular time intervals.

It mitigates some of the problems of success ratio when emulating user transactions.

#### User-Uptime

There are two ways to achieve:

1. Synthetic probes for each _user_
2. User requests as probes

Both formulas can be used in the Incident ratio and Success ratio.

## References

Paper:

Tamas Hauer, Philipp Hoffmann, John Lunney, Dan Ardelean and Amer Diwan _Google_ [Meaningful Availability](https://www.usenix.org/conference/nsdi20/presentation/hauer)

Video:

<iframe width="560" height="315" src="https://www.youtube.com/embed/7TY8RaolprI" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
