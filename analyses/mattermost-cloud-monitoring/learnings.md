# Learnings: ElastiCache Redis OSS end-of-support analysis

## What worked
- A repository-wide grep for `elasticache|redis` found the only ElastiCache module, `aws/redis` (aws_elasticache_cluster plus a subnet group).
- The engine version resolved through `var.redis_version` to its default `"7.1"` (variables.tf:14). Status SUPPORTED comes from the lifecycle documentation.
- The blockers come straight from the IaC: a standalone `aws_elasticache_cluster` with no `transit_encryption_enabled`.

## What failed / limits
- Live access was off (`live_access=off`), so no live, Health or Trusted Advisor data was used, and drift and unmanaged caches were not checked. One read-only `sts get-caller-identity` was invoked by mistake during the CI scan. Its output was discarded and not used.

## Values not resolved
- The `aws/redis` module has no callers or tfvars in this repository, so the deployed `redis_version`, `instance_type` and `num_cache_nodes` are unknown. Defaults were used.
- Region: `var.region` has no value in the repository.
- The newest Valkey 9.x in the Region was not confirmed (9.1 is taken from the docs). Whether cache.t2.micro is offered for Valkey was also not confirmed.
