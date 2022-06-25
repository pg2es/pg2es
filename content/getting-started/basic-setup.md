---
title: "Basic Setup"
---

We'll start simple, with one table about blog posts that we need to be indexed as is.

0. (optional) for sandbox, do cleanup:
    ```sql
    DROP PUBLICATION IF EXISTS "search";
    ```
    ```bash
    curl -XDELETE 'http://127.0.0.1:9200/postgres'
    ```

1. create table(s):
    ```sql
    CREATE TABLE public.posts
    (
        id bigserial NOT NULL,
        title text NOT NULL DEFAULT '',
        content text NOT NULL DEFAULT '',
        PRIMARY KEY (id)
    );
    ```
2. configure table: _(wouldn't be required in future)_
    ```sql
    COMMENT ON TABLE public.posts IS 'index:",all"'; -- index all fields by default
    ```

4. expose the table in a [publication](https://www.postgresql.org/docs/14/sql-createpublication.html)
    ```sql
    CREATE PUBLICATION "search" FOR TABLE public.posts;
    ```
5. add some data
    ```sql
    INSERT INTO public.posts( title, content) VALUES ( 'Post 1', 'Super content here');
    INSERT INTO public.posts( title, content) VALUES ( 'Post 2', 'Boring news article');
    ```

6. restart search-replica


7. add more content
    ```sql
    INSERT INTO public.posts( title, content) VALUES ( 'This is streaming replication', 'how long did it take to arrive in Elastic');
    INSERT INTO public.posts( title, content) VALUES ( 'Im volcano', 'My name is Eyjafjallajökull');
    UPDATE public.posts SET content='Updated interesting article' WHERE title='Post 2';
    ```
