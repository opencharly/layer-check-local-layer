# AGENTS.md — layer-check-local-layer

Standalone candy repo for the `check-local-layer` fixture — the deployable member
of the `check-local` bed. It writes `/etc/check-local-marker` (and an act-step
marker under `/tmp`), declares a custom systemd service to prove the
render-service dispatch path, and carries **no `skill:` entity**.

Canonical files:

- `charly.yml` — the `check-local-layer:` candy entity (a `service:` entry, the
  `write:` / act-verb run steps, and the `file:` / `command:` `check:` probes; no
  `skill:` entity).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-check:check` — the owning family skill: the check plan authoring
  reference, the disposable beds, the deploy-scope check model, and the R10
  change classes. Load before editing any `plan:` step.
- `/charly-local:local-deploy` — the `target: local` deploy surface (the `host:`
  field, the `local:` substrate, the install ledger, teardown) this member runs
  on. Load when a change touches the host-venue path.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`, package sections, service declarations).
  Load before editing any entity field or plan step.
- **Missing owning skill:** this fixture has no `skill:` entity, so no
  `/charly-check-local-layer:*` page is projected for it. The gap is recorded
  against the named batch
  [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291).

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has no
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The fixture's proof is the `check-local` `kind: local` template: the markers on
  the host filesystem after `charly deploy add`, and the `systemd` service
  reported active — all asserted by the bed's deploy-scope probes.

## Modify this repo

- Keep both marker paths and contents stable: the bed's host-side probes assert
  `/etc/check-local-marker` (content `check-local v1`) and the act-step marker.
- The custom `service:` entry is the dispatch-path proof; keep it non-packaged
  and self-contained (a trivial sleep daemon), and keep the start-marker write.
- If an owning skill is authored, add the `skill:` entity here and update this
  signpost and the README in the same change.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
