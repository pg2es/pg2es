---
title: "How It Works"
description: >
  Oversimplified SearchReplica COPY existing data and STREAM ongoing changes to Elasticsearch/OpenSearch in a background.
weight: 1

---

Like many similar projects, SearchReplica abuses [Logical Replication protocol](https://www.postgresql.org/docs/14/protocol-logical-replication.html), decoding all the transactions commited to [Write-Ahead-Log](https://vitalii.kozlovskyi.dev/posts/wtf-is-wal/) and forwarding them into [Elasticsearch](https://www.elastic.co/elasticsearch/)/[OpenSearch](https://opensearch.org/) in a form a JSON documents

Following is the simplified workflow.

#### Initial discovery
1. Fetch and parse all comments related to puslished tables using monstrous query.
2. Check if key fields are awailable in WAL (see: Upsert Mode)
3. Discover User-Defined types, like enums or composite types.

#### Reindexing (cold start)
1. (re)Create Replication Slot if needed.
2. Fetch all existing data via `COPY TO` command. And process them 1 by 1.
3. Push to Elasticsearch in batches

#### Streaming Replication
After initial cold-start reindexing
1. Subscribe 
2. Get Messages in a loop and process them 1 by 1.
3. Periodically push to Elasticsearch in batches.
4. After successfull push, commit LSN of last pushed document back to postgres

This approach provides you with near-to-realtime consistent data without additioan efforts.
