## Vlad Razin

Senior backend engineer — distributed systems and LLM pipelines. Six years across
healthcare, private aviation and edtech, mostly TypeScript and Node.js on PostgreSQL,
AWS and Kubernetes.

Currently at **Kanda Software**, building the AI agent layer for a platform that sources
private jet charters over email. Before that, five years at **Health Samurai** — building
healthcare products on Aidbox, their FHIR platform, and working on the platform itself.
I grew from junior to senior there, and led a clinical search migration over ~16 TB that
took p95 latency from ~4s to ~180ms.

Alongside that I co-founded **418Team**, a digital agency run as a side business, where the
driving-exam app we built for Kazakhstan reached #1 in Education on the App Store within a
month of launch — ahead of Duolingo and Photomath.

Based in Oviedo, Spain.

---

### What I'm building

#### [fline](https://dev.fline.sh) · private beta

A deployment platform that gives an app built with a coding agent its own address,
database and domain. Seven-module Go monorepo, built solo.

A control plane owns all state and every decision; executor daemons poll it for desired
state and run tenant containers; a builder turns an uploaded source archive into an image
with no configuration file written by the tenant. Each tenant gets a dedicated Postgres or
Valkey behind an SNI router, an internal PKI issues auto-renewing mTLS identities to every
daemon, and ACME DNS-01 means a custom domain is issued and renewed without the tenant
touching DNS. Deployment is exposed over MCP, so a coding agent drives it under a short,
named list of permissions and never sees tenant secrets.

**[Architecture notes →](fline.md)** · Go · PostgreSQL · Valkey · Docker · Harbor · Ansible · MCP

#### [One Day Investor](https://github.com/VladosusCocosus/one-day-investor) · [odinvestor.net](https://odinvestor.net)

A personal investment tracker: pocket-based portfolios, monthly net-worth snapshots,
read-only sync across nine crypto exchanges, and an agent-ready API. A Bun monorepo of
eight deployable services and thirteen shared packages, deployed from GitHub Actions to
GHCR with per-branch preview environments.

Open source · Bun · Elysia · React · PostgreSQL · Redis · Docker

#### [Jobbox](https://github.com/VladosusCocosus/jobbox) · [jobbox.fline.sh](https://jobbox.fline.sh)

A local-first macOS mail client that is really a job-application tracker. No server, no
account and no API key — the agent is the Claude Code CLI already on your machine. A
deterministic local scorer flags candidate mail and records the phrase that flagged each
one, so the model reads a shortlist rather than the mailbox. No tool the agent holds can
write to the tracker: every tool appends a proposal, and the only write path is a human
keystroke.

Open source · TypeScript · Electron · React · SQLite · IMAP

#### [temporal-view](https://github.com/VladosusCocosus/temporal-view)

A React devtool for seeing Temporal workflows on the page. Tag DOM elements with
`temporal-workflow-id` and a floating panel lists them, highlights on hover, and links
through to the Temporal UI.

Open source · TypeScript · React

---

### Stack

| Area | Tools |
|---|---|
| **Languages** | TypeScript · Go · SQL · Rust · C |
| **Backend** | Node.js · NestJS · Elysia · Hono · Temporal · REST · GraphQL |
| **Data** | PostgreSQL · OpenSearch · Redis / Valkey |
| **Infrastructure** | AWS · Kubernetes · Helm · Terraform · Docker · GitHub Actions |
| **AI** | LLM agent pipelines · RAG · MCP · inference cost optimization |
| **Frontend & mobile** | React · Next.js · React Native |

---

### Elsewhere

[Portfolio](https://blog.odinvestor.net/portfolio) ·
[LinkedIn](https://www.linkedin.com/in/vladislav-razin-7b3420240/) ·
[Claude Certified Architect](https://www.credly.com/badges/2205afd8-167b-458f-99af-1015a300c15f) ·
[vlad.razin@fline.sh](mailto:vlad.razin@fline.sh)
