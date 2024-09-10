---
date: 2017-03-24T16:39:31Z
title: Sample Tyk OAS API
tags: ["Tyk Operator", "Sample", "Kubernetes"]
description: "Tyk Operator OAS manifest example"
---

In addition to following a step-by-step approach using the Tyk Operator, you can also use Kubernetes manifest files to define your API configurations and apply them directly with `kubectl`. This method allows for easy version control, repeatability, and integration with CI/CD pipelines.

This example shows the minimum resources and fields required to define a Tyk OAS API using Tyk Operator. 

```yaml
apiVersion: v1
data:
  test_oas.json: |-
    {
        "info": {
          "title": "Petstore",
          "version": "1.0.0"
        },
        "openapi": "3.0.3",
        "components": {},
        "paths": {},
        "x-tyk-api-gateway": {
          "info": {
            "name": "Petstore",
            "state": {
              "active": true
            }
          },
          "upstream": {
            "url": "https://petstore.swagger.io/v2"
          },
          "server": {
            "listenPath": {
              "value": "/petstore/",
              "strip": true
            }
          }
        }
      }
kind: ConfigMap
metadata:
  name: cm
  namespace: default
---
apiVersion: tyk.tyk.io/v1alpha1
kind: TykOasApiDefinition
metadata:
  name: petstore
spec:
  tykOAS:
    configmapRef:
      name: cm
      namespace: default
      keyName: test_oas.json
```

To apply it, simply save the manifest into a file (e.g., `tyk-oas-api.yaml`) and use `kubectl apply -f tyk-oas-api.yaml` to create the required resources in your Kubernetes cluster. This command will create the necessary ConfigMap and TykOasApiDefinition resources in the `default` namespace.