# Learnings: ElastiCache Redis OSS end-of-support analysis

Run date: 2026-11-17. Inputs: live_access=off, everything else default.

## What worked
- A repository-wide search for `elasticache|redis|valkey|memcache` found one module, `aws/redis`, with a single `aws_elasticache_cluster` (engine redis, version 7.1 from the variable default).
- There's no application code, client manifest, docker-compose or Helm Redis image, so no app-impact findings.
- CI (`.github/workflows/ci.yaml`) only runs tflint, with no `terraform apply -auto-approve`, so no pipeline risk point.

## What failed or was skipped, and why
- Live data, AWS Health and Trusted Advisor: not read because live_access=off. Drift, deployment state, the real engine version and the Valkey versions offered per Region were not checked.

## Values I couldn't resolve
- `var.region` and `var.name` (the cache ID): no caller or tfvars in this repository sets them.
- Effective `redis_version`: assumed to be the default `7.1`. A caller outside this repo could override it.
- Whether `cache.t2.micro` is offered for Valkey 9 in the target Region. Confirm it before upgrading.
- The newest Valkey 9.x in the Region (9.1 taken from the docs).
