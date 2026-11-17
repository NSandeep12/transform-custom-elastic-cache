# Learnings – ElastiCache Redis OSS EOS analysis (kong-terraform-aws, 2026-11-17)

## What worked
- Single in-scope cache found quickly: `aws_elasticache_replication_group.kong` (redis.tf:1) plus `aws_elasticache_parameter_group.kong` (redis.tf:29).
- Engine version resolved through variable defaults: `redis_engine_version = "5.0.5"` (variables.tf:487), `redis_family = "redis5.0"` (variables.tf:494).
- The Redis client is the Kong runtime itself (ce_pkg 2.8.1 / ee_pkg 2.8.1.4), not a manifest dependency.

## What failed / was not available
- live_access=off: no STS, Health, Trusted Advisor, describe calls or DescribeCacheEngineVersions. Status is from docs; drift not checked; target versions (7.2 / 8.2 / 9.1) not confirmed for the Region.

## Values that could not be resolved
- Whether the cache is deployed (`enable_redis` defaults to false) and any caller overrides of version, family, node type, count.
- AWS Region (no provider block; README example uses us-west-2) – cost estimate uses an assumed cache.t2.small rate.
- AWS provider version (unpinned, no lock file); caller's Terraform version (CI pins 0.12.13 for fmt/init only).
- Whether cache.t2.small is supported by the target engine in the Region.

## Judgment calls
- "No staging environment found" scored +1: the repo is a generic module with no per-environment configuration.
- "Client older than 3 years" scored +1 for Kong 2.8.1 (2022). This pushed the score to 6 (High), so rule 2 (Valkey 7.2) applies over rule 3 (Valkey 9).
- Missing `transit_encryption_enabled` was reported as a blocker for a Valkey target.
