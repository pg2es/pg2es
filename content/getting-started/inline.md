---
title: "Inlining"
weight: 3
description: >
  Inlining one table into another. Hacky yet working way of denormalization via Elasticsearch scripted updates.

---

How to denormalize related data, without additional query? Well, we can push normilized data to search and ask it to figure things out.
This is done using scripted update of parent document. Does not require any effort from Postgres, while increasing a load on Elasticsearch.

{{< hint type=important >}}
**N:M relations are not supported** 

And 1:1 do not make any sense in this context. Open issue if you need it.
{{< /hint >}}


### How to
In order to put one table as list of objects into another doc, you need to have `parent key`, which is usualy foreign key.
Imagine we have structire like thise.

So, having two tables `users` and `comments` with `1-N` relationship, like

 
{{< columns >}}
| Users   | `index:",all" inline:"user_comments,comments"`|
| --------| --------------------------------------------
| id (pk) |
| name    |
| email   |
| phone   | `index:"-"`

<--->
 
| Comments | `index:"-"`
| -------- | -------------------------------
| id (pk)  | `inline:"user_comments"`
| user\_id  | `inline:"user_comments,parent"`
| text     | `inline:"user_comments,content"`
| date     |

{{< /columns >}}

here:
- `index:",all"` comment on Users table means index all fields, with `documentType={tablename}`
- `inline:"user_comments,comments"` on Users table would inline `user_comments` objects into `comments` field.
- `index:"-"` on a Users.phone would not index this field, regardless `index:",all"` on a table.
- `index:"-"` on Comments table, would not index this table entierly.
- `inline:"user_comments"` on Comments field id would include this field in `user_comments` inline, preserving field name. And since this column is PK, it would also be used as PK for inline.
- `inline:"user_comments,parent"` on `user_id` would specify PK of parrent document. So we can append comment into User.comments.
- `inline:"user_comments,content"` on `text` field id would include this field in `user_comments` inline under new `content` name.

and this would produce documents like 
```json
"_source": {
    "docType": "users",
    "id": 1631208,
    "name": "Rick Sanchez",
    "email": "rick@sanches.co",
    "comments": [{
        "id": 25285149,
        "content": "Lorem Ipsum ...",
        "user_id": 1631208
    },{
        "id": 25285753,
        "content": "... dolore eu fugiat nulla pariatur...",
        "user_id": 1631208
    }]
}

```
