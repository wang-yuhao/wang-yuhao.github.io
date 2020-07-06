---
layout: blog
title: 12-Mongodb-Operations
date: 2020-06-04 03:07:47
tags:
---

#### 1. update
db.collection.update(criteria, objNew, upset, multi)
    criteria: The search condition of update,
    objNew: The object or operator of update, like `$set, $inc, $setOnInsert`
    upsert: If the criteria doesn't exist, if insert objNew, true is insert, default[false], not insert.
    multi: default[false], just update the first record of the founded result, if this argument is true, update all founded records.

<!--more-->

Example:
```
db.test.update(
    {'srcaddr':'10.0.0.4'},
    {
        $set:{'last': '0256'},
        $inc:{'packets': 17}, 
        $setOnInsert:{'srcaddr':'10.0.0.4','first': '0221'}
    },
    {upsert: true});
```

Error: "errmsg" : "Updating the path 'last' would create a conflict at 'last'"
```
db.test.update(
    {'srcaddr':'10.0.0.2'},
    {
        $set:{'last': 0226}, 
        $setOnInsert:{'srcaddr':'10.0.0.2','first': 221,'last':26, 'packets': 17}
    },
    {upsert: true});

```

Reason: There is already $set 'last', second time $setOnInsert 'last' make a conflict.
#### Solution: Remove all repeat field in objNew