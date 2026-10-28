# Fleet A vs Fleet B — Kubernetes HPA simulator

Two fleets receive identical job streams; only the autoscaler metric
differs. Watch why queue-depth metrics overshoot while per-pod metrics
converge.

The reconcile pipeline runs with real kube-controller-manager semantics at
commit `8fae0b8f` — metric proposal (±10 % deadband, `ceil[ratio ×
readyPods]`), stabilization windows, rate policies, and the legacy
`behavior == nil` path — pinned by the engine repo's spec suite. Hover any
log line to see which stage decided the outcome.

- [`index.html`](index.html) — v2, deterministic discrete-event engine
- [`v1.html`](v1.html) — the original single-file edition

Each file is fully self-contained: view source, no network requests.
