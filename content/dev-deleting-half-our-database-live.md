---
title: "How we deleted 784,000 rows from a live database without dropping a request"
slug: dev-deleting-half-our-database-live
date: 2026-06-22
author: "RezScore"
tags: [dev, infrastructure, postgres]
section: dev
description: "Our homepage had gotten slow because an internal table had quietly bloated to nearly a million rows. Half of it was dead weight. Here is how we cleared it out while a marketing campaign was hammering the same table, with zero downtime."
draft: false
---

Our homepage had gotten slow. Not broken, just sluggish in a way that is worse than
broken because nobody files a bug for "felt a bit heavy today." We traced it to a
single internal Postgres table that records every visitor's assignment in our A/B
tests. It had quietly grown to nearly a million rows, and every page view had to write
into it and keep its indexes up to date. The writes were the slow part.

When we looked closely, roughly half the table was dead weight: rows belonging to
experiments that had long since concluded. Nothing reads those rows anymore. Our
dashboards read pre-aggregated counters, not the raw per-visitor records. So the fix
was simple to describe and nerve-wracking to execute: delete about 784,000 rows.

The catch was timing. We had a marketing email campaign running, and it was writing
to the exact same table around the clock, roughly 1,200 new rows every hour with no
overnight lull. There was no quiet window to wait for. We had to delete half the table
while it was actively being written to, and the homepage could not so much as hiccup.

Here is how we did it.

## Archive before you delete

A previous cleanup had deleted rows without keeping a copy. Nothing broke, but it was
the kind of thing you only get away with once. So the first rule this time was:
archive every row to a file, verify the file, and only then delete.

Each experiment's rows were dumped to a compressed file, and we checked that the line
count in the file exactly matched the row count in the database before issuing a single
delete. If the counts disagreed, the whole thing aborted before touching anything. We
also made the delete command refuse to run at all unless an archive location was
provided. Fail closed. The cost of all this insurance turned out to be about 40 MB of
compressed files, which is nothing.

## Delete in small batches and yield to traffic

A single `DELETE` of 400,000 rows takes a long lock and would have stalled every
homepage write behind it. Instead we deleted in batches of 1,000, with a short pause
between batches:

```python
while True:
    ids = list(queryset.values_list('pk', flat=True)[:1000])
    if not ids:
        break
    Model.objects.filter(pk__in=ids).delete()
    time.sleep(0.3)
```

That pause is the whole trick. It hands the database back to live traffic between
every batch, so the campaign's writes and real visitors' page loads slip through the
gaps instead of queueing behind us. The job takes longer in wall-clock time. Nobody
waiting on the homepage ever notices.

We also snapshotted a timestamp when each archive started and only deleted rows older
than that instant. Any row the campaign wrote while we worked was simply left for next
time, never deleted without first being archived.

## Watch the locks, do not guess

The thing that turns "should be fine" into "is fine" is measurement. While the delete
ran, we watched Postgres directly for anything stuck waiting on a lock:

```sql
SELECT count(*) FROM pg_stat_activity WHERE wait_event_type = 'Lock';
```

It read zero the entire run. That single number was the difference between hoping we
were being gentle and knowing it. When it finished, the table was down from about
888,000 rows to 470,000, and the homepage write path was fast again.

## The real lesson was not about deleting

Here is the part that surprised us. The table did not bloat because of traffic volume.
It bloated because experiments that had finished months ago were never marked as
archived. Our cleanup tooling only looked at archived experiments, so the dead ones
that still claimed to be "active" were invisible to it, piling up forever.

The durable fix was not the big delete. It was a small job that finds experiments with
no recent activity and archives them automatically, plus a weekly scheduled cleanup so
this never accumulates again. A one-time delete clears the symptom. Automating the
thing nobody remembers to do clears the cause.

If your tables only ever grow, it is worth asking whether you have a volume problem or
a flag nobody flips.
