+++
title = "One handler and two storages"
date = "2025-01-01"

[taxonomies]
tags = ["ugc", "middle_python"]

+++


> *Disclaimer:*
> This post is a starting point. Each pattern has trade-offs and operational details.
>
> Before choosing one, research [microservices.io](https://microservices.io/)

The UGC module brings up a classic distributed transaction issue:
`can we safely write to two databases from one handler?`

The answer is no.
Writing to multiple databases from a single handler almost always leads to data inconsistencies and hard-to-debug failures.


Instead, use one of these patterns to ensure consistency:
* Transactional Outbox:
Write changes only to the master database and, in the same transaction, record an event in an outbox table. A background worker reads from the outbox-table and updates secondary storages.
```python
def handler(data):
    with transaction():
        save_to_main_db(data)
        save_to_outbox({'event': 'created', 'data': data})
```

* Event Sourcing:
Store every change as an event in an event log. Rebuild the current state of each storage by replaying these events. All storages receive updates from the same event stream, so you avoid direct double writes.
```python
def handler(data):
    push_event({'event': 'created', 'data': data})

def consumer_ugc(event):
    if event['type'] == 'created':
        create_ugc_entity(event['data'])

def consumer_analytics(event):
    if event['type'] == 'created':
        create_analytic_log(event['data'])
```

* Change Data Capture (CDC):
Track changes in the master database using CDC tools (like Debezium). As changes happen, capture them and propagate updates to other storages asynchronously.
```python
def handler(data):
    save_to_main_db(data)

# APP -> DB -> CDC -> DB2
```

![Distributed fail](/middle_dev_insights/ugc/ugc-scetch.gif "Distributed Fail")

### How to act:
Pick a single source of truth (the master). Use an event-based approach—Transactional Outbox, Event Sourcing, or CDC—to propagate changes. All three avoid direct double writes, reducing the risk of data loss. . All writes are recoverable.
