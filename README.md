# Contribution [1]: [Feature][Connector] Implement Splunk Source and Sink Connectors for Apache SeaTunnel

**Contribution Number:** [1]
**Student:** Raphael Adewale
**Issue:** https://github.com/apache/seatunnel/issues/10753 ("[Umbrella][Feature][Connector] Support New Connectors - Claim & Tracking")
**Status:** [Phase III]

---

## Why I Chose This Issue

I chose the umbrella issue "Support New Connectors - Claim & Tracking" in Apache SeaTunnel because it aligns with my interest in data engineering and my goal to build hands-on experience with data integration platforms. SeaTunnel is a high-performance, distributed data integration tool under the Apache Software Foundation, and this issue explicitly invites community members to claim and implement new source/sink connectors, making it a well-structured entry point for a first contribution.

Within the umbrella issue, I claimed the **Splunk connector (both Source and Sink)**. I'm interested in this because:

1. The scope is clearly defined by the tracker. Splunk is listed as "Both" with status "Todo" and no existing contributor, PR, or partial coverage, so my contribution fills a completely open gap rather than duplicating existing work.
2. The maintainers actively guide first-time contributors. The issue thread includes explicit claiming rules (comment to claim, state whether you are implementing source, sink, or both, and submit a PR against `dev` within four weeks), links to the contribution setup guide and code submission guide, and a maintainer (David Zollo) offering direct mentorship for first-time connector contributors.
3. Implementing both sides teaches me the full lifecycle of a data integration engine: on the sink side, batched writes and delivery semantics via Splunk's HTTP Event Collector (HEC); on the source side, reading search results through Splunk's REST API, pagination, and schema mapping into SeaTunnel's row model.
4. Splunk is a widely used log analytics platform, so this connector has real demand, and SeaTunnel's plugin architecture gives me existing HTTP-based connectors to study as templates.

Per maintainer guidance in the thread, a runnable and reviewable first increment is the smoothest path, so I plan to deliver the work in focused slices (sink first, then source) rather than one large PR.

---

## Understanding the Issue

### Problem Description

Apache SeaTunnel's connector ecosystem is missing a number of connectors that the community needs (Azure Event Hub, Google Pub/Sub, Zendesk, Shopify, Splunk, MQTT, and others). Issue #10753 is a tracking/claim board coordinating who implements what. Specifically for my claim: SeaTunnel has no Splunk connector at all. Users cannot write pipeline data into Splunk for indexing, and they cannot pull indexed events out of Splunk into a SeaTunnel pipeline.

### Expected Behavior

Users should be able to configure Splunk in a SeaTunnel job config in either direction:

- **Sink:** specify a Splunk HEC endpoint, token, and options (index, sourcetype, batching), and SeaTunnel writes pipeline records into Splunk as events.
- **Source:** specify a Splunk REST endpoint, credentials, and a search/query (with time range), and SeaTunnel reads matching events and passes them downstream to any supported sink, consistent with how other API-based source connectors behave.

### Current Behavior

No Splunk connector exists in the `dev` branch on either side. The tracker marks Splunk as "Todo" for both source and sink. Any pipeline attempting to use Splunk has no supported plugin.

### Affected Components

- `seatunnel-connectors-v2/`: a new `connector-splunk` module containing both source and sink implementations
- Connector plugin registration/discovery (plugin mapping config)
- Documentation under `docs/en/connector-v2/source/` and `docs/en/connector-v2/sink/` (new Splunk pages with example configs)
- Tests: unit tests for the sink writer and source reader/split enumerator, and e2e tests under `seatunnel-e2e/` (Splunk runs well in a Docker testcontainer)

---

## Reproduction Process

*(This is a feature request, not a bug, so "reproduction" here means verifying the gap and standing up a working dev environment.)*

### Environment Setup

