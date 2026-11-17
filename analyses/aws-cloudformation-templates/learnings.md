# Learnings: ElastiCache Redis OSS end-of-support analysis

Run date: 2026-11-17 · Inputs: live_access=off, all others default.

## What worked
- Repository scan found one stack defined twice: `ElastiCache/Elasticache-snapshot.yaml` and its JSON equivalent `ElastiCache/Elasticache-snapshot.json`. Each defines `RedisReplicationGroup` (redis 2.8.24), `RedisParameterGroup` (family redis2.8) and `RedisSubnetGroup`.
- Engine and version are literal values (YAML lines 146-147, JSON lines 217-218), so nothing needed resolving through Parameters or Mappings.
- Status comes from the AWS docs fallback: Redis OSS 2.x is past end of life (2023-01-13) and is not in the Extended Support schedule, so no cost was estimated.

## What failed or was skipped, and why
- No AWS calls were made because live_access=off: no STS identity, Health, Trusted Advisor, describe calls or DescribeCacheEngineVersions. As a result, drift was not checked and deployment state is unknown.
- I did not use web access to re-check the lifecycle docs. Lifecycle dates and the 9.1 / 8.2 version strings come from the skill references.

## Values I couldn't resolve
- The live engine version and whether either template is deployed, and in which Region. The IaC mapping covers us-east-1, us-west-1 and us-west-2.
- Whether a direct upgrade from 2.8.24 to Valkey is allowed. The docs don't say; confirm with `describe-cache-engine-versions` or AWS Support.
- Whether `cache.m3.medium`, the only allowed node type, is offered for Valkey 9 / 8 / 7.2 or Redis OSS 7.1.

## Notes for the remediation
- Both templates must be changed together. The JSON file appears to be a rain-generated twin of the YAML (it uses `Rain::Embed`).
- Blocker: `TransitEncryptionEnabled` is unset, but a Valkey replication group requires it. The customer must decide on TLS or pick Redis OSS 7.1.
- `ElastiCache/elastic-code.js` is a control-plane Lambda (modifyReplicationGroup). It is not a Redis client, so no application signals were found.
