---
title: "Consistency"
description: >
  How to keep data in Elasticsearch consistent with primary PostgreSQL database? 
  Use logical decoding.
---

#### Postgres: Write Ahead Log
By design, data in Postgres in always consistent. All the transactions are applied in sequentual matter, into single transaction log. To be more precise Write Ahead Log. No matter how many times you replay WAL file, result would be the same.

#### Postgres: Replication Slot
Collection of offsets, that preserves Postgres from deleting WAL files. Allows to replay changes from last commited position

#### Changes are applied sequantually
Documents in batch query garanteed to be applied in order. Batch query are executed sequentualy, one after another.
_(parallel execution for different documents is still posible and will be implemented in next releases)_

#### Elasticsearch/Opensearch TransLog
After receiving `ACK` response, data is saved into Translog (same as WAL), so it's safe to assume it's replicated.



#### Done!
And there is no need in two-phase locking or any external synchronization point.

