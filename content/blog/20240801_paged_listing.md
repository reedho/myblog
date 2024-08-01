+++
title = "Create paged listing from Datalevin query results with Missionary concurrent flow processing"
date = 2024-08-01
[taxonomies]
lang=["clojure"]
lib=["missionary", "datalevin"]
+++

This function demonstrated how we can create paging data out of a collection of
entities, utilizing the [missionary](https://github.com/leonoel/missionary) flow
to achieve safe and performant concurrent processing.

```clojure
(defn paged-listing
  "Return a task completing with paged listing of running `eid->task` for each eid
  in eids concurrently."
  [conn
   eid->task ;; this will be called as `(eid->task conn x)`
   eids {:keys [currentPage
                itemsPerPage]
         :or {currentPage 1
              itemsPerPage PNR_ITEMS_PER_PAGE}}
   ]
  (let [paged-eids (->> eids
                        (drop (* itemsPerPage (dec currentPage)))
                        (take itemsPerPage))
        total-items (count eids)
        [q r] ((juxt quot rem) total-items itemsPerPage)
        total-pages (+ q (if (zero? r) 0 1))
        ]
    (m/sp
      {:currentPage currentPage
       :totalItems total-items
       :totalPage total-pages
       :itemsPerPage itemsPerPage
       :pnrReportSummaries
       (m/? (->> (m/ap (let [x (m/?> 4 (m/seed paged-eids))]
                         (m/? (eid->task conn x))))
                 (m/reduce conj)))})))
```

<br />

The `eids` argument is, in this case, could be a values returned from
[datalevin](https://github.com/juji-io/datalevin) query shown below:

```clojure
(->> (d/q '[:find [?e ...]
            :where
            [?e :locatorCode]]
          (d/db conn))
     (sort #(compare %2 %2))
     (take 10))
```

<br />

Argument `eid->task` is a function returning missionary task.

```clojure
(defn pull-detail-task
  [conn eid]
  (m/via m/blk
         (-> (d/pull (d/db conn) '[*] eid)
             ,,, ;; some data transformation function here
             )))
```

<br />

And, finally, the typical usage could be some thing like this:

```clojure
(-> eids
    (as-> $$ (paged-listing conn pull-detail-task $$)
    )
;; =>
{:totalItems 6,
 :pnrReportSummaries
 [{:locatorCode "9EA5F385" ,,, } {:locatorCode "YCTKQZJ2" ,,, } ,,, ],
 :currentPage 2,
 :totalPage 3,
 :itemsPerPage 2}
```
