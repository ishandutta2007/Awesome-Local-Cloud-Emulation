# Awesome-Local-Cloud-Emulation

## Top Local Cloud Emulation Ecosystem



**Curated List of SaaS Products & Open-Source GitHub Projects**  

*Focused on Local AWS/Azure/GCP Emulation, Offline Development & Self-Hosted Cloud Sandboxes*  

**Last updated: October 2026**



This repository tracks notable **SaaS platforms** and **open-source projects** for **Local Cloud Emulation**. These tools simulate cloud provider APIs on your local machine—letting you develop, test, and iterate against AWS, Azure, or GCP services without real cloud accounts, network calls, or bills. They are essential for CI pipelines, offline development, and testing infrastructure-as-code before deployment.



**Examples** include LocalStack, Moto, floci, MiniStack, Azurite, and cloudemu (the category leaders).



**Open-source emphasis**: This section is heavily expanded with every major active project for self-hosting, avoiding paid emulation tiers, and retaining full control over your local cloud environment. The landscape shifted dramatically in **March 2026** when LocalStack's Community Edition went proprietary—requiring an account and auth token, with commercial use restricted on the free tier . This triggered a wave of open-source alternatives. The current open-source landscape is anchored by **Moto** (the mature Python AWS mock), **floci** (the MIT-licensed LocalStack drop-in replacement), **MiniStack** (the multi-account AWS emulator), and **fakecloud** (the depth-first Rust emulator), with **Azurite** serving Azure storage and **cloudemu** providing multi-cloud in-memory emulation.



Contributions welcome! Open a PR to add/update entries. Keep descriptions factual and link to official sites.



## Table of Contents

