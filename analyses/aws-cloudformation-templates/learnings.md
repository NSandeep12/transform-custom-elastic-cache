# Learnings: ElastiCache Redis OSS end-of-support analysis (2026-11-17)

## What worked
- A case-insensitive repository search for `elasticache|redis` found every in-scope file: `ElastiCache/Elasticache-snapshot.yaml`, `ElastiCache/Elasticache-snapshot.json` and `ElastiCache/elastic-code.js`. No Terraform, CDK, docker-compose, Helm or Redis client manifests reference Redis.
- Engine and version are literal values (`Engine: redis`, `EngineVersion: 2.8.24`). No parameter or mapping indirection had to be resolved.
- The YAML and JSON templates describe the same stack in two formats. Each was reported as its own cache definition so a remediation edits both.

## What failed and why
- No live data, AWS Health or Trusted Advisor data was read because `live_access=off`. Status comes from the AWS documentation (lifecycle reference) and the run date. Drift was not checked.
- `.github/workflows/stale.yml` is tracked in git but missing from the working tree, so CI deployment practices couldn't be checked. No deploy pipeline was found, so no CI risk point was added.

## Values not resolved
- Region: the template uses `AWS::Region` with a mapping for us-east-1, us-west-1 and us-west-2. The deployed Region is unknown.
- Whether a direct upgrade from Redis OSS 2.8.24 to Valkey is allowed. The AWS docs don't say. Confirm with `describe-cache-engine-versions` or AWS Support.
- The newest Valkey versions in the Region (9.1 and 8.2 are taken from the docs) weren't confirmed with `DescribeCacheEngineVersions`.
- Whether node type `cache.m3.medium` (previous generation, fixed by `AllowedValues`) supports the target engine. This needs verification.
- Live replication group ID and account: not read.
