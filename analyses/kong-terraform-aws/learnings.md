# Learnings: ElastiCache Redis OSS end-of-support analysis (kong-terraform-aws, 2026-11-17)

## What worked
- One Terraform cache found: `aws_elasticache_replication_group.kong` (redis.tf:1) plus `aws_elasticache_parameter_group.kong` (redis.tf:29).
- Engine and version resolved through variable defaults: `redis_engine_version = "5.0.5"` (variables.tf:487), `redis_family = "redis5.0"` (variables.tf:494).
- Lifecycle status came from the documented schedule (Redis OSS 5 in Extended Support since 2026-02-01).

## What failed or was not used, and why
- Live data, AWS Health and Trusted Advisor were not read because the plan context set `live_access=off`. Drift was not checked.

## Values that could not be resolved
- Actual deployed version: probably 5.0.6, because AWS upgraded 5.0.0 to 5.0.5 before Extended Support. Not confirmed.
- Whether the cache is deployed at all: `enable_redis` defaults to false, and no tfvars are in the repository.
- AWS provider version: there is no `required_providers` constraint. CI pins Terraform 0.12.13.
- Region: not set in the module. The README example uses us-west-2. The cost estimate assumes about $0.034/hour for cache.t2.small.
- Kong / lua-resty-redis client version: there is no client manifest in this repository.
- Whether cache.t2.small is offered for Valkey in the Region.
