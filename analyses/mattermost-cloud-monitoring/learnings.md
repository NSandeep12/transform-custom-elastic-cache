# Learnings: ElastiCache Redis OSS EOS analysis (mattermost-cloud-monitoring)

## What worked
- A repo-wide grep for `elasticache`/`redis` found a single module: `aws/redis` (one `aws_elasticache_cluster`, one subnet group).
- Engine version resolved via `var.redis_version` -> default `7.1` (aws/redis/variables.tf:14).

## What failed / not possible
- Live access disabled (`live_access=off`): no STS, ElastiCache, Health or Trusted Advisor calls. Drift not checked.

## Values that couldn't be resolved
- Callers of `aws/redis`: none in this repo, so `redis_version`, `region`, `instance_type` and `num_cache_nodes` come from defaults only (region has no default).
- Newest Valkey version offered in the Region: taken from docs (9.1), not confirmed with DescribeCacheEngineVersions.
- Whether cache.t2.micro is offered for Valkey in the target Region.

## Notes
- The only CI workflow (.github/workflows/ci.yaml) runs tflint only; no `terraform apply -auto-approve`.
- No application code, client manifests or dev/test Redis images found.
