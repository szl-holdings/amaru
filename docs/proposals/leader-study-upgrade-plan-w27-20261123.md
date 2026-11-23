# Leader-Study Upgrade Plan - W27 loop-3 - 2026-11-23

Status: PROPOSE-ONLY DRAFT - DO NOT MERGE. Owner-gated: merging requires governing approval and the estate signature-wall owner unlock.

Repo: amaru - Rebuilt SZL organ: bounded health endpoint with W3C trace echo and OTLP export (organ contract v2)

Frame: OUROBOROS loop-3 leader study. Governing seat: Kimi kimi-k3.1-pro (json_ok, think off). Advisory seats: Astra/Sol/Claude community rails (provenance unverified, advisory only). Leader evidence web-verified 2026-11-23.

## Leader moats to absorb
- OpenTelemetry + W3C Trace Context: Vendor-neutral distributed tracing with standardized traceparent/tracestate propagation, span semantics, and OTLP export. Moat: Universal interoperability: any conformant tracer, collector, or backend can join the trace without custom integration; the W3C header contract is the industry default.
- Prometheus blackbox-exporter v0.29.1: External HTTP/TCP/ICMP/DNS probing of endpoints from outside the service, measuring real availability and latency as an outside client sees it. Moat: Measures the user-visible truth rather than self-reported health; catches failures (DNS, TLS, network path, wrong status/body) that an in-process /healthz cannot see.
- Kubernetes liveness/readiness/startup probe semantics: Kubelet-driven container health gating: startup probes protect slow starts, liveness restarts deadlocked containers, readiness controls traffic admission. Moat: Health is enforced by the platform itself, decoupled from app code; correct probe separation prevents restart loops and premature traffic.
- Grafana Beyla: eBPF-based zero-instrumentation auto-observability: captures RED metrics and traces for services without code changes. Moat: Observes any process including ones you cannot modify, at kernel level, with near-zero app overhead and no SDK dependency.

## Honest gaps (grounded in repo facts)
- Only traceparent is in the stated contract; tracestate propagation is not mentioned, so W3C Trace Context support is partial relative to the OTel/W3C leader.
- Health is a single self-reported GET /healthz; there is no external probing in the facts, so real outside-in availability (DNS/TLS/network path) is unmeasured.
- The facts describe one endpoint with no stated separation of liveness vs readiness vs startup behavior, leaving Kubernetes probe semantics unaddressed.
- OTLP export is fail-closed to loopback/cluster-local only; this is a deliberate security boundary but means no export path to external/managed backends without an in-cluster collector relay.
- Stdlib-only, no third-party runtime dependencies means hand-rolled OTLP/HTTP and trace handling rather than the hardened OTel SDK, so protocol edge cases (tracestate, span attributes, retries/backoff on export) carry more implementation risk.
- Observability is limited to one span per request; no metrics (RED) or logging contract is stated, unlike leaders that provide RED metrics out of the box.
- Topics are empty and the repo is a fresh rebuild (created 2026-11-18) explicitly not a restoration, so it lacks the battle-testing and community validation the leaders have.

## Dosed upgrade plan
- Dose 1 (contract completeness, low risk): add tracestate accept/propagate alongside traceparent and extend unit tests to cover it, keeping stdlib-only.
- Dose 2 (probe semantics, low risk): document and, if desired, split /healthz into startup/liveness/readiness behaviors and ship example Kubernetes probe manifests; no new runtime deps.
- Dose 3 (outside-in verification, medium): propose an optional blackbox-exporter scrape config and CI job that probes /healthz externally and asserts trace id echo and status.
- Dose 4 (export verification, medium): add an integration test that runs a local in-cluster OTLP collector and asserts exactly one honest span with correct service.name and trace continuity.
- Dose 5 (optional cross-check, higher/optional): evaluate Beyla or OTel SDK as a complementary layer in a separate proposal, without weakening the fail-closed egress policy or the stdlib-only runtime.
- Risk note: Low for docs/tests and tracestate handling; medium for adding CI collectors/probers (new test-only dependencies and workflow surface); higher for introducing Beyla or an OTel SDK, which could conflict with the stdlib-only, no-extra-egress design and must stay optional and isolated.
- Propose-only note: All steps are proposals for maintainer review; none change the protected-main supply-chain workflow, the fail-closed OTLP egress policy, or the stdlib-only runtime without explicit approval. External probing and any eBPF/SDK addition should be additive and optional, preserving the existing signed-digest publication path.

## Advisory notes (unverified rails, non-binding)
- astra: ci-witness-action: Cryptographic provenance standards (SLSA/in-toto) amaru: W3C Trace Context specification authority ChaskaLLM: Hardware-level memory safety primitives
- sol: ci-witness-action: Cryptographic non-repudiation of build provenance. amaru: Standardized observability for distributed trace propagation. ChaskaLLM: Deterministic resource-constrained inference guarantees.
- claude: ci-witness-action: Cryptographic provenance standards for supply-chain attestation. amaru: W3C distributed tracing interoperability benchmarks. ChaskaLLM: Hardware-level memory isolation for inference kernels.

## DO NOT MERGE
This plan is a proposal artifact of the 10-pass OUROBOROS loop (loop-3 of 10). No code, no CI, no behavior changes in this file. A reviewer with authority may convert to a work order later.