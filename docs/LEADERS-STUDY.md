# Domain Leader Study — amaru (organ / trace echo verification)

OUROBOROS phase-3, cohort-01 tranche-2. PROPOSE-ONLY study card: documentation only, no production change in this PR.

## Domain leaders studied
| Leader | What they have | Key sources |
| --- | --- | --- |
| W3C Trace Context (Level 2) | The traceparent/tracestate propagation standard: exact parse rules, echo-back contract, conformance test vectors | w3c.org/TR/trace-context-2 |
| OpenTelemetry context propagation | Reference implementations of W3C propagation across languages; the "incomplete traces" failure mode from broken context propagation is Their data, our lesson | uptrace.dev blog; openobserve.ai guide |

## What the leaders do better
- The traceparent echo contract is precisely specified: what a receiver must do with invalid, partial, or extra headers, and when to echo vs replace. Our organ verifications define this ad hoc today.
- Conformance is testable: W3C publishes test vectors for header handling, so compliance is receipt-able, not asserted.
- Incomplete-trace postmortems show silently dropped context is the #1 real-world failure; the leader's fix is strict parse + explicit error, mirroring our fail-closed doctrine.

## Absorb actions (proposed, NOT implemented in this PR)
- [ ] Encode the organ's trace-echo rule as a W3C-conformant traceparent parse/echo step with explicit failure receipts.
- [ ] Add baggage-style key:value propagation for organ metadata alongside trace id, using the W3C Baggage header format.
- [ ] Import a subset of W3C conformance test vectors into the repo's test suite so echo behavior is receipt-verified.
- [ ] Document the organ's propagation semantics against the leader's decision table (valid header, extra header, invalid header).

## Sources
- W3C Trace Context Level 2: https://www.w3.org/TR/trace-context-2/
- OTel context propagation guide: https://uptrace.dev/opentelemetry/context-propagation
