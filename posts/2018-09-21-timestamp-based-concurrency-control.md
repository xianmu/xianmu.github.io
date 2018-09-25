---
title: timestamp-based concurrency control
tags: database
---

[Timestamp-based concurrency control](https://en.wikipedia.org/wiki/Timestamp-based_concurrency_control) is a non-block (optimistic) concurrency control method for executing concurrent transactions.

Timestamp could be real (read from the wall clock) or logical (using a counter) provided that it is monotonically increasing and will not produce duplicate timestamps.

Each transaction $T_i$ acquires its timestamp $TS_i$ at the beginning. The transaction also maintains the following data, $DEP(T_i)$-the set of dependent transactions, $OLD(T_i)$-the set of original values of updated data. While on the other side, each record $R_j$ holds two extended fields, $RTS(R_j)$-timestamp of latest read, $WTS(R_j)$-timestamp of latest update.

Then for each read of $T_i$ (suppose the reading record is $R_j$):

1. If $WTS(R_j) \gt TS(T_i)$, then abort $T_i$;

2. Otherwise, add the latest transaction that updated $R_j$ into $DEP(T_i)$, and set $RTS(R_j)=max{RTS(R_j), TS(T_i)}$.

For each update of $T_i$,

1. If $RTS(R_j) \gt TS(T_i)$, then abort;

2. If $WTS(R_j) \gt TS(T_i)$, then skip (according to [Thomas Write Rule](https://en.wikipedia.org/wiki/Thomas_write_rule), the value will finally be overwritten by the succeeding transaction);

3. Otherwise, add the original value $<R_j, WTS(R_j)>$ into $OLD(T_i)$, set $WTS(R_j)=TS(T_i)$, and update $R_j$.

On commit,

1. check if any transaction in $DEP(T_i)$ is in progress, wait for all of them ended

2. if any transaction in $DEP(T_i)$ aborted, then abort

3. otherwise, do commit

On abort,

1. for each original value $(R_j, WTS(R_j))$ in $OLD(T_i)$, if $WTS(R_j)==TS(T_i)$, then restore the original value and $WTS(R_j)$

