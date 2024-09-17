---
title: "Manage TLS certificates"
date: 2024-06-25
tags: ["Tyk Operator", "Kubernetes", "certificates", "TLS"]
description: "Using Tyk Operator to manage TLS certificates in Kubernetes environment"
---

Tyk Operator is designed to offer a seamless Kubernetes-native experience by managing TLS certificates stored within Kubernetes for your API needs. Traditionally, to use a certificate (e.g., as a client certificate, domain certificate, or certificate for accessing an upstream service), you would need to manually upload the certificate to Tyk and then reference it using a 'Certificate ID' in your API definitions. This process can become cumbersome, especially in a Kubernetes environment where certificates are often managed as secrets and may rotate frequently.

To address this challenge, Tyk Operator allows you to directly reference certificates stored as Kubernetes secrets within your custom resource definitions (CRDs). This reduces operational overhead, minimizes the risk of API downtime due to certificate mismatches, and provides a more intuitive experience for API developers.

### Benefits of Managing Certificates with Tyk Operator
- **Reduced operational overhead**: Automates the process of updating certificates when they rotate.
- **Minimized risk of API downtime**: Ensures that APIs continue to function smoothly, even when certificates are updated.
- **Improved developer experience**: Removes the need for API developers to manage certificate IDs manually.

## Managing client certificates in Tyk OAS
For Tyk OAS APIs, client certificates are managed using the `TykOasApiDefinition` CRD. You can reference Kubernetes secrets that store client certificates in your API definitions.

Example of Referencing Client Certificates in Tyk OAS

```yaml
# Secret is not created in this manifest.
# Please store client certificate in k8s TLS secret `tls-cert`.

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
  clientCertificate: 
      enabled: true
      allowlist: [tls-cert]
```

In this example, the `clientCertificate` section allows you to enable client certificate management and specify a list of Kubernetes secrets (`tls-cert`) that store allowed client certificates.

## Managing custom domain certificates in Tyk OAS

You can also manage custom domain certificates using Kubernetes secrets in Tyk OAS. This is particularly useful if you need to use a specific TLS certificate for a custom domain.

Example of Defining Custom Domain Certificates

```yaml
# Secret is not created in this manifest.
# Please store custom domain certificate in a kubernetes TLS secret `custom-domain-secret`.
apiVersion: v1
data:
  test_oas.json: |-
    {
      "info": {
        "title": "Petstore with custom domain",
        "version": "1.0.0"
      },
      "openapi": "3.0.3",
      "components": {},
      "paths": {},
      "x-tyk-api-gateway": {
        "info": {
          "name": "Petstore with custom domain",
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
  name: petstore-with-customdomain
spec:
  tykOAS:
    configmapRef:
      name: cm
      namespace: default
      keyName: test_oas.json
  customDomain:
    enabled: true
    name: "buraksekili.dev"
    certificatesRef:
      - custom-domain-secret
```

This example shows how to enable a custom domain (`buraksekili.dev`) with a TLS certificate stored in a Kubernetes secret (`custom-domain-secret`).

## Implementing public keys pinning in Tyk OAS

Tyk Operator also supports certificate pinning, allowing you to secure your API by pinning a public key to a specific domain.

Example of public keys pinning

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: cm
  namespace: default
data:
  test_oas.json: |-
    {
      "info": {
        "title": "httpbin with certificate pinning",
        "version": "1.0.0"
      },
      "openapi": "3.0.3",
      "components": {},
      "paths": {},
      "x-tyk-api-gateway": {
        "info": {
          "name": "httpbin with certificate pinning",
          "state": {
            "active": true
          }
        },
        "upstream": {
          "url": "https://httpbin.org/"
        },
        "server": {
          "listenPath": {
            "value": "/httpbin/",
            "strip": true
          }
        }
      }
    }
---
apiVersion: v1
kind: Secret
metadata:
  name: domain-secret
type: kubernetes.io/tls # The secret needs to be a type of kubernetes.io/tls
data:
  tls.crt: <PUBLIC_KEY>
  tls.key: ""
---
apiVersion: tyk.tyk.io/v1alpha1
kind: TykOasApiDefinition
metadata:
  name: "oas-pinned-public-keys"
spec:
  tykOAS:
    configmapRef:
      keyName: test_oas.json
      name: cm
  certificatePinning:
    enabled: true
    domainToPublicKeysMapping:
      - domain: "httpbin.org"
        publicKeyRefs:
          - domain-secret
```

This example demonstrates how to enable certificate pinning for the domain `httpbin.org` using a public key stored in a Kubernetes secret (`domain-secret`).

## Managing client certificates in Tyk Classic
In Tyk Classic, client certificates can be managed using the `ApiDefinition` CRD. See [Client mTLS]({{<ref "product-stack/tyk-operator/advanced-configurations/client-authentication#client-mtls">}}) for an example.

## Implementing public keys pinning in Tyk Classic
In Tyk Classic, client certificates can be managed using the `ApiDefinition` CRD. See [Certificate pinning]({{<ref "security/certificate-pinning#define-via-tyk-operator">}}) for an example.

## Implementing mTLS with upstream server in Tyk Classic
In Tyk Classic, upstream mTLS can be managed using the `ApiDefinition` CRD. See [Upstream mTLS via Operator]({{<ref "basic-config-and-security/security/mutual-tls/upstream-mtls#via-tyk-operator">}}) for an example.

## Conclusion
By integrating Kubernetes secrets management with Tyk Operator, you can simplify the process of managing TLS certificates for your APIs, enhance security, and provide a more native Kubernetes experience. Use the examples provided to set up your API configurations with client certificates, custom domain certificates, or certificate pinning, all managed efficiently within your Kubernetes environment.