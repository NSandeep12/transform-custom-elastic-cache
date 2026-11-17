# Learnings: ElastiCache Redis OSS end-of-support analysis (cloudposse-elasticache-redis, 2026-11-17)

## What worked
- The repository is a reusable Terraform module (cloudposse/elasticache-redis). Its effective versions came from `variables.tf` defaults (`engine = "redis"`, `engine_version = "7.1"`, `family = "redis7"`, `serverless_major_engine_version = "7"`) and from the example `fixtures.us-east-2.tfvars` files (`7.0` for the complete example, `7` for the serverless example).
- All four caches (the root replication group, the root serverless cache and the two example module calls) are on Redis OSS 7.x, so their status is SUPPORTED. No Extended Support exposure was found.
- Recommendation rule 3 (Valkey 9) applied to every cache: no client libraries are in the repo (the only `go.mod` is Terratest), and every risk score is 2 or lower.

## What failed / was not available
- `live_access=off`, so no AWS calls were made (STS, ElastiCache, Health and Trusted Advisor were all skipped). The status source is `docs`. Drift was not checked, so the `drift` array is empty.
- The newest Valkey version offered in us-east-2 was not confirmed live. Valkey 9.1 (node-based) and major 9 (Serverless) come from the skill's target options and AWS docs.

## Values that could not be resolved
- Callers of this module can override `engine_version`, `family`, `transit_encryption_enabled` and `global_replication_group_id`. Their real values are outside this repository, so callers' deployments need their own analysis.
- I did not verify that the AWS provider supports Valkey 9 at the module's minimum (`>= 5.73.0`). Confirm it before remediation.

## Judgement calls
- I reported the example module calls (Terratest fixtures) as separate caches. They create real caches in a test account.
- The Terratest suite counts as a test environment, so the "No test or staging environment" risk point was not added.
- I didn't count CI pipeline points: the workflows call shared Cloud Posse workflows (Terratest), not production applies.
