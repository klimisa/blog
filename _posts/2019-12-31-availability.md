---
layout: post
title: Availability
---

# Availability

A good availability metric should be:

- Meaningful
- Proportional
- Actionable

`Meaningful` means that it should capture what users experience.
`Propotional` means that a change in the metric should be propotianal to the user-perceived the change.
`Actionable` means giving insights why availability for a period was so low.

### Quantify Availability

The two most common approaches to quantify availability are _Success ratio_ and _Incident ratio_.

#### Success ratio (%)

Fraction of the number of successful requests to total requests _ever a period of time_

Period of time is not important.

#### Incident ratio (%)

The ratio of `up minutes` to `down minutes`

"up minutes" are based on the duration of _known incidents_

### Availability Metrics

#### Time-based

The time between failures is uptime and the time to recover from failure is downtime.

availability = uptime / (uptime + downtime)

Requires,
- Manual labeling of uptime and downtime
- Use of threshold

#### Count-based

It uses success ratio - successful requests to total requests.
It perceives what user perceives better than time.

Characteristics,

- Easy to implement.
- Prone to bias: by highly active user

#### Synthetic Probes

Synthetic monitoring means that a script is used to create activity that is monitored,
the activity can be anything from a simple ping to determine if a server is up, to an emulated user transaction which uses a real browser.
Probes the system automatically at regular time interval.

It mitigates some of problems of success ratio when emulates user transactions.

#### User-Uptime

There are two ways to achieve,

1. Synthetic probes for each _user_
2. User requests as probes

Both formulas can be used Incident ratio and Success ratio.

## References

Tamas Hauer, Philipp Hoffmann, John Lunney, Dan Ardelean and Amer Diwan _Google_ [Meaningful Availability](https://www.usenix.org/conference/nsdi20/presentation/hauer)
