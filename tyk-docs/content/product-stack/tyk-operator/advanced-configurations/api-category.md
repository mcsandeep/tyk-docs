---
title: "API Category"
date: 2024-09-12
tags: ["Tyk", "Kubernetes", "API Category"]
description: "This documentation provides a comprehensive guide on configuring API category within the ApiDefinition Custom Resource Definition (CRD) using Tyk Operator."
keywords: ["Tyk Operator", "Kubernetes", "API Category"]
---

## Overview

## Tyk OAS API

## Tyk Classic API

For a Tyk Classic API, you can specify the category name using the `spec.name` field with a `#` qualifier. This will categorize the API in the Tyk Dashboard.

Example

```yaml  {linenos=true, linenostart=1, hl_lines=["6-6"]}
apiVersion: tyk.tyk.io/v1alpha1
kind: ApiDefinition
metadata:
  name: categorized-api
spec:
  name: "my-classic-api #global #staging"
  use_keyless: true
  protocol: http
  active: true
  proxy:
    target_url: http://categorized.example.com
    listen_path: /categorized
    strip_listen_path: true
```