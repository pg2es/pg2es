---
title: "Search Replica"
description: >
  Search replica would sync your postgres data to Elasticsearch/OpenSearch in a background.
  Your Postgres database is sourse of truths and Search is read-only near-to-realtime replica.

---

Search Replica is a small adapter, which replicates Postgres data into elasticsearch index.
Your Postgres database is sourse of truths and Elasticsearch read-only near-to-realtime replica.

As many similar projects, we abuse logical decoding for our needs.

#### Small & Lightwaigh
* No external dependencies
* [Container](https://hub.docker.com/r/pg2es/search-replica/tags) size is less than 5M
* Ludicrous resource requirements.

#### Works out of the box
* No complex schema or additional yaml file required.
* If needed, configurable via COMMENTS on table or column.
* Reindexes existing data

#### Cloud Native
tbd;


#### Alternatives:
- [ZomboDB](https://www.zombodb.com/)
- [PGSync](https://pgsync.com/)
