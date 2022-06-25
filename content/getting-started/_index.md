---
title: "Getting Started"
weight: 2

---

> Talk is cheap. Show me the code. (c) Linus.


### Demo

#### SearchReplica In Action

Get sources
```bash
git clone git@github.com:pg2es/search-replica.git
cd search-replica/demo
```

Start sandbox
```bash
docker-compose up -d
```
and wait a few second for elastic initalization.  
**Done.** You have a working sandbox to experiment with.

cleanup everything: _`-v` deletes volumes_
```bash
docker-compose down -v
```


Play with the data in **pgAdmin** at [localhost:8081](http://127.0.0.1:8081/)  
See results in **Search UI** here [locahost:8080](http://127.0.0.1:8080/)  


Raw **PostgreSQL** is exposed at `psql -h 127.0.0.1 -U postgres`.  
Synced **Elasticsearch** is available at `http://127.0.0.1:9200/`. No auth required.  

Debug Logs:
```bash
docker logs -f docker logs -f pg2es-search-replica
```

<!-- {{< button relref="/" size="large" target="_blank" >}} Search UI {{< /button >}} -->

**Have fun!**

> WARN:
> 
> Currently the only way to rediscover config is by restarting it.
> ```
> docker restart pg2es-search-replica
> ```
> Also is a sandbox, each restart will reindex all the data from database
