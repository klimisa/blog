﻿---
layout: post
title: Effective Logging
---

### Good and consistent information

A logging entry must have all the information that you need in order to understand the event.

Some examples,

- On exception we should include the page or the route that caused it.
- Parameters that affect the behavior should be included.
- The user that was logged in.
- Machine name etc.

Most importantly it should be able to give answers to following questions,

- What where you doing?
- How did you get there?
- What did you type on the form?

### Keep application clean

 Avoid try-catch everywhere.
 
 To create default entries that we are interested in,
 
 - Use global handlers
 - Filters
 - Attributes
 
 Logging should not affect the performance or the behavior of the application
 
 ### Easily consumable
 
 Logging entries should be easily consumable by a variety of audiences.
 - Developers
 - Testers
 - Business Analysts
 - Product managers
 
 To be achieved a web interface is needed where you will review and aggregate the entries.
 
 ### Improve fixing problems
 
 Understand and fix the problem simply by looking at the log entry and the code.
 
 ### Enable deeper understanding
 
 Good logging helps you surface anomalies and features performance.
 
 ### Prioritisation
 
 Good aggregation help you with prioritisation.
 
 - Which error should I fix first
 - Which should be the next performance improvement.
 
 Base on your aggregation