- [SaaS/Hosted Platforms](#saas-hosted-platforms)

- [Open-Source GitHub Projects](#open-source-github-projects)

- [How to Contribute](#how-to-contribute)

- [Disclaimer](#disclaimer)



## SaaS/Hosted Platforms

| Product | Description | Pricing | Free Tier Limits |
| :--- | :--- | :--- | :--- |
| **[LocalStack](https://localstack.cloud/)** | Emulates AWS locally in Docker. Features base AWS emulation, Cloud Pods state sharing, ephemeral sandboxes, and Azure in private preview. Commercial use on the free Hobby tier is prohibited; requires an account & auth token. | Starts at $39/dev/month (Base plan); $89/dev/month (Ultimate plan) | **Hobby Plan**: $0 forever (Strictly non-commercial use only, community tier service limits, requires auth token).<br>**Free Trial**: 30-day free trial of Ultimate tier (no credit card required). |

*Note: No other dedicated SaaS platforms exist for local cloud emulation. The value of these tools is local execution—cloud providers themselves do not offer hosted "local emulation" services. The paid tiers that exist (LocalStack) are commercial licenses for the same local software.*

## Open-Source GitHub Projects



### AWS Emulation



- **[Moto](https://github.com/getmoto/moto)**  

  **The mature Python AWS mock.** The longest-standing open-source AWS emulator, designed as a library that patches botocore in-process, with a standalone server mode for other SDKs and languages . **Two modes**: in-process (fastest, no external process, Python-only) and standalone server (speaks wire protocol, any language). **Strengths**: enormous service coverage built over years, deeply integrated into Python testing culture, and zero infrastructure for Python tests. **Limitations**: in-process mode cannot run real Lambda code, real databases, or real Redis—it mocks API shapes, not execution . Best for Python test suites that need fast, dependency-free AWS mocks.



- **[floci](https://github.com/floci-io/floci)**  

  **The MIT-licensed LocalStack drop-in replacement.** A single static binary speaking the AWS wire protocol on port 4566. **No account, no auth token, no paid tier** . **Migration path**: automatically translates LocalStack's environment variables, runs unmodified init scripts from `/etc/localstack/init/`, and responds on `/_localstack/health`—CI pipelines waiting for that signal won't notice the difference . **Depth-first architecture**: uses real containers when accuracy demands it—Lambda runs inside AWS's official execution image, RDS spins up actual PostgreSQL/MySQL/MariaDB, ElastiCache runs real Valkey . **Multi-account isolation**: a 12-digit `AWS_ACCESS_KEY_ID` becomes the account ID . **Caveat**: mimics AWS behavior, not AWS security—IAM policies are not enforced by default (enable via `FLOCI_SERVICES_IAM_ENFORCEMENT_ENABLED`) . Best for teams migrating from LocalStack who want a free, actively maintained alternative with real backend execution.



- **[MiniStack](https://github.com/ministackorg/ministack)**  

  **The multi-tenant AWS emulator.** Surfaced during the March 2026 LocalStack paywall window . **Multi-tenancy**: a 12-digit `AWS_ACCESS_KEY_ID` becomes the account ID, with full resource isolation per account—multiple developers or CI pipelines can share one endpoint without collisions . **Multi-region**: state is isolated per region since 1.4.0 . **Compatibility**: responds on LocalStack's health endpoints (`/_localstack/health`), easing migration . **Features**: reset endpoint for clean CI state, init scripts, and configurable Lambda execution (local or Docker) . Best for teams that want multi-account isolation without configuring separate emulator instances.



- **[fakecloud](https://github.com/faiscadev/fakecloud)**  

  **The depth-first Rust emulator.** Single ~19 MB static binary, AGPL-3.0 licensed. **Philosophy**: 100% behavioral conformance for each service before adding the next—validated against 54,000+ Smithy test variants and Terraform provider acceptance tests . **Coverage**: 23 services, 1,680 operations including S3, SQS, SNS, DynamoDB, Lambda, IAM, STS, KMS, Secrets Manager, CloudFormation, EventBridge, RDS, ElastiCache, Step Functions, and Bedrock . **Real execution**: Lambda runs in Docker across 13 runtimes, RDS runs real PostgreSQL/MySQL/MariaDB, ElastiCache runs real Redis/Valkey . **Cross-service wiring**: EventBridge → Step Functions, S3 → Lambda, and 15+ more integrations actually fire . Best for teams that need high-fidelity cross-service behavior for complex integration tests.



### Azure Emulation



- **[Azurite](https://github.com/Azure/Azurite)**  

  **Microsoft's official Azure Storage emulator.** Open-source (JavaScript/Node.js), cross-platform (Windows, Linux, macOS). Supports **Blob, Queue, and Table** services—but not Azure Files or Data Lake Storage Gen2 . **IP-style URLs**: `http://127.0.0.1:10000/devstoreaccount1/container/blob` (account name is in the path, not hostname) . **Production-style URLs** available via hosts file modification . Best for Azure developers testing storage operations locally without a cloud account.



- **[azlocal](https://github.com/DanielTangnes/azlocal)**  

  **The unified Azure emulator suite.** Orchestrates Azurite, Cosmos DB Linux emulator, Service Bus emulator, and built-in mocks for Key Vault and Event Grid behind a single CLI, declarative YAML config, and web dashboard . **Status**: early development, but orchestration, declarative provisioning/seeding, Key Vault/Event Grid mocks, and CI health reports work . **Quick start**: `azlocal init` → `azlocal up -d` → `azlocal ui` (dashboard at port 8900) . Best for Azure teams that want a single command to start a consistent local environment across multiple services.



- **[Topaz](https://github.com/topaz-project/Topaz)**  

  **The comprehensive Azure emulator.** Covers control and data plane APIs, with **local ARM and Bicep template deployments**, Azure RBAC, and **Microsoft Entra ID tenant emulation** . Best for teams that need to test Azure infrastructure-as-code locally, not just storage.



### Google Cloud Emulation



- **[floci-gcp](https://github.com/floci-io/floci-gcp)**  

  **The free GCP local emulator.** No account, no auth token, no feature gates . **Single port** (4588) for all services via HTTP/2 ALPN negotiation—no per-service daemon setup . **Supported services**: Cloud Storage, Pub/Sub, Firestore, Datastore, Secret Manager, IAM, and Managed Kafka . **Persistence modes**: memory (default, fast), persistent (durable), hybrid (5s async flush), WAL (highest durability) . Best for GCP teams that want a free, LocalStack-style emulator with broad service coverage.



- **[localgcp](https://github.com/slokam-ai/localgcp)**  

  **The Vertex AI-aware GCP emulator.** One Go binary covering 14 services including **Vertex AI** (Gemini API with local model backends via Ollama, OpenAI, or Anthropic), BigQuery, Spanner, Firestore, Pub/Sub, Cloud Storage, Bigtable, Cloud SQL, Memorystore, Cloud Tasks, KMS, Secret Manager, Cloud Run, and Cloud Logging . **Key differentiator**: Vertex AI emulation with `generateContent`, streaming, tool/function calling, and embeddings—backed by local models instead of stubs . Best for teams building GCP AI applications that need to test Vertex AI integration locally.



- **Firebase Local Emulator Suite** — Google-provided suite for local testing across Firebase services including Authentication, Firestore, Realtime Database, and Cloud Functions . Best for Firebase-centric applications.



### Multi-Cloud Emulation



- **[cloudemu](https://github.com/stackshy/cloudemu)**  

  **The multi-cloud in-memory emulator.** Emulates AWS, Azure, and GCP APIs entirely in memory—any language, $0, deterministic, resettable . **Three ways to run**: standalone server/Docker (ports 4566/4568/4569 for AWS/Azure/GCP), in-process Go `httptest.NewServer`, or typed Go API directly . **Coverage**: storage (S3/Blob/GCS), compute (EC2/VM/Compute Engine), NoSQL (DynamoDB/Cosmos/Firestore), relational (RDS/SQL/Cloud SQL), Kubernetes control planes (EKS/AKS/GKE), serverless, messaging, and more . **Kubernetes**: hands back a real kubeconfig for `kubectl` round-trips . **Reset**: `curl -X POST http://127.0.0.1:4566/_cloudemu/reset` for clean test state . **Limitation**: emulates the API layer, not real backend execution—it's a control-surface mock . Best for multi-cloud applications that need one local endpoint for all three providers.



### Additional Strong Open-Source Options



- **S3Mock** — Community implementation of a subset of the Amazon S3 API for local integration testing, with Docker and Testcontainers support .

- **ElasticMQ** — SQS-compatible message queue, usable standalone or embedded .

- **BigQuery Emulator** — Go-based BigQuery API emulator for local query tests .

- **Frameworks for building custom systems**: Use **floci** or **MiniStack** as the AWS backbone (migrate from LocalStack). Use **Azurite** or **azlocal** for Azure. Use **floci-gcp** or **localgcp** for GCP. Use **cloudemu** when you need one emulator across multiple clouds. Use **Moto** for Python-only unit tests.



## How to Contribute



1. Fork the repo.

2. Add/edit entries in `README.md` (follow existing format).

3. Include: name, link, 1–2 sentence description, and whether it's SaaS or open-source.

4. Submit PR with a short explanation.



Star the repo if you find it useful!



## Disclaimer



- This is a **community-curated** list — not exhaustive and not an endorsement.

- **LocalStack is no longer freely usable for commercial work**: the Community Edition requires authentication and prohibits commercial use on the free tier. The open-source alternatives above exist specifically to fill this gap .

- **Emulation fidelity varies**: in-process mocks (Moto) are fast but don't execute real code. Wire-protocol servers (floci, MiniStack, fakecloud) can run real Lambda/databases but are heavier. Choose based on whether your tests need API shapes or actual cross-service behavior .

- **Security is not emulated**: floci explicitly does not enforce IAM policies by default—it mimics AWS behavior, not AWS security. Enable `FLOCI_SERVICES_IAM_ENFORCEMENT_ENABLED` if you need policy validation .

- **Data persistence defaults vary**: floci stores in memory by default (lost on shutdown); MiniStack persists; Azurite has configurable persistence. Configure volumes for state you want to keep across restarts.



---



**Made for cloud developers, DevOps engineers, and teams building offline-first cloud workflows.**  

Let's make local cloud emulation more open, faithful, and appropriately scoped.
