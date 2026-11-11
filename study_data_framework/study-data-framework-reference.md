# A calendar-driven framework for study data

Architecture and build history, de-identified. Author: Ismail Imad Nashwan.

This describes a system that tracks and routes the data of a clinical research study from acquisition to validated storage. It was built for generality and is applied to two studies at one site. Participant identifiers, hosts, storage names and site details are omitted. Facts are as recorded during the build (19 October to 11 November 2026) and were not re-checked against the running code when this was written.

## 1. The problem

Each participant produces intake audio, MRI, EEG, eye-tracking, behavioural, neuronavigation and treatment recordings, on different machines and on different days. Before this system, a person renamed, de-identified, uploaded and checked each one by hand.

## 2. The general core

These seven structures recurred in every stage of the build, whatever the data was.

1. **Monotone node states.** Every participant, visit and data stream is a node. Its state can only move forward, and only the process that owns a node may judge it.
2. **An authoritative schedule.** The study calendar defines what should exist. Timers are keyed by kind, subject and date, and are armed only when the key is new.
3. **One executor, read-only observers.** A single always-on machine runs the loops. Other machines hold a derived view and can only send it signals.
4. **Addresses with permissions.** Each storage location has a permission (some are read-only). Reads consult a cached index first and go live only on a miss.
5. **Human steps as log entries.** A step that needs a person is a logged confirmation. It never blocks a loop, and only a person can mark an item done.
6. **Remote workers under a contract.** Work on other machines returns exit codes with fixed meanings, and each machine keeps its own copy of the scripts and index.
7. **Versioned, encrypted transport.** Code and state move between machines as chained encrypted commits.

## 3. The application layer

Everything that had to be decided for a specific study:

- the roster and the map between identified and de-identified participant codes;
- the visit diagram, which defines the nodes and the expected data;
- the identified and de-identified storage trees, and which of them are read-only;
- the machines (one executor, three workers);
- the calendar that acts as the schedule, and the daily pass times;
- the human approval points (for example, clinician approval of generated stimuli);
- the drivers for outside services (browser-automated exports and document preparation).

The two studies currently written in have different visit schedules and data streams and share the core: 28 processes on four machines.

Requirements: cloud storage, separately stored behavioural data, and an institutional network.

## 4. Extensions built on the core

- **Exports:** per-stage study tables computed from resolved state.
- **Outlets:** a generic "arrival, command, done-check" step that runs preprocessing for each scheduled session on another machine under a resource controller.
- **Pooled analyses:** fits over every session once preprocessing has run.

## 5. Build history

A first version (about 9,000 lines of Python with a terminal interface) ran by mid-October 2026. The stages below hardened it.

| Stage | Dates (2026) | What was done | Structure it added |
|---|---|---|---|
| 1. Map, sandbox, fix | 19–22 Oct | State logic mapped; faults found (a pass that could never fire, a backfill that overrode stricter rules); fixes verified in a sandbox replaying a simulated week | Monotone states; owner-evaluates rule; person-only states; anchored, idempotent patches |
| 2. Always-on | 22–28 Oct | Execution moved to a dedicated machine; state carried in an encrypted, versioned store; observer roles defined | Single executor with read-only projections; signals as the only channel |
| 3. Data truth | 28–30 Oct | Guards added after an empty calendar read and a zero-byte scan database; four daily passes; visit diagrams made the specification | Source validation before overwrite; diagram as specification; exit-code contract |
| 4. Intake automation | 30 Oct–2 Nov | Transcription and stimulus preparation automated with clinician approval; a record-and-replay browser driver for exports | Outside services as drivers with health states; human gates that close on an external action |
| 5. Protections | 2–4 Nov | Read-only stores enforced; cache-then-live search; driver health and restart as loop steps | Permission partition; going-forward-only changes |
| 6. Validation semantics | 4–6 Nov | Validation made a logged human step, never a gate; next-day checks with retries for data that lags; every process marked live on 5 November | Split between machine-settable states and the human-only "done" |
| 7. Beyond the loop | 6–11 Nov | Eye-tracking preprocessing made a self-contained pipeline run on a GPU workstation; scheduled outlets opened | Outlets and pooled stages |

By 11 November every step of the daily loop completed without error.

## 6. Failures found

- A calendar read returned an empty calendar, which left an invitation sender inactive for about two weeks before it was noticed. Sources are now validated before use and short or empty reads are refused.
- A full temporary disk on the executor stopped state synchronisation for two days. Failed transfers are now reported.
- A stricter rule could not correct a node already marked done, because states only move forward. Such corrections need a one-off script, by design.

## 7. Limits

- One maintainer, and no independent code review.
- Generality is a design property shown on two studies at one site. It has not been tested on a third study or at another institution.
- The system is purpose-built and does not use a general workflow orchestrator.
