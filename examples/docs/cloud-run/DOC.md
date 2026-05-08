# Cloud Run

Google Cloud's fully managed serverless container platform. Runs HTTPS-reachable services and one-off jobs from any container image; scales to zero by default; integrates with IAM, VPC, Secret Manager, Cloud SQL, and Pub/Sub. Two resource shapes — **services** (long-lived, request-driven) and **jobs** (run-to-completion, scheduled or invoked).

## Key concepts

- **Services** — long-running HTTPS endpoints. Each deploy creates an immutable **revision**; traffic is split across revisions by percentage (or pinned to one).
- **Jobs** — execute a container to completion. Useful for batch work, migrations, scheduled tasks (paired with Cloud Scheduler).
- **Revisions** — immutable snapshots of image + config + env. Roll back by shifting traffic to a previous revision.
- **Scaling** — concurrency per instance, min/max instances, CPU allocation (request-only vs always-on). Scales to zero unless `min-instances` is set.
- **Service accounts & IAM** — every service runs as a service account; callers need `roles/run.invoker` (or `allUsers` for public services).
- **Networking** — direct VPC egress or Serverless VPC Connector; ingress controls (`all`, `internal`, `internal-and-cloud-load-balancing`).
- **Secrets & config** — env vars and Secret Manager refs at deploy time; mount secrets as files or env.

## Frequently used flags

- `gcloud run deploy <service> --image <img>` — deploy/update a service.
- `gcloud run services update-traffic <service> --to-revisions=<rev>=<pct>` — split traffic.
- `gcloud run jobs create|execute <job>` — define and run a job.
- `--allow-unauthenticated` / `--no-allow-unauthenticated` — public vs IAM-gated.
- `--min-instances`, `--max-instances`, `--concurrency`, `--cpu`, `--memory` — sizing knobs.
- `--set-env-vars`, `--set-secrets` — environment.

## Canonical docs

- <https://cloud.google.com/run/docs>
- <https://cloud.google.com/run/docs/reference/yaml/v1>

_Last verified: 2026-05-08 against <https://cloud.google.com/run/docs>._
