# fline — architecture notes

[dev.fline.sh](https://dev.fline.sh) · private beta · seven-module Go monorepo, built solo

fline gives an app built with a coding agent its own address, database and domain. The
source is closed while the platform is in private beta, so these are notes on how it is
put together and why.

---

## The problem

A coding agent can now write a working application in an afternoon. Getting that
application onto the internet is still the slow part, and most of the slowness is
configuration the agent has to invent: a Dockerfile, a compose file, a database URL, a
TLS certificate, a DNS record. Each one is a place for the agent to guess wrong, and
each one is a secret or a piece of infrastructure the agent now has to be trusted with.

fline removes the configuration step rather than generating it, and it assumes the agent
is not trustworthy with credentials.

---

## Control plane and executors

All state and every decision live in the control plane. Executor daemons hold none: they
poll for desired state and reconcile the containers on their host toward it.

This is the standard reconciliation split, and it is chosen for the standard reason — an
executor that crashes, or is disconnected for an hour, or is replaced entirely, converges
back to the correct state on its next poll without anyone replaying a queue of missed
commands. The control plane never has to know whether an executor is reachable in order
to accept a deploy.

Deploys are **immutable and versioned**. A rollback republishes an earlier version as a
new one; it does not move a pointer backwards. The distinction matters after an incident:
the history reads as an append-only log of what was live and when, and there is no state
in which two observers disagree about what "the previous version" meant.

## The builder

The builder turns an uploaded source archive into an image, with **no configuration file
written by the tenant**. Nothing to get wrong in a Dockerfile means one fewer failure mode
that only shows up at deploy time, and nothing for the agent to hallucinate.

## Privilege separation, enforced by a test

The tenant-facing service is four processes holding deliberately different credentials.
The boundary between them is enforced by an **import-graph test**, not by convention: the
build fails if a package on the wrong side of the line imports across it.

The process that parses untrusted input — uploaded archives and model output — holds one
model API key and can reach no database, no control plane, no object storage and no PKI.
That is the process an attacker gets if they get anything, and it is deliberately the one
holding least.

Convention would have been cheaper to write and would have decayed on the first hurried
change. A test fails loudly instead.

## A database per tenant, behind an SNI router

Every tenant gets a dedicated Postgres or Valkey: its own container, its own certificate,
its own credentials. No shared instance, no row-level isolation to get right, no noisy
neighbour.

Routing to them is the interesting part. An SNI router picks the backend **without
decrypting the connection** — it reads the server name from the TLS handshake and splices
the two sides together. The wrinkle is that Postgres does not speak TLS from the first
byte: the client opens in plaintext, sends an `SSLRequest`, and only then negotiates.
There is no SNI to read yet at the point a normal TLS router expects one, so the router
handles the Postgres preamble itself before the handshake it is actually routing on.

## Identity and certificates

An internal PKI issues **auto-renewing mTLS identities to every daemon**, so control-plane
and executor traffic is mutually authenticated and a compromised host's identity expires
on its own rather than waiting to be noticed and revoked by hand.

For tenant-facing domains, **ACME DNS-01** means a custom domain is issued and renewed
without the tenant touching DNS — and, more to the point, without a human in the loop on
a 90-day cycle.

## The agent-facing surface

Deployment is exposed over **MCP**, so a coding agent drives the platform directly, under
a short and explicitly named list of permissions.

Tenant secrets are entered in the browser and **never handed back to the agent**. The agent
can deploy an application that uses a secret without ever being able to read it. This is
the same premise as the process split above, applied at the edge: the component most
likely to be manipulated is the one given least.

---

## Stack

Go · PostgreSQL · Valkey · Docker · Harbor · Ansible · ACME (DNS-01) · MCP

---

The same ideas, in code you can actually read: **[Jobbox](https://github.com/VladosusCocosus/jobbox)**
constrains an LLM agent so that no tool it holds can write to the user's data — every tool
appends a proposal, and the only write path is a human keystroke.
