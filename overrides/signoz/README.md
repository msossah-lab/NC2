# SigNoz OTel Collector Override

## Context

SigNoz core is hosted on a dedicated VM outside Kubernetes.  
Inside the cluster, only the OpenTelemetry collector components are deployed with the official `signoz/k8s-infra` chart.

## Official Chart

- Chart: `signoz/k8s-infra`
- Recommended version: pin to a tested chart release before production rollout (for example `--version <CHART_VERSION>`).

## Install With Overrides

```bash
helm repo add signoz https://charts.signoz.io
helm repo update
helm upgrade --install k8s-infra signoz/k8s-infra \
  --namespace monitoring \
  -f overrides/signoz/otel-collector-values.yaml
```

## Values To Replace

Update these placeholders in `otel-collector-values.yaml` before deploy:

- `SIGNOZ_VM_IP` in `otelExporterEndpoint`
- `global.deploymentEnvironment` if you use a different environment name
