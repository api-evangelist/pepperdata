---
name: Query Pepperdata time-series metrics
description: Retrieve a Pepperdata metric over a time window with series breakdowns and filters, choosing sampling to control cost and volume.
api: openapi/pepperdata-rest-api-openapi.yml
operations: [getMetrics]
---

# Query Pepperdata time-series metrics

Use this to pull a metric's time series for a cluster, broken down or filtered
by dimension.

## Auth
Header `Authorization: PDAPI <API-key-id>:<API-key-token>`; base URL
`https://dashboard.pepperdata.com/{realm}/api`.

## Steps
1. **getMetrics** — `GET /m` with query arguments:
   - Required: `s` and `e` (`YYYY/MM/DD-HH:mm`), `m` (metric name from the
     dashboard's Metric Definitions), and either `sample` (ms between points,
     minimum 5000) **or** `targetpoints` (target point count).
   - Optional: `tzo` (tz offset), `ms` (max series, default 20),
     `format` (`json` default, or `csv`), `downsampler=percentile`,
     `omitpoints=all`, `omitaggregates=1`, `dataonly=1`.
2. **Break down / filter** — add dimension keys: `h` host, `u` user, `q` queue,
   `c` control domain, `j` job sequence id, `tt` task type. Use `key=*` for a
   full breakdown, `key=literal` to filter, `key=/regex/` to regex-match; combine
   multiple keys (e.g. `&h=my-host&j=*`).

## Rules
- A small `sample` over a long window is expensive — for multi-hour queries use
  `sample=10000` (10s) or set `targetpoints`.
- When matched series exceed `ms`, the top N are returned plus aggregates
  (`sum_of_the_others`, `max_of_the_others`, `average_of_all_matched_series`)
  unless `omitaggregates=1`.
