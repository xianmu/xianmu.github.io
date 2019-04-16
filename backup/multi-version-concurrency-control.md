---
title: Multiversion Concurrency Control
tags: database
---

In previous posts, we talked about pessimistic/optimistic concurrency control methods, including [2PL](https://en.wikipedia.org/wiki/Two-phase_locking), [T/O](2018-09-21-timestamp-based-concurrency-control.html), [OCC](2019-01-20-optimistic-concurrency-control.html).

However, MVCC focues on another problem, readers will block other writers or vice versa. Take OCC as an example, on commit, the transaction will write the modified set back, which will block all other transactions prefer to read the same record.

Targeting this problem, MVCC maintains multiple versions of each record, each transaction will read from a consistent version of database and write back a new one, so that parallel access will never conflict with or block each other.

MVCC is orthogonal to pessimistic/optimistic concurrency control methods, we do actually have MV-2PL/MV-TO/MV-OCC variants.
