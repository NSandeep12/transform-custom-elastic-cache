# ElastiCache Redis OSS end-of-support analysis: sample results
Output of a read-only analysis (an AWS Transform custom transformation definition) run against public repositories. It looks for ElastiCache Redis OSS caches in Terraform, CloudFormation and CDK, reports their end-of-support status, and recommends a target. It changes no code.

**Status: work in progress.** The results are machine-generated and have not been reviewed by a person. Treat them as examples, not as findings about those projects.

## How these were produced
- Run date: 2026-11-17. Definition: `elasticache-redis-oss-eos-analysis` (category `analysis`, draft version `3h971ofet2lmvwwnx9iujx3a`), local run with `atx custom def exec`. All four repositories ran on the same version.
- Live AWS access, AWS Health and Trusted Advisor were **off** (`live_access=off`). Status comes from the repository and AWS documentation only. Drift between live clusters and code was not checked.
- Each source repository was cloned at the commit shown below and analysed in a copy. Only the three report files were kept; no source code from those repositories is stored here.

## Results
| Repository (commit) | Cache | Found | Status | Risk (score) | Blockers | Recommended |
|---|---|---|---|---|---|---|
| [kong-terraform-aws](https://github.com/Kong/kong-terraform-aws) (`78d2ecc1d1`) | aws_elasticache_replication_group.kong | redis 5.0.5 | PAYING_NOW | Medium (3) | 1 | valkey-9 (9.1) |
| [mattermost-cloud-monitoring](https://github.com/mattermost/mattermost-cloud-monitoring) (`ee7e8e715d`) | aws_elasticache_cluster.redis_cluster | redis 7.1 | SUPPORTED | Medium (3) | 2 | valkey-9 (9.1) |
| [aws-cloudformation-templates](https://github.com/aws-cloudformation/aws-cloudformation-templates) (`a0f43bc6d2`) | RedisReplicationGroup | redis 2.8.24 | EOL | Medium (3) | 1 | valkey-9 (9.1) |
| [aws-cloudformation-templates](https://github.com/aws-cloudformation/aws-cloudformation-templates) (`a0f43bc6d2`) | RedisReplicationGroup | redis 2.8.24 | EOL | Medium (3) | 1 | valkey-9 (9.1) |
| [cloudposse-elasticache-redis](https://github.com/cloudposse/terraform-aws-elasticache-redis) (`911c1b1008`) | aws_elasticache_replication_group.default | redis 7.1 | SUPPORTED | Low (2) | 0 | valkey-9 (9.1) |
| [cloudposse-elasticache-redis](https://github.com/cloudposse/terraform-aws-elasticache-redis) (`911c1b1008`) | aws_elasticache_serverless_cache.default | redis 7 | SUPPORTED | Low (0) | 0 | valkey-9 (9) |
| [cloudposse-elasticache-redis](https://github.com/cloudposse/terraform-aws-elasticache-redis) (`911c1b1008`) | module.redis (examples/complete) | redis 7.0 | SUPPORTED | Low (2) | 0 | valkey-9 (9.1) |
| [cloudposse-elasticache-redis](https://github.com/cloudposse/terraform-aws-elasticache-redis) (`911c1b1008`) | module.redis (examples/serverless) | redis 7 | SUPPORTED | Low (0) | 0 | valkey-9 (9) |

Status meanings: `EOL` is a Redis OSS version past end of life (2.x, 3.x) that isn't in the AWS Extended Support schedule. `PAYING_NOW` is Redis OSS 4 or 5 in Extended Support. `SUPPORTED` is Redis OSS 7.x. A blocker is a condition that stops automatic remediation (for example a single-node `aws_elasticache_cluster`, or in-transit encryption off when the target is Valkey).

## Known limitations
- The Cloud Posse repository is a module with example callers, so the same resource can appear once as module defaults and once as an example. Entries overlap.
- Without live data, a cache declared at Redis 5.0.0 to 5.0.5 is treated as running 5.0.6, because AWS upgraded such caches to 5.0.6 when Extended Support began.
- Redis 2.x and 3.x are past end of life. The AWS docs don't say whether a direct upgrade to Valkey is allowed from them, so the report says to confirm that.

## What changed since the first commit
The first reports had four errors, found by checking them against the AWS documentation. They are fixed here.
1. Redis 2.8.24 was reported as paying Extended Support. It is past end of life and not in the Extended Support schedule (status `EOL`).
2. A report said the Year 2 Extended Support premium is higher than Year 1. AWS lists 80% for Years 1 and 2, and 160% for Year 3.
3. A declared Redis 5.0.5 was scored as "older than 5.0.6". AWS upgraded such caches to 5.0.6, so the points no longer apply without live data.
4. Supported caches with a Medium risk score got Medium-severity findings, and a Terratest harness counted as an unreviewed deployment pipeline. Supported caches are now Low severity, and test harnesses no longer count.

## Layout
`analyses/<repository>/` holds `elasticache-upgrade-plan.html` (readable report), `elasticache-upgrade-plan.json` (the same data for tools) and `learnings.md` (what the run could and couldn't resolve).

## Source repositories and licenses
- [kong-terraform-aws](https://github.com/Kong/kong-terraform-aws): Zillow Group / Kong Inc. (see the repository LICENSE.md)
- [mattermost-cloud-monitoring](https://github.com/mattermost/mattermost-cloud-monitoring): Apache-2.0
- [aws-cloudformation-templates](https://github.com/aws-cloudformation/aws-cloudformation-templates): Apache-2.0
- [cloudposse-elasticache-redis](https://github.com/cloudposse/terraform-aws-elasticache-redis): Apache-2.0

The reports quote short fragments (resource names, line numbers, version values) from those repositories for reference only.
