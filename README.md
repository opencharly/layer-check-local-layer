# layer-check-local-layer

A `target: local` check fixture: the deployable member of the `check-local`
bed.

The `check-local-layer` candy drops `/etc/check-local-marker` on the host
filesystem. It is used by the `check-local` `kind: local` template to exercise
the externalized `target: local` path (`candy/plugin-deploy-local` +
`kit.WalkPlans`):

- the `write:` task lands the marker via the kit walk's Op leg;
- an act-verb run-step lands a second marker (`/tmp/charly-check-local-act/marker`)
  via the host's `RunHostStep` act-`OpStep` arm;
- deploy-scope `check:` probes run on the host (not in a container) to verify both
  markers after `charly deploy add`.

The candy also declares a custom (non-packaged) `systemd` service,
`check-local-marker-daemon`, proving the render-service **DISPATCH** path a
`target: local` / `target: vm` deploy-compile exercises: deploy-compile →
`sdk/deploykit`'s `CompileServiceSteps` → `renderServiceViaSeam` →
`InvokeProvider(kind:init)` + `InvokeProvider(verb:egress)` → the rendered unit
installed at the target's own `MachineVenue` apply. The unit writes a marker on
start and stays running — a trivial sleep daemon, no real workload.

The candy is a **fixture**: it ships no user-facing service and has no `skill:`
entity. The owning family skill is `/charly-check:check` (with
`/charly-local:local-deploy` for the target surface); the missing owning `skill:`
entity is tracked by
[opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291).

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `check-local-layer` |
| Target | `target: local` (host filesystem) |
| Effect | writes `/etc/check-local-marker` (mode `0644`, `check-local v1`) and `/tmp/charly-check-local-act/marker` |
| Service | `check-local-marker-daemon` (custom systemd unit, scope `system`) |
| Plan | `write:` + act-verb run steps, `file:`/`command:` `check:` probes |
| Owns | 0 `skill:` entities (fixture) |

## How to use it

Compose it as a layer ref on a `target: local` deploy (a `local:` node). A box is
a `candy:` node carrying the box's `base:` image and a nested `candy:` list of
layer refs (the nested `candy:` is the composition list; the outer `candy:` is
the box body):

```yaml
my-box:
  candy:                  # the box body (an IMAGE is a `candy:` node carrying `base:`)
    base: fedora          # the box's base image
    candy:                # the box's composition list
      - '@github.com/opencharly/layer-check-local-layer:v2026.241.1216'
```

The fixture is driven by the `check-local` `kind: local` template as its
deployable member, not by a user box.

## Layout

- `charly.yml` — the `check-local-layer:` candy entity (a `service:` entry plus
  the plan; no `skill:` entity).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning family skill: `/charly-check:check`
- Target skill: `/charly-local:local-deploy`
- Missing `skill:` entity: [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291)
- Authoring reference: `/charly-image:layer`
- [`opencharly/marketplace`](https://github.com/opencharly/marketplace) — the projected corpus
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
