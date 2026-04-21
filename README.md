# NC2 Helm Charts Repository

This repository is structured as a Helm charts source that can be consumed directly by Rancher and Helm clients.

## Repository Layout

```text
.
├── charts/                  # Fully owned Helm charts
│   ├── elastic/
│   └── kibana/
├── legacy/                  # Original non-templated manifests kept for reference
├── overrides/               # Custom values for external/official charts
│   └── signoz/
└── packages/                # Packaged chart artifacts and index.yaml (generated, committed intentionally)
```

## Rancher Setup

Use one of the following repository targets in Rancher (**Apps -> Repositories**):

- Git target (recommended when only `github.com` is allowed):
  - Type: Git repository containing Helm charts
  - URL: `https://github.com/msossah-lab/NC2`
  - Branch: `main`
- HTTP Helm target (when raw/static hosting is reachable):
  - Type: HTTP(s) URL to Helm index
  - URL: `https://raw.githubusercontent.com/msossah-lab/NC2/main/packages/index.yaml`

Notes:

- `packages/index.yaml` now uses relative chart archive URLs, which works well for Git-based repository consumption.
- If HTTP mode is used, ensure Rancher can reach the chart archive URLs referenced by `index.yaml`.

## Owned Charts

### Install elastic

```bash
helm install elastic charts/elastic --namespace production --create-namespace
```

Key overridable values in `charts/elastic/values.yaml`:

- `image.repository`, `image.tag`
- `service.type`, `service.port`
- `nodeSets[*].count`
- `nodeSets[*].resources.requests`, `nodeSets[*].resources.limits`
- `env`

### Install kibana

```bash
helm install kibana charts/kibana --namespace production --create-namespace
```

Key overridable values in `charts/kibana/values.yaml`:

- `kibana.image.repository`, `kibana.image.tag`
- `kibana.replicaCount`
- `kibana.service.type`, `kibana.service.port`
- `kibana.resources.requests`, `kibana.resources.limits`
- `kibana.env`
- `fleet.enabled`

## SigNoz Override

For the external SigNoz VM + in-cluster collector deployment model, follow:

- `overrides/signoz/README.md`

## Release Workflow (Owned Charts)

1. Edit chart files under `charts/<chart-name>/`
2. Bump `version` in that chart `Chart.yaml`
3. Run lint:
   - `helm lint charts/kibana`
   - `helm lint charts/elastic`
4. Re-package charts:
   - `helm package charts/kibana -d packages/`
   - `helm package charts/elastic -d packages/`
5. Rebuild index:
   - Git-mode Rancher compatible: `helm repo index packages/ --url packages`
   - Optional for HTTP-hosted repo mode: `helm repo index packages/ --url <public-packages-base-url>`
6. Commit and push changes, including updated files in `packages/`
