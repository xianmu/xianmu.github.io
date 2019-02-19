---
title: Optimistic Concurrency Control
tags: database
---

In a previous post, we talked about [timestamp-based concurrency control](2018-09-21-timestamp-based-concurrency-control.html).

Compared to timestamp-based concurrency control, OCC(Optimistic Concurrency Control) is more optimistic as timestamp-based concurrency control validates whether there is a conflict on executing read/write, while OCC validates it on commit. 

More specifically, within OCC, on executing read/write, related objects are firstly copied to the transaction's private space and all write is performed on local copy. On commit, OCC validates whether some concurrent transaction has already been committed, if yes, the transaction shall be abort and restarted, otherwise, it is safe to commit and local copies will be written back to the database.
