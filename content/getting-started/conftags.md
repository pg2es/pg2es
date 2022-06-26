---
title: "Configuration"
description: >
  Conftags config and customization usigng PostgreSQL COMMENT.
weight: 2

---

To simplify workload, SearchReplica relies on rarelly used sql `COMMENT`s.
Defining them on table or field allows skip, rename fields, inline one table into another document, etc.

PostgreSQL `COMMENT` is a `Text` field which can store values of any length (up to 1GB)

### How to
You define comments with basic ommands
```sql
COMMENT ON TABLE your.tablename IS '...conftag values here...';
COMMENT ON COLUMN  your.tablename.columnname IS '...conftag values here...';
```

### Tag Syntax
Syntax is slightly changed struct tags from Golang, if you familiar.

It's a string of **space separated** tags, where tag starts with **name** followed by semicolon.  
3 spacebars in a row or `#` stops tag parsing leaving end part as human readable comment.
```
tagname:"positional1,,positional3,other,whatever" tag2:"val1,val2"    Some regular human readable comment
```

```sql
COMMENT ON TABLE posts IS 'index:"blogposts,all" inline:"blog_comments,comments"    PLEASE DONT DELETE ROWS';
COMMENT ON COLUMN posts.id IS 'index:"-,pk" # This would use id as _id and remove it from json document';
```


### Possible values
Tag values can be positional, or Flags which follow positional values.

#### Table
- `index`:
  - docType  _(`-` means do not index)_
  - Flags:
    - `all` (deprecated) index all fields in this table
- `inline`:
  - inline name, that will be injected as a field
  - field name
  - _(optional)_ script name for adding
  - _(optional)_ script name for removal
- `join`:
  - field name
  - _(optional)_ type name _(by default docType is used)_

#### Field
- `index`:
  - rename  _(`-` means do not index)_
  - Flags:
    - `pk` this value, prefixed by table name would be used as document \_id.
    - `id` value would be used as document [\_id field](https://www.elastic.co/guide/en/elasticsearch/reference/7.10/mapping-id-field.html) 
    - `routing` value would be used for [routing](https://www.elastic.co/guide/en/elasticsearch/reference/7.10/mapping-routing-field.html)
- `inline`:
  - inline name, where this field should be used
  - rename (within inline) NOT IMPLEMENTED
  - Flags:
    - `pk` inline PK
    - `parent` parent `_id`
    - (optional) `routing` routing value of PARENT document. (in order to find and update it)
- `join`:
  - Flags:
    - `parent` field used as parent value
    - `name` field is used as join type (for polymorphism)



### Examples:
from [demo/schema.sql](https://github.com/pg2es/search-replica/blob/762925/demo/schema.sql#L60-L81)
```sql
COMMENT ON TABLE "main_doc" IS 'index:"main,all" join:"join,immaparent" inline:"inline_name,inlined_field"';
COMMENT ON COLUMN "main_doc".id IS 'index:",routing,id"';
COMMENT ON COLUMN "main_doc".ignore_me IS 'index:"-"';

/* Just inline and do not index as separate document.
We need to specify inline PK, parent document ID and optionally routing.
Each inlined field should be explicitly defined as such. */
COMMENT ON TABLE "inline_doc" IS 'index:"-"';
COMMENT ON COLUMN "inline_doc".parent_id  IS 'inline:"inline_name,_pk,parent,routing"';
COMMENT ON COLUMN "inline_doc".id  IS 'inline:"inline_name,pk"';
COMMENT ON COLUMN "inline_doc".value  IS 'inline:"inline_name"';


/* Be aware, child document should always be in the same shard as parent, thus routing field is used. It can be any shard key, or just a parent ID like shown below */
COMMENT ON TABLE "child_doc" IS 'index:"child,all" join:"join,immachild"';
COMMENT ON COLUMN "child_doc".id IS 'index:",id"';
COMMENT ON COLUMN "child_doc".parent_id IS 'index:",routing" join:"parent"';
COMMENT ON COLUMN "child_doc".ignore_me IS 'index:"-"';
```
