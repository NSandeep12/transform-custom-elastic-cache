# Learnings: ElastiCache Redis OSS end-of-support analysis (cloudposse-elasticache-redis, 2026-11-17)

## What worked
- The repository is a reusable Terraform module (`main.tf`) with two example roots. Effective versions were resolved through `examples/*/fixtures.us-east-2.tfvars` -> `examples/*/main.tf` -> module `variables.tf` defaults -> `main.tf` locals.
- Three caches were assessed: the `examples/complete` replication group (redis 7.0), the `examples/serverless` cache (redis, major 7) and the module defaults (redis 7.1). All three are SUPPORTED, so no Extended Support exposure applies.
- No blockers. Every cache is an `aws_elasticache_replication_group` or `aws_elasticache_serverless_cache` (not `aws_elasticache_cluster`), in-transit encryption defaults to `true`, and the AWS provider constraint `>= 5.73.0` needs no major bump.

## What failed or was limited, and why
- Live data, AWS Health and Trusted Advisor were not used because the plan context set `live_access=off`. As a result drift, unmanaged caches and Global Datastore membership were not checked.
- The newest Valkey version in us-east-2 (9.1 node-based, major 9 Serverless) comes from the skill's documentation fallback. It was not confirmed with `DescribeCacheEngineVersions`.

## Values that could not be resolved
- The actual deployed replication group and Serverless IDs. Terratest adds a random attribute (`eg-test-redis-test-<random>`).
- Downstream consumers' inputs (engine_version, transit_encryption_enabled, global_replication_group_id). These are outside this repository, so the module-default entry only covers callers that keep the defaults.
- Redis client libraries and application code: none exist in this repository.
