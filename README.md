# Focus Data Presentation Azure Composition
This composition is part of the FinOps Data Presentation component, in the Krateo Composable FinOps.

## Summary

1. [Overview](#overview)
2. [Architecture](#architecture)
3. [Examples](#examples)
4. [Configuration](#configuration)

## Overview
This composition allows for the creation of a single resource, the `DataPresentationAzure` and let the [Composition Dynamic Controller](https://github.com/krateoplatformops/composition-dynamic-controller) automatically compile the FocusConfig resource from the [finops-operator-focus](https://github.com/krateoplatformops/finops-operator-focus), which also starts the exporting/scraping process, automatically storing data into the database. See [finops-operator-exporter](https://github.com/krateoplatformops/finops-operator-exporter) and [finops-operator-scraper](https://github.com/krateoplatformops/finops-operator-scraper) for more information.

## Architecture
In the diagram, this component is the `Focus Data Presentation Composition` (in this specific instance, the provider is Azure).

![FinOps Composition Definition Parser](_diagrams/architecture.png)

## Examples
The configuration requires the filter for the `DataPresentationAzure` resource, and the `ScraperConfig`, to allow the data to be stored into the database (**note**: the `tableName` must be set to _pricing_table_, or consider modifying the notebook on the finops-database-handler for frontend presentation):
```yaml
apiVersion: composition.krateo.io/v0-1-0
kind: AzurePricingFinops
metadata:
  name: finops-example-azure-vm-pricing
  namespace: azure-pricing-system
spec:
  annotationKey: krateo-finops-focus-resource
  filter: serviceName eq 'Virtual Machines' and armSkuName eq 'Standard_B2s' and type eq 'Consumption'
  operatorFocusNamespace: krateo-system
  scraperConfig:
    tableName: pricing_table
    pollingInterval: "1h"
    scraperDatabaseConfigRef: 
      name: finops-database-handler
      namespace: krateo-system
```

The operatorFocusNamespace must be set to the namespace of the [finops-operator-focus](https://github.com/krateoplatformops/finops-operator-focus).

## Configuration
Check the Krateo FinOps Operators for additional information on how to compile the `ScraperConfig`:
- [finops-operator-focus](https://github.com/krateoplatformops/finops-operator-focus)
- [finops-operator-exporter](https://github.com/krateoplatformops/finops-operator-exporter)
- [finops-operator-scraper](https://github.com/krateoplatformops/finops-operator-scraper)

## How to Install
Install the Blueprint with Krateo Composable Operations:

```sh
cat <<EOF | kubectl apply -f -
apiVersion: core.krateo.io/v1alpha1
kind: CompositionDefinition
metadata:
  annotations:
     "krateo.io/connector-verbose": "true"
  name: azure-pricing-finops
  namespace: azure-pricing-system
spec:
  chart:
    repo: azure-pricing-finops
    url: https://marketplace.krateo.io
    version: 1.0.0
EOF
```
