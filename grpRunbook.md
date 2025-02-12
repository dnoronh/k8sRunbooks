# Searchengine - gRPC high error rate

## Table of Contents

- [Alerts](#alerts)
- [Description](#description)
- [Services and Systems Affected](#services-and-systems-affected)
- [Observability](#observability)
- [Mitigation](#mitigation)

## Alerts

- `High gRPC error rate - Searchengine`

## Description

The error rate of one or multiple gRPC endpoints on the `searchengine` services exceeded a defined
threshold that is considered having a critical impact to users.

## Services and Systems Affected

`searchengine` is the entry point for search requests for any client. Therefore, this issue affects
any upstream client calling `searchengine`, e.g. through gateway. Any errors might also be caused by
any downstream services due to error propagation when not handled.

List of services downstream and in chained call order that might be affected:

- `searchengine` -> `preselection`
- `searchengine` -> `dealservice`
- `searchengine` -> `dealservice` -> `dealcreator` -> `price-fetcher`
- `searchengine` -> `rlc`

## Observability

First, figure out which services are the root of the errors, then:

- Check the service specific application logs for error messages (check RUNBOOK for links).
- Check the service specific grafana dashboards for further metrics that might indicate what's
  wrong, e.g. CPU, memory, JVM metrics (check RUNBOOK for links).
- Check if HPA of the specific service is maxed out,
  e.g. `kubectl describe horizontalpodautoscalers searchengine`
- Check most recent releases by the search-core team.

## Mitigation

### Scalability issues

If you observe that any services are overburdened, consider scaling up. Check first if an HPA is
available (likely there is): `kubectl get hpa`, e.g.
`kubectl describe horizontalpodautoscalers searchengine`.

```text
Reference:                               Deployment/searchengine
Metrics:                                 ( current / target )
  resource cpu on pods:                  57% (680m) / 70%
Min replicas:                            2
Max replicas:                            4
Deployment pods:                         4 current / 4 desired # <- Maxed out with max replicas
```

If maxed out, increase max replica count:

```bash
kubectl edit horizontalpodautoscaler searchengine
```

```yml
spec:
    maxReplicas: 4 # <- Increase this value
    minReplicas: 2
```

Observe relevant dashboards to see if pressure on the service and errors decrease.

### Single pods affected

If you determine that only single pods of a replica set are affected, consider restarting them, e.g.
`kubectl delete pod searchengine-6d7cc9c66-gbj6w`

### Recent release might have broken something

During working hours, get in touch with the team first to check on that. If you determined that the
current release might be broken and the previous one might be fine, rollback, e.g.
for `searchengine`you may use use rollback functionality of ArgoCD by clicking
on `HISTORY AND ROLLBACK` button or manually run

```bash
kubectl rollout undo deployment searchengine
```
