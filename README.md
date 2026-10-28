# Fleet A vs Fleet B — Kubernetes HPA simulator

A teaching simulator for the Horizontal Pod Autoscaler control loop: two
fleets receive **identical job streams**; only the autoscaler metric
differs. Watch why queue-depth metrics overshoot while per-pod metrics
converge — with the real kube-controller-manager semantics, not a
caricature.

**→ [Open the simulator](./index.html)** · [v1, the original single-file edition](./v1.html)

## What it shows

- The full reconcile pipeline per sync: metric proposal (inclusive ±10 %
  deadband, `ceil[ratio × readyPods]`, scale-from-zero branches) →
  stabilization windows (clamp of the *current* count between the up-window
  minimum and down-window maximum of remembered desires) → rate policies
  (`Pods`/`Percent` per `periodSeconds`, `selectPolicy`, direction guards).
- The legacy `behavior == nil` path too: rolling max-of-window + the
  2×/-or-`+4` scale-up cap.
- Hover any log line to see which stage decided the outcome, with the
  arithmetic spelled out and the stabilization chips linked to the clamps.
- End-user graphs (worst live wait, p95 turnaround) beside the HPA's view
  — the distance between them is the point.

## How faithful is it?

Every control-loop rule mirrors `kubernetes/kubernetes` at commit
`8fae0b8f` — `horizontal.go`, `replica_calculator.go`,
`metrics/utilization.go`, `apis/autoscaling/v2/defaults.go` — pinned by a
spec suite in the engine repo (88 tests, including the quirks:
integer-truncated utilization percent, ceil-vs-trunc asymmetry between
up/down percent policies, `policies: []` freezing a direction, the
two-layer webhook-vs-controller defaulting). Teaching presets (60 s
up-window, 30 s sync) are clearly separated from spec-true defaults.

The engine is a deterministic discrete-event simulator (integer ticks,
seeded streams, golden traces) — same seed, identical run, always.

Built from the `sim/` workspace (`@k8ssim/des-core` + `@k8ssim/hpa-sim`);
this page is a single self-contained HTML file — view source, no network
requests.
