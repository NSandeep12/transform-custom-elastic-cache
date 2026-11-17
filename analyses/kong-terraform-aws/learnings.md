# Learnings — ElastiCache Redis OSS EOS analysis (kong-terraform-aws, 2026-11-17)

## What worked
- One Terraform replication group (`redis.tf:1`) with its engine version resolved from the `var.redis_engine_version` default (`variables.tf:487`, "5.0.5"). There are no tfvars files.
- Status came from the lifecycle docs: Redis OSS 5 is PAYING_NOW (Extended Support Year 1 since 2026-02-01).

## What failed / not done
- Live access, AWS Health and Trusted Advisor were not used because `live_access=off`. Drift, the live version (probably 5.0.6) and unmanaged caches were not checked.

## Values not resolved
- Region: comes from `data.aws_region.current`, so the cost estimate assumes us-east-1 pricing for cache.t2.small (~$0.034/hour).
- Whether the cache is deployed: `enable_redis` defaults to false.
- AWS provider version: not pinned.
- Kong Redis client version: lua-resty-redis inside Kong, not in any manifest. No known minimum, so the client check passes.
- Whether cache.t2.small is offered for Valkey 9 in the target Region.

## Judgement calls
- I scored automatic failover as off (+2) because `automatic_failover_enabled` is unset.
- I scored "no staging environment" (+1) because this is a module that has `var.environment` but no staging configuration.
- I did not count the CI pipeline: it only runs fmt/init on pull requests.