- Followed the official [Contribution Setup Guide](https://seatunnel.apache.org/docs/developer/setup): JDK 8/11, Maven, forked `apache/seatunnel`, checked out the `dev` branch, and built the project locally.
- Ran a local Splunk instance via the official Docker image for development and testing.
- [Notes on challenges you faced, e.g., Maven build time, module structure, and how you solved them]

### Steps to Verify the Gap

1. Search the `dev` branch of `seatunnel-connectors-v2` for Splunk-related modules.
2. Confirm that no Splunk source or sink implementation exists anywhere in the connector tree.
3. Confirm the connector docs list no Splunk pages; there is no supported way to reference Splunk in a SeaTunnel job config.

### Evidence

- **Commit/branch showing verification:** (https://github.com/RaphaelAdewale/apache-seatunnel)
- **Screenshots/logs:** TBD
- **My findings:** TBD
---

## Solution Approach

### Analysis

SeaTunnel connectors implement a common Connector-V2 API. The two sides need different pieces:

- **Sink:** a `SeaTunnelSink` with a `SinkWriter` that serializes `SeaTunnelRow` records into Splunk events and posts them in batches to the HTTP Event Collector (`/services/collector/event`), with token auth, configurable index/sourcetype/source fields, batching/flush settings, and retry handling.
- **Source:** a `SeaTunnelSource` with a `SourceReader` and `SplitEnumerator` that executes a Splunk search through the REST API (e.g., `search/jobs` export), handles pagination and time-windowed splits, and converts result fields into SeaTunnel's row type according to a user-declared schema.

Existing HTTP-based connectors (the generic HTTP source/sink and API connectors like Elasticsearch) provide the patterns to follow for both sides.

### Proposed Solution

Implement a minimal, reviewable Splunk connector in two slices:

1. **Sink first:** HEC-based event writing with batching and clear error handling. This is the simpler, more self-contained side and gives reviewers a small first PR.
2. **Source second:** REST search-based reading in batch mode first (bounded time range), with streaming/incremental reads as a follow-up.

Each slice ships with documentation, an example config, and tests.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** SeaTunnel needs a Splunk connector covering both directions; PRs target `dev` and should each state clearly which side they implement so reviewers can align them with the tracker.

**Match:** Study the generic HTTP source/sink connector for request/auth handling, and the Elasticsearch connector for a comparable log-store source/sink pair (search-based reads, bulk writes).

**Plan:**
1. Comment on issue #10753 to claim Splunk, both source and sink (done; claims must lead to a PR within four weeks per maintainer rules).
2. Create the `connector-splunk` module: shared config/options and HTTP client, then `SplunkSink` + `SplunkSinkWriter` (HEC).
3. Open PR 1 (Sink) with docs, example config, and tests; register the plugin.
4. Implement `SplunkSource`, `SplunkSourceReader`, and split enumerator (REST search export), plus row conversion for a declared schema.
5. Open PR 2 (Source) with docs, example config, and tests.

**Implement:** [Link to your branch/commits as you work]

**Review:** Self-review against SeaTunnel's [Code Submission Guide](https://seatunnel.apache.org/community/submit_guide/submit-code); each PR title/description must clearly state whether it is **Splunk Sink** or **Splunk Source** so reviewers keep it aligned with the tracker.

**Evaluate:** Run both directions against a local Splunk container: verify sink events appear in the target index with correct fields, and verify the source reads a known dataset completely into a Console/Assert sink. All CI checks pass.

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: Config validation: required options (HEC token/URL for sink; base URL, credentials, and search for source) are enforced, and invalid configs fail with clear errors
- [ ] Test case 2: Sink serialization: `SeaTunnelRow` records are converted into correct HEC event JSON (index, sourcetype, timestamp, field mapping) and batched/flushed as configured
- [ ] Test case 3: Source conversion and pagination: Splunk search results map to `SeaTunnelRow` per the declared schema, and multi-page/large result sets are read completely and exactly once

### Integration Tests

- [ ] E2E job: FakeSource → Splunk Sink writes events that are queryable in a Splunk testcontainer
- [ ] E2E job: Splunk Source → Console/Assert sink reads a seeded dataset correctly on the Zeta engine
- [ ] Error paths: invalid HEC token and unreachable endpoint surface clear connector exceptions rather than silent failures

### Manual Testing

[Run against a local Splunk Docker instance; document indexes written, searches executed, record counts, and results]

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** [List]
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches, e.g., sink-first sequencing, HEC vs. raw TCP input, which REST search endpoint, schema design]

---

## Pull Request

**PR Link (Sink):** [GitHub PR URL when submitted; must target the `dev` branch]
**PR Link (Source):** [GitHub PR URL when submitted; must target the `dev` branch]

**PR Description:** [Draft; state clearly that each PR implements the **Splunk Sink** or **Splunk Source** connector for umbrella issue #10753]

**Maintainer Feedback:**
- [Date]: Maintainer guidance in the thread: comment with the exact connector name, state clearly whether you are implementing source, sink, or both, and open a fresh PR against `dev` within the claim window; a focused, runnable first increment is the smoothest path.
- [Date]: Maintainer (davidzollo) noted the four-week claim window and offered direct help for first-time connector contributors.

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[e.g., SeaTunnel Connector-V2 API, sink writer and source reader/split enumerator design, Splunk HEC and REST API integration, Maven multi-module builds, testcontainer-based e2e testing]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- Issue #10753 (connector claim & tracking board): https://github.com/apache/seatunnel/issues/10753
- SeaTunnel Contribution Setup Guide: https://seatunnel.apache.org/docs/developer/setup
- SeaTunnel Code Submission Guide: https://seatunnel.apache.org/community/submit_guide/submit-code
- Splunk HTTP Event Collector documentation: https://docs.splunk.com/Documentation/Splunk/latest/Data/UsetheHTTPEventCollector
- Splunk REST API reference (search endpoints): https://docs.splunk.com/Documentation/Splunk/latest/RESTREF/RESTsearch
- Related connector work for reference: HubSpot Source (#10358 / PR #10447), BigQuery Sink (#10355 / PR #10485)
