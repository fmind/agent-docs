---
last_verified: 2026-05-08
sources:
  docs: https://cloud.google.com/run/docs
  release: https://cloud.google.com/run/docs/release-notes
---

# Cloud Run

Google Cloud's fully managed serverless container platform. Runs HTTPS-reachable services and one-off jobs from any container image; scales to zero by default; integrates with IAM, VPC, Secret Manager, Cloud SQL, and Pub/Sub.

## Key concepts

- **Service** — long-running HTTPS endpoint; each deploy creates an immutable revision.
- **Job** — run-to-completion container, invoked or scheduled.
- **Revision** — immutable snapshot of image + config + env; rollback by traffic shift.
- **Traffic split** — percentage allocation across revisions, or pinned to one.
- **Scaling** — concurrency per instance, min/max instances, CPU allocation.
- **Service identity** — the service account a service or job runs as.
- **IAM invoker** — `roles/run.invoker` (or `allUsers`) gates request-time invocation.
- **Networking** — `--ingress` controls who can reach the service; `--vpc-egress` controls how outbound traffic leaves.

## Documentation map

### Concepts & overview

- [What is Cloud Run](https://cloud.google.com/run/docs/overview/what-is-cloud-run) — services vs jobs, when to choose each.
- [Container runtime contract](https://cloud.google.com/run/docs/container-contract) — ports, signals, filesystem, lifecycle.
- [Quotas & limits](https://cloud.google.com/run/quotas).
- [Pricing](https://cloud.google.com/run/pricing).
- [Release notes](https://cloud.google.com/run/docs/release-notes).

### Deploy services

- [Deploy a container image](https://cloud.google.com/run/docs/deploying).
- [Deploy from source (buildpacks)](https://cloud.google.com/run/docs/deploying-source-code).
- [`gcloud run deploy` reference](https://cloud.google.com/sdk/gcloud/reference/run/deploy) — every flag.
- [Knative `service.yaml` reference](https://cloud.google.com/run/docs/reference/yaml/v1) — declarative spec.
- [Continuous deployment from Git](https://cloud.google.com/run/docs/continuous-deployment-with-cloud-build).

### Configure services

- [CPU allocation & always-on CPU](https://cloud.google.com/run/docs/configuring/cpu).
- [Memory limits](https://cloud.google.com/run/docs/configuring/memory-limits).
- [Concurrency](https://cloud.google.com/run/docs/configuring/concurrency).
- [Min/max instances](https://cloud.google.com/run/docs/configuring/max-instances).
- [Environment variables](https://cloud.google.com/run/docs/configuring/environment-variables).
- [Secrets (Secret Manager)](https://cloud.google.com/run/docs/configuring/services/secrets).
- [Cloud SQL connections](https://cloud.google.com/run/docs/configuring/connect-cloudsql).
- [Volume mounts](https://cloud.google.com/run/docs/configuring/services/volume-mounts).
- [Startup & liveness probes](https://cloud.google.com/run/docs/configuring/healthchecks).

### Networking

- [Ingress controls](https://cloud.google.com/run/docs/securing/ingress).
- [Direct VPC egress](https://cloud.google.com/run/docs/configuring/vpc-direct-vpc).
- [Serverless VPC connectors](https://cloud.google.com/run/docs/configuring/connecting-vpc).
- [Custom domains](https://cloud.google.com/run/docs/mapping-custom-domains).
- [Static outbound IPs](https://cloud.google.com/run/docs/configuring/static-outbound-ip).

### Identity & access

- [Service identity (runtime SA)](https://cloud.google.com/run/docs/securing/service-identity).
- [Authenticate developers, services, end users](https://cloud.google.com/run/docs/authenticating/overview).
- [Manage access (`run.invoker`)](https://cloud.google.com/run/docs/securing/managing-access).

### Traffic & rollouts

- [Rollbacks, gradual rollouts, traffic migration](https://cloud.google.com/run/docs/rollouts-rollbacks-traffic-migration).
- [Tags & blue-green](https://cloud.google.com/run/docs/managing/services).

### Jobs

- [Create & execute jobs](https://cloud.google.com/run/docs/create-jobs).
- [Schedule a job (Cloud Scheduler)](https://cloud.google.com/run/docs/execute/jobs-on-schedule).
- [`gcloud run jobs deploy` reference](https://cloud.google.com/sdk/gcloud/reference/run/jobs/deploy).

### Triggers

- [HTTPS triggers](https://cloud.google.com/run/docs/triggering/https-request).
- [Pub/Sub push](https://cloud.google.com/run/docs/triggering/pubsub-push).
- [Eventarc events](https://cloud.google.com/run/docs/triggering/trigger-with-events).

### Operations

- [Logging](https://cloud.google.com/run/docs/logging).
- [Metrics & monitoring](https://cloud.google.com/run/docs/monitoring).
- [Troubleshoot](https://cloud.google.com/run/docs/troubleshooting).

### Reference

- [`gcloud run` command group](https://cloud.google.com/sdk/gcloud/reference/run).
- [REST API](https://cloud.google.com/run/docs/reference/rest).
