# ElastiCache Redis OSS end-of-support analysis: sample results
Output of a read-only analysis (an AWS Transform custom transformation definition) run against public repositories. It looks for ElastiCache Redis OSS caches in Terraform, CloudFormation and CDK, reports their end-of-support status, and recommends a target. It changes no code.

**Status: work in progress.** The results are machine-generated and have not been reviewed by a person. Treat them as examples, not as findings about those projects.

## How these were produced
- Run date: 2026-11-17.
- Definition: `elasticache-redis-oss-eos-analysis` (category `analysis`), local run with `atx custom def exec`.
- The Kong, Mattermost and Cloud Posse reports were run on an earlier draft of the definition. The CloudFormation report was run after a later fix that adds the `EOL` status for Redis 2.x and 3.x (the earlier draft wrongly said Extended Support applied to 2.8.24). The fix doesn't affect Redis 4 and later, so the other three results stand.
- Live AWS access, AWS Health and Trusted Advisor were **off** (`live_access=off`). Status comes from the repository and AWS documentation only. Drift between live clusters and code was not checked.
- Each source repository was cloned at the commit shown below, analysed in a copy, and only the three report files were kept. No source code from those repositories is stored here.

## Results
| Repository (commit) | Cache | Found | Status | Risk (score) | Blockers | Recommended |
|---|---|---|---|---|---|---|
| [kong-terraform-aws](https://github.com/Kong/kong-terraform-aws) (`78d2ecc1d1`) | aws_elasticache_replication_group.kong | redis 5.0.5 | PAYING_NOW | High (6) | 1 | valkey-7.2 (7.2) |
| [mattermost-cloud-monitoring](https://github.com/mattermost/mattermost-cloud-monitoring) (`ee7e8e715d`) | aws_elasticache_cluster.redis_cluster | redis 7.1 | SUPPORTED | Medium (3) | 2 | valkey-9 (9.1) |
| [aws-cloudformation-templates](https://github.com/aws-cloudformation/aws-cloudformation-templates) (`a0f43bc6d2`) | RedisReplicationGroup (Elasticache-snapshot.yaml) | redis 2.8.24 | EOL | Medium (3) | 1 | valkey-9 (9.1) |
| [aws-cloudformation-templates](https://github.com/aws-cloudformation/aws-cloudformation-templates) (`a0f43bc6d2`) | RedisReplicationGroup (Elasticache-snapshot.json) | redis 2.8.24 | EOL | Medium (3) | 1 | valkey-9 (9.1) |
| [cloudposse-elasticache-redis](https://github.com/cloudposse/terraform-aws-elasticache-redis) (`911c1b1008`) | module.redis (examples/complete) -> aws_elasticache_replication_group.default | redis 7.0 | SUPPORTED | Medium (3) | 0 | valkey-9 (9.1) |
| [cloudposse-elasticache-redis](https://github.com/cloudposse/terraform-aws-elasticache-redis) (`911c1b1008`) | module.redis (examples/serverless) -> aws_elasticache_serverless_cache.default | redis 7 | SUPPORTED | Low (0) | 0 | valkey-9 (9) |
| [cloudposse-elasticache-redis](https://github.com/cloudposse/terraform-aws-elasticache-redis) (`911c1b1008`) | aws_elasticache_replication_group.default (module defaults) | redis 7.1 | SUPPORTED | Low (2) | 0 | valkey-9 (9.1) |

Status meanings: `EOL` is a Redis OSS version already past end of life (2.x, 3.x) with no Extended Support. `PAYING_NOW` is Redis OSS 4 or 5 in Extended Support. `SUPPORTED` is Redis OSS 7.x. A blocker is a condition that stops automatic remediation (for example a single-node `aws_elasticache_cluster`, or in-transit encryption off when the target is Valkey).

## Layout
`analyses/<repository>/` holds `elasticache-upgrade-plan.html` (readable report), `elasticache-upgrade-plan.json` (the same data for tools) and `learnings.md` (what the run could and couldn't resolve).

## Source repositories and licenses
- [kong-terraform-aws](https://github.com/Kong/kong-terraform-aws): Zillow Group / Kong Inc. (see repo LICENSE.md)
- [mattermost-cloud-monitoring](https://github.com/mattermost/mattermost-cloud-monitoring): Apache-2.0
- [aws-cloudformation-templates](https://github.com/aws-cloudformation/aws-cloudformation-templates): Apache-2.0
- [cloudposse-elasticache-redis](https://github.com/cloudposse/terraform-aws-elasticache-redis): Apache-2.0

The reports quote short fragments (resource names, line numbers, version values) from those repositories for reference only.
