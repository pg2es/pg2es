---
title: "Why is it fast"
---

#### Low level binary API
Search replica uses jackc's [pgconn](https://github.com/jackc/pgconn) & [pgtype](https://github.com/jackc/pgtype) libraries, which can decode postgres data quite efficiently. Most types in pgtype, implement JSON marshalling, making this step even more efficient.

Uses binary format for initial reindexing and streaming (PG 14+)


#### Streaming
Both `COPY TO` and Streamed messaged are processed one by one, reusing memory. Keeping memory requirements low.


#### Bulk queries
All updates are combined into bulk buffer, which is flushed by predefined intervals.  
No updates - no requests. 

In case, if SearchReplica was in idle state, and message arrives, it would be forwarded immediately.


#### Simple flow
* No additional DB requests
* No dependencies, queues, locks, network delay.

Just fetch => decode => encode => push the data.

#### Painless Scripting
Complex features, like inlining, use scripted update. It's relatively heavy operation, but way cheaper than alternatives and does not distrupt streaming.
