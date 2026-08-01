---
name: Pull Pepperdata job tuning recommendations
description: Query recent Spark/MapReduce/Tez/Kubernetes apps and return only those with tuning recommendations, paginating through large result sets.
api: openapi/pepperdata-rest-api-openapi.yml
operations: [getJobDetails]
---

# Pull Pepperdata job tuning recommendations

Use this to surface apps that Pepperdata flags with cost/performance tuning
recommendations.

## Auth
Header `Authorization: PDAPI <API-key-id>:<API-key-token>`; base URL
`https://dashboard.pepperdata.com/{realm}/api`.

## Steps
1. **getJobDetails** — `GET /jobdetails` with query arguments:
   - Time window: `s` (start; absolute `2020/07/20-05:54`, relative `1h`, or
     epoch-ms), `e` (end; defaults to now), `tzo` (tz offset hours).
   - Scope: `apptype` (`spark`|`mapreduce`|`tez`), `namespace` (Kubernetes),
     `finalstatus`, `appstate`, or `j` (comma-separated job IDs).
   - Recommendations: `recommendationsonly=1`, or `recommendationsgte=moderate`,
     or `recommendationsmatching=critical,severe`.
   - Detail: `showalldetails=1` for configuration properties, recommendations,
     and counters. For Spark internals add `fetchsparkdetails=1`.
   - Volume: `limit` (max 10,000).
2. **Paginate** — when the result set exceeds the return size, re-call
   `getJobDetails` with `filterjobidsafter=<last job ID>` to fetch the next page.

## Rules
- All `/jobdetails` access is GET-only.
- `fetchsparkdetails=1` requires `apptype=spark` and is capped (100 jobs, 1000
  stages, 24 hours).
- Sensitive strings may be encrypted (start `-..`, end `._`) on installations
  with the extra encryption layer — decrypt client-side.
