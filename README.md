# SourceTaintRelay

A GenLayer primitive that turns a pinned, untrusted public text source into a bounded **safe data view** for downstream agents. It is a content transformation, not a graph, certificate, escrow, or generic "AI approves" wrapper.

## Consensus boundary

The registrant supplies a GitHub repository, immutable commit SHA, file path, and topic—not the source text or its safety label. The contract constructs the `raw.githubusercontent.com` URL and independently fetches it inside the GenLayer nondeterministic flow. It normalizes at most eight short lines, computes the full-response and per-line SHA-256 hashes, and asks the leader and validators to classify each original line as `DATA`, `INSTRUCTION`, or `UNCERTAIN`. Exact agreement is required for the entire label vector, source hash, safe text, and report root. `DATA` lines alone enter `get_safe_text`; `INSTRUCTION` lines are excluded. Any `UNCERTAIN` line, invalid model output, malformed text, or ambiguous source fails closed to an empty view.

```text
register pinned GitHub source + topic
             │
             ▼
ingest ── independent HTTP fetch ── bounded line normalization
             │                         │
             │                         ▼
             └── validator-recomputed semantic taint vector
                                       │
                   ┌───────────────────┼──────────────────┐
                   ▼                   ▼                  ▼
                 CLEAN              FILTERED         QUARANTINED
              all lines DATA       DATA lines only     empty view
```

Unreachable or non-200 sources become `UNAVAILABLE` with an empty view and may be retried. A completed classification is terminal: a caller cannot repeatedly sample models until a dangerous line is mislabeled safe. Attempts remain immutable and are keyed by source and attempt ID. Only the registrant may close a source; closing blanks its safe view without erasing prior reports.

## What this does and does not prove

This guards an ingestion boundary for text consumed through `get_safe_text`. It does **not** certify the truth of factual lines, guarantee that every injection is caught, hide the original public URL, or constrain agents that bypass the relay. The demonstration fixtures are synthetic; they prove the filtering mechanism, not any real service status. Pinned commits limit source mutation; validators still fetch and hash the actual response.

## API

`register_source`, `ingest`, `close`, `get_safe_text`, `get_source`, `get_attempt`.

Example fixtures: [clean](fixtures/clean.txt) and [tainted](fixtures/tainted.txt). The tainted fixture contains an instruction to an assistant to upload its local environment file; the intended safe view contains only its separate factual line. See [LIVE_PROOFS.md](LIVE_PROOFS.md) for finalized StudioNet evidence.

## Run

```powershell
genvm-lint check contracts/SourceTaintRelay.py
python -m pytest tests/direct -q
genlayer network set studionet
genlayer deploy --contract contracts/SourceTaintRelay.py
```

For `register_source`, use a 40-character commit SHA containing the fixture file. Then call `ingest(source_id, attempt_id)`. Check the transaction's **leader execution result** and read `get_attempt` and `get_safe_text`; `FINALIZED` alone is not proof of successful execution. The contract pins a concrete GenVM runner.
