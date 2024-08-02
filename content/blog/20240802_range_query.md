+++
title = "Range Query"
date = 2024-08-02
[taxonomies]
lang=["clojure"]
lib=["datalevin"]
+++

So, at one moment i think what of kind of query the _range query_ is? It turn
out just yet another fancy name and don't over thinking ot it ever again. This
is the principle.

Range query is for when the clause is not an equal (`=`).

Or, in more generic sense: Range query is for whatever that could return more
than one exact result.

SQL:

```sql
select * from users where id>1;
select * from users where created_at between '2024-08-01' and '2024-08-02';
```

<br />

Datalog:

```clojure
(d/q '[:find ?e
       :in $ n
       :where
       [?e :user/id ?x]
       [(> ?x n)]
       ]
     (d/db (:qln.pnr/datalevin-conn qln.sys/*sys*))
     1)

(d/q '[:find ?e
       :in $ dt1 dt2
       :where
       [?e :user/id]
       [?e :user/createdAt ?x]
       [(> ?x dt1)]
       [(< ?x dt2)]
       ]
     (d/db (:qln.pnr/datalevin-conn qln.sys/*sys*))
     (java.util.Date/from (java.time.Instant/parse "2024-08-01T00:00:00Z"))
     (java.util.Date/from (java.time.Instant/parse "2024-08-02T00:00:00Z")))
```
