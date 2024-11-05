---
description: Tyk Tools that help with automating deployment and API Management operations
linkTitle: Automation Tools
tags: ["Tyk API Management", "Open Source", "Self-Managed", "Tyk Cloud", "API Gateway"]
title: Automation Tools
---
## Introduction
Managing APIs across multiple environments can quickly become complex. Updating and overseeing multiple configurations, security policies, and deployments requires a significant amount of effort without the right tools. Tyk’s suite of automation tools simplifies this process by enabling automated control over API management tasks, helping teams ensure reliability, reduce manual errors, and maintain consistency across deployments.

In this guide, we’ll walk through the primary tools for automating API management with Tyk, including:

* **Tyk Operator for Kubernetes**: Automate API deployments within Kubernetes environments.
* **Tyk Sync**: Sync configurations across environments for consistent API management.
* **Programmatic API Management**: Use Tyk’s APIs to automate tasks such as token management and policy updates.
* **Multi-Environment Deployments**: Simplify deployments across various staging, testing, and production environments.


## Prerequisites

Before diving into lifecycle automations with Tyk, ensure you have the following:

- **A Tyk installation** (Self-Managed or Cloud)
  - If you don't have Tyk installed, follow our [installation guide](/tyk-on-premises/installation/)
  - For Tyk Cloud, sign up [here](https://tyk.io/sign-up/)
  - Tyk Operator license key. Starting from Tyk Operator v1.0, a valid [license key](#obtain-a-license-key) is required.
- **Access to a Kubernetes cluster v1.19+** (for Tyk Operator sections)
  - If you're new to Kubernetes, check out the official [Kubernetes documentation](https://kubernetes.io/docs/setup/)

- **Helm 3+** (for installing Tyk Operator)
  - If you don't have Helm installed, follow the [official Helm installation guide](https://helm.sh/docs/intro/install/)
  - Verify your installation by running `helm version` in your terminal

- **Tyk Dashboard v3+ access and API credentials** (for Tyk Sync setup)
  - Learn how to set up the Tyk Dashboard [here](/tyk-dashboard/)
  - For API credentials, see our guide on [creating API tokens](/tyk-dashboard/managing-users/#create-an-api-token)

- **Basic knowledge of Kubernetes, YAML, and API concepts** (important for Tyk Operator and Tyk Sync)
  - For Kubernetes, visit the [official tutorials](https://kubernetes.io/docs/tutorials/)
  - For YAML, check out this [YAML tutorial](https://yaml.org/spec/1.2/spec.html)
  - For API concepts, review our [API management basics](/getting-started/key-concepts/)


If you're missing any of these prerequisites, please follow the provided links to set up the necessary components before proceeding with the lifecycle automation steps.


## Automate API Management in Kubernetes Environments

Using Tyk Operator within Kubernetes allows you to manage API lifecycles declaratively. This section provides instructions for setting up and configuring the Tyk Operator to automate API creation, updates, and security in Kubernetes clusters, ensuring your APIs align with Kubernetes management practices.


### What is Tyk Operator?
If you’re using Kubernetes, or if you’re building an API that operates within a Kubernetes environment, the Tyk Operator is a powerful tool for automating the API lifecycle.

Tyk Operator integrates directly with Kubernetes, allowing you to define and manage APIs as code. This means you can deploy, update, and secure APIs using the same declarative configuration approach Kubernetes uses for other application components. 

{{< img src="/img/operator/tyk-operator.svg" alt="Tyk Operator" width="600" >}}


### Key Concepts

#### Custom Resources in Tyk

In Kubernetes, a [Custom Resource (CR)](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) is an extension of the Kubernetes API that allows you to introduce custom objects in your cluster. Custom Resources enable you to define and manage custom configurations and settings specific to your applications, making Kubernetes highly extensible. These custom objects are defined using Custom Resource Definitions (CRDs), which specify the schema and structure of the resource.

Tyk Operator manages two custom resources to help users create and maintain their API configurations:

TykOasApiDefinition: Available from Tyk Operator v1.0. It represents a [Tyk OAS API configuration]({{<ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc">}}). Tyk OAS API is based on the OpenAPI specification (OAS) and is the recommended format for standard HTTP APIs. Tyk Operator supports all [Tyk OAS API feature]({{<ref "getting-started/using-oas-definitions/oas-reference">}}) as they become available on the Gateway.

ApiDefinition: Available on all versions of Tyk Operator. It represents a [Tyk Classic API configuration]({{<ref "tyk-gateway-api/api-definition-objects">}}). Tyk Classic API is the traditional format used for defining all APIs in Tyk, and now the recommended format for non-HTTP APIs such as TCP, GraphQL, and Universal Data Graph (UDG). Tyk Operator supports the major features of Tyk Classic API and the feature support details can be tracked at our [reference]({{<ref "product-stack/tyk-operator/reference/api-definition">}}) page.

These custom resources enable users to leverage Kubernetes' declarative configuration management to define, modify, and version their APIs, seamlessly integrating with other Kubernetes-based workflows and tools.

##### API Definition and Security Policy

The following custom resources can be used to configure APIs and policies at [Tyk Gateway]({{<ref "tyk-oss-gateway">}}) or [Tyk Dashboard]({{<ref "tyk-dashboard">}}).

| Kind               | Group       | Version   | Description                                                                                       |
|--------------------|-------------|-----------|---------------------------------------------------------------------------------------------------|
| TykOasApiDefinition| tyk.tyk.io  | v1alpha1  | Defines configuration of [Tyk OAS API Definition object]({{<ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc">}})                                 |
| ApiDefinition      | tyk.tyk.io  | v1alpha1  | Defines configuration of [Tyk Classic API Definition object]({{<ref "tyk-gateway-api/api-definition-objects">}})                                 |
| SecurityPolicy     | tyk.tyk.io  | v1alpha1  | Defines configuration of [security policies]({{<ref "getting-started/key-concepts/what-is-a-security-policy">}}). Operator supports linking ApiDefinition custom resources in SecurityPolicy's access list so that API IDs do not need to be hardcoded in the resource manifest.        |
| SubGraph           | tyk.tyk.io  | v1alpha1  | Defines a [GraphQL federation subgraph]({{<ref "getting-started/key-concepts/graphql-federation#subgraphs-and-supergraphs">}}).                                           |
| SuperGraph         | tyk.tyk.io  | v1alpha1  | Defines a [GraphQL federation supergraph]({{<ref "getting-started/key-concepts/graphql-federation#subgraphs-and-supergraphs">}}).                                        |
| OperatorContext    | tyk.tyk.io  | v1alpha1  | Manages the context in which the Tyk Operator operates, affecting its overall behavior and environment. See [Operator Context]({{<ref "product-stack/tyk-operator/key-concepts/operator-context">}}) for details. |

##### Tyk Classic Developer Portal

The following custom resources can be used to configure [Tyk Classic Developer Portal]({{<ref "tyk-developer-portal/tyk-portal-classic">}}).

| Kind               | Group       | Version   | Description                                                                                       |
|--------------------|-------------|-----------|---------------------------------------------------------------------------------------------------|
| APIDescription     | tyk.tyk.io  | v1alpha1  | Configures [Portal Documentation]({{<ref "tyk-apis/tyk-portal-api/portal-documentation">}}). |
| PortalAPICatalogue | tyk.tyk.io  | v1alpha1  | Configures [Portal API Catalogue]({{<ref "getting-started/key-concepts/api-catalogue">}}). |
| PortalConfig       | tyk.tyk.io  | v1alpha1  | Configures [Portal Configuration]({{<ref "tyk-apis/tyk-portal-api/portal-configuration">}}). |

#### CRD Versioning

Tyk follows standard practices for naming and versioning custom resources as outlined by the Kubernetes Custom Resource Definition [versioning guidelines](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/). Although we are currently on the `v1alpha1` version, no breaking changes will be introduced to existing Custom Resources without a version bump. This means that any significant changes or updates that could impact existing resources will result in a new version (e.g., `v1beta1` or `v1`) and Operator will continue supporting all CRD versions for a reasonable time before deprecating an older version. This ensures a smooth transition and compatibility, allowing you to upgrade without disrupting your current configurations and workflows.

For more details on Kubernetes CRD versioning practices, refer to the Kubernetes Custom Resource Definition [Versioning documentation](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definition-versioning/).


#### Operator User
Tyk Operator is a Kubernetes Controller that manages Tyk Custom Resources (CRs) such as API Definitions and Security Policies. Developers define these resources as [Custom Resource (CRs)]({{<ref "product-stack/tyk-operator/key-concepts/custom-resources">}}), and Tyk Operator ensures that the desired state is reconciled with the Tyk Gateway or Dashboard. This involves creating, updating, or deleting API configurations until the target state matches the desired state.

For the Tyk Dashboard, Tyk Operator functions as a system user, bound by Organization and RBAC rules.

During start up, Tyk Operator looks for these keys from `tyk-operator-conf` secret or from the environment variables (listed in the table below).

| Key or Environment Variable | Description  |
|:-----|:-------------|
| `TYK_MODE` | "ce" for OSS or "pro" for licensed users |
| `TYK_URL` | URL of Tyk Gateway or Dashboard API |
| `TYK_ORG` | Organization ID of Operator user |
| `TYK_AUTH` | API key of Operator user |

These would be the default credentials Tyk Operator uses to connect to Tyk.


#### Multi-tenancy in Tyk

Tyk Dashboard is multi-tenant capable, which means you can use a single Tyk Dashboard instance to host separate [organizations]({{< ref "basic-config-and-security/security/dashboard/organisations">}}) for each team or department. Each organization is a completely isolated unit with its own:

- API Definitions
- API Keys
- Users
- Developers
- Domain
- Tyk Classic Portal

This structure is ideal for businesses with a complex hierarchy, where distinct departments operate independently but within the same overall infrastructure.

{{< img src="/img/operator/tyk-organisations.svg" alt="Multi-tenancy in Tyk Dashboard" width="600" >}}

##### Define OperatorContext for Multi-Tenant API Management

The `OperatorContext` in Tyk Operator allows you to create isolated management environments by defining specific access parameters for different teams or departments within a shared Tyk Operator instance. It helps you specify:

- The Tyk Dashboard with which the Operator interacts
- The organization under which API management occurs
- The user identity utilized for requests
- The environment in which the Operator operates

By setting different `OperatorContext` configurations, you can define unique access and management contexts for different teams. These contexts can then be referenced directly in your `ApiDefinition` or `SecurityPolicy` custom resource definitions (CRDs) using the `contextRef` field, enabling precise control over API configurations.

##### Example Scenarios Using OperatorContext

1. **No OperatorContext Defined**
   - If no `OperatorContext` is defined, Tyk Operator defaults to using credentials from the `tyk-operator-conf` secret or from environment variables. This means all API management actions are performed under the system’s default user credentials, with no specific contextual isolation.

2. **OperatorContext Defined but Not Referenced**
   - When an `OperatorContext` is defined but not referenced in an API configuration, Tyk Operator continues to use the default credentials from `tyk-operator-conf`. The specified `OperatorContext` is ignored, resulting in API operations being managed under default credentials.

3. **OperatorContext Defined and Referenced**
   - If a specific `OperatorContext` is both defined and referenced in an API or policy, Tyk Operator utilizes the credentials and parameters from the referenced `OperatorContext` to perform API operations. This allows each API or policy to be managed with isolated configurations, enabling team-based or department-specific API management within the same Kubernetes cluster.

Using `OperatorContext` offers flexibility for multi-tenancy, helping organizations manage and isolate API configurations based on their specific team or departmental needs.

{{< img src="/img/operator/tyk-operator-context.svg" alt="Multi-tenancy in Kubernetes Tyk Operator" width="600" >}}


### Install and Configure Tyk Operator

We assume you have already installed Tyk. If you don’t have it, check out [Tyk
Cloud]({{<ref "deployment-and-operations/tyk-cloud-platform/quick-start">}}) or [Tyk Self
Managed]({{<ref "getting-started/installation">}}) page. [Tyk Helm
Chart]({{<ref "product-stack/tyk-charts/overview">}}) is the preferred (and easiest) way to install Tyk on Kubernetes.

In order for policy ID matching to work correctly, Dashboard must have `allow_explicit_policy_id` and
`enable_duplicate_slugs` set to `true` and Gateway must have `policies.allow_explicit_policy_id` set to `true`.

Tyk Operator needs a [user credential]({{<ref "product-stack/tyk-operator/key-concepts/operator-user">}}) to connect with
Tyk Dashboard. The Operator user should have write access to the resources it is going to manage, e.g. APIs, Certificates,
Policies, and Portal. It is the recommended practice to turn off write access for other users for the above resources. See
[Using Tyk Operator to enable GitOps with Tyk]({{< ref "getting-started/key-concepts/gitops-with-tyk" >}}) about
maintaining a single source of truth for your API configurations.

#### Install cert-manager

Tyk Operator uses cert-manager to provision certificates for the webhook server. If you don't have cert-manager
installed, you can follow this command to install it:

```console
$ kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.8.0/cert-manager.yaml
```

Since Tyk Operator supports Kubernetes v1.19+, the minimum cert-manager version you can use is v1.8. If you run into the
cert-manager related errors, please ensure that the desired version of Kubernetes version works with the chosen version
of cert-manager by checking [supported releases page](https://cert-manager.io/docs/installation/supported-releases/) and
[cert-manager documentation](https://cert-manager.io/docs/installation/supported-releases/).

Please wait for the cert-manager to become available before continuing with the next step.



#### Option 1: Install Tyk Operator via Tyk's Umbrella Helm Charts

If you are using [Tyk Stack]({{<ref "product-stack/tyk-charts/tyk-stack-chart">}}), [Tyk Control
Plane]({{<ref "product-stack/tyk-charts/tyk-control-plane-chart">}}), or [Tyk Open
Source Chart]({{<ref "product-stack/tyk-charts/tyk-oss-chart">}}), you can install Tyk Operator alongside other Tyk
components by setting value `global.components.operator` to `true`.

Starting from Tyk Operator v1.0, a license key is required to use the Tyk Operator. You can provide it while installing
Tyk Stack, Tyk Control Plane or Tyk OSS helm chart by setting `global.license.operator` field. You can also set license
key via a Kubernetes secret using `global.secrets.useSecretName` field. The secret should contain a key called
`OperatorLicense`

#### Option 2: Install Tyk Operator via stand-alone Helm Chart

If you prefer to install Tyk Operator separately, follow this section to install Tyk Operator using Helm.

##### Configure Tyk Operator via environment variable or tyk-operator-conf secret

Tyk Operator configurations can be set using `envVars` field of helm chart. See the table below for a list of expected
environment variable names and example values.

```yaml
envVars:
  - name: TYK_OPERATOR_LICENSEKEY
    value: "{YOUR_LICENSE_KEY}"
  - name: TYK_MODE
    value: "pro"
  - name: TYK_URL
    value: "http://dashboard-svc-tyk-tyk-dashboard.tyk.svc:3000"
  - name: TYK_AUTH
    value: "2d095c2155774fe36d77e5cbe3ac963b"
  - name: TYK_ORG
    value: "5e9d9544a1dcd60001d0ed20"
```

It can also be set via a Kubernetes secret. The default K8s secret name is `tyk-operator-conf`. If you want to use
another name, configure it through Helm Chart [envFrom](#helm-configurations) value.

The Kubernetes secret or envVars field should set the following keys:

{{< tabs_start >}}

{{< tab_start "Licensed mode (Self-managed or Tyk Cloud)" >}}

| Key                          | Mandatory | Example Value                                       | Description                                                                                                                  |
| :--------------------------- | :-------- | :-------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------- |
| TYK_OPERATOR_LICENSEKEY      | Yes       | <JWT_ENCODED_LICENSE_KEY>                           | Tyk Operator license key                                                                                                     |
| TYK_MODE                     | Yes       | pro                                                 | “ce” for Tyk Open Source mode, “pro” for Tyk licensed mode.                                                                  |
| TYK_URL                      | Yes       | http://dashboard-svc-tyk-tyk-dashboard.tyk.svc:3000 | Management URL of Tyk Gateway (Open Source) or Tyk Dashboard                                                                 |
| TYK_AUTH                     | Yes       | 2d095c2155774fe36d77e5cbe3ac963b                    | Operator user API key.                                                                                                       |
| TYK_ORG                      | Yes       | 5e9d9544a1dcd60001d0ed20                            | Operator user ORG ID.                                                                                                        |
| TYK_TLS_INSECURE_SKIP_VERIFY | No        | true                                                | Set to `“true”` if the Tyk URL is HTTPS and has a self-signed certificate. If it isn't set, the default value is `false`.    |
| WATCH_NAMESPACE              | No        | foo,bar                                             | Comma separated list of namespaces for Operator to operate on. The default is to operate on all namespaces if not specified. |
| WATCH_INGRESS_CLASS          | No        | customclass                                         | Define the ingress class Tyk Operator should watch. Default is `tyk`                                                         |
| TYK_HTTPS_INGRESS_PORT       | No        | 8443                                                | Define the ListenPort for HTTPS ingress. Default is `8443`.                                                                  |
| TYK_HTTP_INGRESS_PORT        | No        | 8080                                                | Define the ListenPort for HTTP ingress. Default is `8080`.                                                                   |

{{< tab_end >}}

{{< tab_start "Open Source" >}}

**Note**: From Tyk Operator v1.0, although Tyk Operator is compatible with the Open Source Tyk Gateway, a valid license
key is required for running Tyk Operator.

| Key                          | Mandatory | Example Value                                      | Description                                                                                                                  |
| :--------------------------- | :-------- | :------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------- |
| TYK_OPERATOR_LICENSEKEY      | Yes       | <JWT_ENCODED_LICENSE_KEY>                          | Tyk Operator license key                                                                                                     |
| TYK_MODE                     | Yes       | ce                                                 | “ce” for Tyk Open Source mode, “pro” for Tyk licensed mode.                                                                  |
| TYK_URL                      | Yes       | http://gateway-svc-tyk-ce-tyk-gateway.tyk.svc:8080 | Management URL of Tyk Gateway (Open Source) or Tyk Dashboard                                                                 |
| TYK_AUTH                     | Yes       | myapisecret                                        | Operator user API key.                                                                                                       |
| TYK_ORG                      | Yes       | myorgid                                            | Operator user ORG ID.                                                                                                        |
| TYK_TLS_INSECURE_SKIP_VERIFY | No        | true                                               | Set to `“true”` if the Tyk URL is HTTPS and has a self-signed certificate. If it isn't set, the default value is `false`.    |
| WATCH_NAMESPACE              | No        | foo,bar                                            | Comma separated list of namespaces for Operator to operate on. The default is to operate on all namespaces if not specified. |
| WATCH_INGRESS_CLASS          | No        | customclass                                        | Define the ingress class Tyk Operator should watch. Default is `tyk`                                                         |
| TYK_HTTPS_INGRESS_PORT       | No        | 8443                                               | Define the ListenPort for HTTPS ingress. Default is `8443`.                                                                  |
| TYK_HTTP_INGRESS_PORT        | No        | 8080                                               | Define the ListenPort for HTTP ingress. Default is `8080`.                                                                   |

{{< tab_end >}}

{{< tabs_end >}}

**Connect to Tyk Gateway or Dashboard**

If you install Tyk using Helm Chart, `tyk-operator-conf` will have been created with the following keys:
`TYK_OPERATOR_LICENSEKEY, TYK_AUTH, TYK_MODE, TYK_ORG`, and `TYK_URL` by default. If you didn't use Helm Chart for
installation, please prepare `tyk-operator-conf` secret yourself using the commands below:

```console
$ kubectl create namespace tyk-operator-system

$ kubectl create secret -n tyk-operator-system generic tyk-operator-conf \
  --from-literal "TYK_OPERATOR_LICENSEKEY=${TYK_OPERATOR_LICENSEKEY}" \
  --from-literal "TYK_AUTH=${TYK_AUTH}" \
  --from-literal "TYK_ORG=${TYK_ORG}" \
  --from-literal "TYK_MODE=${TYK_MODE}" \
  --from-literal "TYK_URL=${TYK_URL}"
```

{{< note success >}} **Note**

User API key and Organization ID can be found under "Add / Edit User" page within Tyk Dashboard. `TYK_AUTH` corresponds
to <b>Tyk Dashboard API Access Credentials</b>. `TYK_ORG` corresponds to <b>Organization ID</b>. {{< /note >}}

{{< note success >}} **Note**

If the credentials embedded in the `tyk-operator-conf` are ever changed or updated, the tyk-operator-controller-manager
pod must be restarted to pick up these changes. {{< /note >}}

**Watch Namespaces**

Tyk Operator is installed with cluster permissions. However, you can optionally control which namespaces it watches by
setting the `WATCH_NAMESPACE` through `tyk-operator-conf` secret or the environment variable to a comma separated list
of k8s namespaces. For example:

- `WATCH_NAMESPACE=""` will watch for resources across the entire cluster.
- `WATCH_NAMESPACE="foo"` will watch for resources in the `foo` namespace.
- `WATCH_NAMESPACE="foo,bar"` will watch for resources in the `foo` and `bar` namespace.

**Watch custom ingress class**

You can configure [Tyk Operator as Ingress Controller]({{<ref "product-stack/tyk-operator/tyk-ingress-controller">}}) so
that [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) resources can be managed by Tyk as
APIs. By default, Tyk Operator looks for the value `tyk` in Ingress resources `kubernetes.io/ingress.class` annotation
and will ignore all other ingress classes. If you want to override this default behavior, you may do so by setting
[WATCH_INGRESS_CLASS](#step-1-create-tyk-operator-conf-secret) through `tyk-operator-conf` or the environment variable.

##### Install Tyk Operator and Custom Resource Definitions (CRDs)

You can install CRDs and Tyk Operator using the stand-alone Helm Chart by running the following command:

```console
$ helm repo add tyk-helm https://helm.tyk.io/public/helm/charts/
$ helm repo update

$ helm install tyk-operator tyk-helm/tyk-operator -n tyk-operator-system
```

This process will deploy Tyk Operator and its required Custom Resource Definitions (CRDs) into your Kubernetes cluster
in `tyk-operator-system` namespace.

**Helm configurations**

| Key                                         | Type   | Default                                |
| ------------------------------------------- | ------ | -------------------------------------- |
| envFrom[0].secretRef.name                   | string | `"tyk-operator-conf"`                  |
| envVars[0].name                             | string | `"TYK_OPERATOR_LICENSEKEY"`            |
| envVars[0].value                            | string | `"{OPERATOR_LICENSEKEY}"`              |
| envVars[1].name                             | string | `"TYK_HTTPS_INGRESS_PORT"`             |
| envVars[1].value                            | string | `"8443"`                               |
| envVars[2].name                             | string | `"TYK_HTTP_INGRESS_PORT"`              |
| envVars[2].value                            | string | `"8080"`                               |
| extraVolumeMounts                           | list   | `[]`                                   |
| extraVolumes                                | list   | `[]`                                   |
| fullnameOverride                            | string | `""`                                   |
| healthProbePort                             | int    | `8081`                                 |
| hostNetwork                                 | bool   | `false`                                |
| image.pullPolicy                            | string | `"IfNotPresent"`                       |
| image.repository                            | string | `"tykio/tyk-operator"`                 |
| image.tag                                   | string | `"v1.0.0"`                             |
| imagePullSecrets                            | list   | `[]`                                   |
| metricsPort                                 | int    | `8080`                                 |
| nameOverride                                | string | `""`                                   |
| nodeSelector                                | object | `{}`                                   |
| podAnnotations                              | object | `{}`                                   |
| podSecurityContext.allowPrivilegeEscalation | bool   | `false`                                |
| rbac.image.pullPolicy                       | string | `"IfNotPresent"`                       |
| rbac.image.repository                       | string | `"gcr.io/kubebuilder/kube-rbac-proxy"` |
| rbac.image.tag                              | string | `"v0.8.0"`                             |
| rbac.port                                   | int    | `8443`                                 |
| rbac.resources                              | object | `{}`                                   |
| replicaCount                                | int    | `1`                                    |
| resources                                   | object | `{}`                                   |
| serviceMonitor                              | bool   | `false`                                |
| webhookPort                                 | int    | `9443`                                 |

#### Upgrading Tyk Operator

##### Upgrading from v0.x to v1.0+

Starting from Tyk Operator v1.0, a valid license key is required for the Tyk Operator to function. If Tyk Operator is
upgraded from v0.x versions to one of v1.0+ versions, Tyk Operator needs a valid license key that needs to be provided
during upgrade process. This section describes how to set Tyk Operator license key to make sure Tyk Operator continues
functioning.

{{< note >}} When upgrading Tyk Operator between licensed versions, follow the standard upgrade procedure described
under [Upgrading
Tyk Operator]({{<ref "tyk-stack/tyk-operator/installing-tyk-operator#upgrading-tyk-operator-and-crds">}}) section. It is
important to note that this process differs if transitioning from an unlicensed to a licensed version. {{< /note >}}

To provide the license key for Tyk Operator, Kubernetes secret used to configure Tyk Operator (typically named
tyk-operator-conf as described above) requires an additional field called `TYK_OPERATOR_LICENSEKEY`. Populate this field
with your Tyk Operator license key.

To configure the license key:

1. Locate the Kubernetes Secret used to configure Tyk Operator (typically named `tyk-operator-conf`).
2. Add a new field called `TYK_OPERATOR_LICENSEKEY` to this Secret.
3. Set the value of `TYK_OPERATOR_LICENSEKEY` to your Tyk Operator license key.

After updating the Kubernetes secret with this field, proceed with the standard upgrade process outlined below.

##### Upgrading Tyk Operator and CRDs

You can upgrade Tyk Operator through Helm Chart by running the following command:

```console
$ helm upgrade -n tyk-operator-system tyk-operator tyk-helm/tyk-operator --wait
```

[Helm does not upgrade or delete CRDs](https://helm.sh/docs/chart_best_practices/custom_resource_definitions/#some-caveats-and-explanations)
when performing an upgrade. Because of this restriction, an additional step is required when upgrading Tyk Operator with
Helm.

```console
$ kubectl apply -f https://raw.githubusercontent.com/TykTechnologies/tyk-charts/refs/heads/main/tyk-operator-crds/crd-$TYK_OPERATOR_VERSION.yaml
```

{{< note success >}} **Note**
Replace $TYK_OPERATOR_VERSION with the image tag corresponding to the Tyk Operator version to which
the Custom Resource Definitions (CRDs) belong. For example, to install CRDs compatible with Tyk Operator v1.0.0, set $TYK_OPERATOR_VERSION to v1.0.0. 
 {{< /note >}}


#### Uninstalling Tyk Operator

To uninstall Tyk Operator, you need to run the following command:

```console
$ helm delete tyk-operator -n tyk-operator-system
```

### Create an OAS API
#### Prepare the Tyk OAS API Definition
First, you need to have a complete Tyk OAS API definition file ready. This file will contain all the necessary configuration details for your API in OpenAPI Specification (OAS) format.

Here is an example of what the Tyk OAS API definition might look like. Note that Tyk extension `x-tyk-api-gateway` section should be present.

```json {hl_lines=["9-25"],linenos=true}
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
```

Save this API definition file (e.g., `oas-api-definition.json`) locally.

{{< note success >}}
**Tips**  

You can create and configure your API easily using Tyk Dashboard in a developer environment, and then obtain the OAS API definition following these instructions:

1. Open the Tyk Dashboard
2. Navigate to the API you want to manage with the Tyk Operator
3. Click on the "Actions" menu button and select "View Raw Definition."
4. This will display the raw OAS API definition of your API, which you can then copy and save locally.
{{< /note >}}

#### Create a ConfigMap for the Tyk OAS API Definition

You need to create a [ConfigMap](https://kubernetes.io/docs/concepts/configuration/configmap/#configmap-object) in Kubernetes to store your Tyk OAS API definition. The Tyk Operator will reference this ConfigMap to retrieve the API configuration.

To create the ConfigMap, run the following command:

```sh
kubectl create configmap tyk-oas-api-config --from-file=oas-api-definition.json -n tyk
```

This command creates a ConfigMap named `tyk-oas-api-config` in the `tyk` namespace (replace `tyk` with your actual namespace if different).

{{< note success >}}
**Notes**

There is inherent size limit to a ConfigMap. The data stored in a ConfigMap cannot exceed 1 MiB. In case your OpenAPI document exceeds this size limit, it is recommended to split your API into smaller sub-APIs for easy management. For details, please consult [Best Practices for Describing Large APIs](https://learn.openapis.org/best-practices.html#describing-large-apis) from the OpenAPI initiative.
{{< /note >}}

{{< note success >}}
**Notes**

If you prefer to create ConfigMap with a manifest using `kubectl apply` command, you may get an error that the annotation metadata cannot exceed 256KB. It is because by using `kubectl apply`, `kubectl` automatically saves the whole configuration in the annotation [kubectl.kubernetes.io/last-applied-configuration](https://kubernetes.io/docs/reference/labels-annotations-taints/#kubectl-kubernetes-io-last-applied-configuration) for tracking changes. Your OAS API Definition may easily exceed the size limit of annotations (256KB). Therefore, `kubectl create` is used here to get around the problem.
{{< /note >}}

#### Create a TykOasApiDefinition Custom Resource

Now, create a `TykOasApiDefinition` resource to tell the Tyk Operator to use the OAS API definition stored in the ConfigMap.

Create a manifest file named `tyk-oas-api-definition.yaml` with the following content:

```yaml
apiVersion: tyk.tyk.io/v1alpha1
kind: TykOasApiDefinition
metadata:
  name: petstore
spec:
  tykOAS:
    configmapRef:
      name: tyk-oas-api-config   # Metadata name of the ConfigMap resource that stores the OAS API Definition
      namespace: tyk             # Metadata namespace of the ConfigMap resource
      keyName: oas-api-definition.json # Key for retrieving OAS API Definition from the ConfigMap
```

#### Apply the TykOasApiDefinition Manifest

Use `kubectl` to apply the `TykOasApiDefinition` manifest to your cluster:

```sh
kubectl apply -f tyk-oas-api-definition.yaml
```

This command creates a new `TykOasApiDefinition` resource in your cluster. The Tyk Operator will watch for this resource and configures Tyk Gateway or Tyk Dashboard with a new API using the provided OAS API definition.

#### Verify the Tyk OAS API Creation

To verify that the API has been successfully created, check the status of the TykOasApiDefinition resource:

```sh
kubectl get tykoasapidefinition petstore
```

You should see the status of the resource, which will indicate if the API creation was successful.

```bash
NAME       DOMAIN   LISTENPATH   PROXY.TARGETURL                  ENABLED   SYNCSTATUS   INGRESSTEMPLATE
petstore            /petstore/   https://petstore.swagger.io/v2   true      Successful
```

#### Test the Tyk OAS API
After the Tyk OAS API has been successfully created, you can test it by sending a request to the API endpoint defined in your OAS file.

For example, if your API endpoint is `/store/inventory"`, you can use `curl` or any API client to test it:

```sh
curl "TYK_GATEWAY_URL/petstore/store/inventory"
```

Replace TYK_GATEWAY_URL with a URL of Tyk Gateway.

#### Manage and Update the Tyk OAS API
To make any changes to your API configuration, update the OAS file in your ConfigMap and then re-apply the ConfigMap using `kubectl replace`:

```sh
kubectl create configmap tyk-oas-api-config --from-file=oas-api-definition.json -n tyk --dry-run=client -o yaml | kubectl replace -f -
```

The Tyk Operator will automatically detect the change and update the API in the Tyk Gateway.

{{< note success >}}
**Notes**

`kubectl replace` without `--save-config` option is used here instead of `kubectl apply` because we do not want to save the OAS API definition in its annotation. If you want to enable `--save-config` option or use `kubectl apply`, the OAS API definition size would be further limited to at most 262144 bytes.
{{< /note >}}

#### OAS API Examples
This example shows the minimum resources and fields required to define a Tyk OAS API using Tyk Operator. 

```yaml{hl_lines=["7-7", "41-44"],linenos=true}
apiVersion: v1
kind: ConfigMap
metadata:
  name: cm
  namespace: default
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

Here, a `ConfigMap` is created that contains the Tyk OAS API Definition with the `data` field with key `test_oas.json`. This is linked to from a `TykOasApiDefinition` resource via `spec.tykOAS.configmapRef`.

To apply it, simply save the manifest into a file (e.g., `tyk-oas-api.yaml`) and use `kubectl apply -f tyk-oas-api.yaml` to create the required resources in your Kubernetes cluster. This command will create the necessary ConfigMap and TykOasApiDefinition resources in the `default` namespace.



### Secure an OAS API
#### Update your Tyk OAS API Definition

First, you'll modify your existing Tyk OAS API Definition to include the API key authentication configuration.

When creating the OAS API, you stored your OAS definition in a file named `oas-api-definition.json` and created a ConfigMap named `tyk-oas-api-config` in the `tyk` namespace.

Modify your Tyk OAS API Definition `oas-api-definition.json` as follow. 

```json {hl_lines=["8-14","16-20","33-40"],linenos=true}
{
  "info": {
    "title": "Petstore protected",
    "version": "1.0.0"
  },
  "openapi": "3.0.3",
  "components": {
    "securitySchemes": {
      "petstore_auth": {
        "type": "apiKey",
        "name": "Authorization",
        "in": "header"
      }
    }
  },
  "security": [
    {
      "petstore_auth": []
    }
  ],
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
      "authentication": {
        "enabled": true,
        "securitySchemes": {
          "petstore_auth": {
            "enabled": true
          }
        }
      },
      "listenPath": {
        "value": "/petstore/",
        "strip": true
      }
    }
  }
}
```

In this example, we added the following sections to configure key authentication for this API. 

- `components.securitySchemes` defines the authentication method (in this case, `apiKey` in the header).
- `security`: Applies the authentication globally to all endpoints.
- `x-tyk-api-gateway.server.authentication`: Tyk-specific extension to enable the authentication scheme.

You can configure your API for any Tyk supported authentication method by following [Authentication with Tyk OAS]({{<ref "getting-started/key-concepts/authentication">}}) documentation.

Save your updated API definition in the same file, `oas-api-definition.json`.

#### Update the ConfigMap with the new Tyk OAS API Definition

Update the existing ConfigMap that contains your Tyk OAS API Definition with the following command:

```sh
kubectl create configmap tyk-oas-api-config --from-file=oas-api-definition.json -n tyk --dry-run=client -o yaml | kubectl replace -f -
```

This command updates the existing ConfigMap named `tyk-oas-api-config` in the `tyk` namespace (replace `tyk` with your actual namespace if different) with the new Tyk OAS API Definition stored in `oas-api-definition.json`.

Since a `TykOasApiDefinition` resource has been created with reference to this ConfigMap in the previous tutorial:

```yaml
apiVersion: tyk.tyk.io/v1alpha1
kind: TykOasApiDefinition
metadata:
  name: petstore
spec:
  tykOAS:
    configmapRef:
      name: tyk-oas-api-config   # Metadata name of the ConfigMap resource that stores the OAS API Definition
      namespace: tyk             # Metadata namespace of the ConfigMap resource
      keyName: oas-api-definition.json # Key for retrieving OAS API Definition from the ConfigMap
```

Any changes in the ConfigMap would be detected by Tyk Operator. Tyk Operator will then automatically reconcile the changes and update the API configuration at Tyk.

#### Verify the changes

Verify that the `TykOasApiDefinition` has been updated successfully:

```sh
kubectl get tykoasapidefinition petstore -o yaml
```

Look for the `latestTransaction` field in `status`:

```yaml
status:
  latestTransaction:
    status: Successful
    time: "2024-09-16T11:48:20Z"
```

The **Successful** status shows that Tyk Operator has reconciled the API with Tyk successfully. The last update time is shown in the `time` field.

#### Test the API Endpoint
Now, test your API endpoint to confirm that it requires an API key.

For example, if your API endpoint is `/store/inventory"`, you can use `curl` or any API client to test it:

```sh
curl -v "TYK_GATEWAY_URL/petstore/store/inventory"
```

Replace TYK_GATEWAY_URL with a URL of Tyk Gateway.

Request should fail with a `401 Unauthorized` response now as an API key is required for access. Your API has been secured by Tyk Gateway.

### Add a Security Policy To An OAS API
To further protect access to your APIs, you will want to add a security policy. 
Below, we take you through how to define the security policy but you can find [Security Policy Examples](#Security-Policy-Examples)

#### Define the Security Policy manifest

To create a security policy, you must define a Kubernetes manifest using the `SecurityPolicy` CRD. The following example illustrates how to configure a default policy for trial users for a Tyk Classic API named `httpbin` and a Tyk OAS API named `petstore`.

```yaml
apiVersion: tyk.tyk.io/v1alpha1
kind: SecurityPolicy
metadata:
  name: trial-policy                    # Unique Kubernetes name
spec:
  name: Default policy for trial users  # Descriptive name for the policy
  state: active
  active: true
  access_rights_array:
    - name: httpbin                     # Kubernetes name of referenced API
      namespace: default                # Kubernetes namespace of referenced API
      kind: ApiDefinition               # `ApiDefinition` (Default) or `TykOasApiDefinition`
      versions:
        - Default                       # The default version of Tyk Classic API is "Default"
    - name: petstore
      namespace: default
      kind: TykOasApiDefinition         # Use `TykOasApiDefinition` if you are referencing Tyk OAS API
      versions:
        - ""                            # The default version of Tyk OAS API is ""
  quota_max: 1000
  quota_renewal_rate: 3600
  rate: 120
  per: 60
  throttle_interval: -1
  throttle_retry_limit: -1
```

Save the manifest locally in a file, e.g. `trial-policy.yaml`

In this example, we have defined a security policy as described below:

##### Define Security Policy status and metadata

  - **`name`**: A descriptive name for the security policy.
  - **`active`**: Marks the policy as active (true or false).
  - **`state`**: The current state of the policy. It can have one of three values:
    - **`active`**: Keys connected to this policy are enabled and new keys can be created.
    - **`draft`**: Keys connected to this policy are disabled; no new keys can be created.
    - **`deny`**: Policy is not published to Gateway; no keys can be created.
  - **`tags`**: A list of tags to categorize or label the security policy, e.g.

    ```yaml
    tags:
      - Hello
      - World
    ```

  - **`meta_data`**: Key-value pairs for additional metadata related to the policy, e.g.

    ```yaml
    meta_data:
      key: value
      hello: world
    ```

##### Define Access Lists for APIs

  - **`access_rights_array`**: Defines the list of APIs that the security policy applies to and the versions of those APIs.
    - **`name`**: The Kubernetes metadata name of the API resource to which the policy grants access.
    - **`namespace`**: The Kubernetes namespace where the API resource is deployed.
    - **`kind`**: Both Tyk OAS APIs (`TykOasApiDefinition`) and Tyk Classic APIs (`ApiDefinition`) can be referenced here. The API format can be specified by `kind` field. If omitted, `ApiDefinition` is assumed.
    - **`versions`**: Specifies the API versions the policy will cover. If the API is not versioned, include the default version here. The default version of a Classic API is "Default". The default version of an OAS API is "".

In this example, the security policy will apply to an `ApiDefinition` resource named `httpbin` in the `default` namespace and a `TykOasApiDefinition` resource named `petstore` in the `default` namespace. Note that with Tyk Operator, you do not need to specify API ID as in the raw [Policy definition]({{<ref "basic-config-and-security/security/security-policies/policies-guide">}}). Tyk Operator will automatically retrieve the API ID of referenced API Definition resources for you.

##### Define Rate Limits, Usage Quota, and Throttling

- **`rate`**: The maximum number of requests allowed per time period (Set to `-1` to disable).
- **`per`**: The time period (in seconds) for the rate limit (Set to `-1` to disable).
- **`throttle_interval`**: The interval (in seconds) between each request retry  (Set to `-1` to disable).
- **`throttle_retry_limit`**: The maximum number of retry attempts allowed  (Set to `-1` to disable).
- **`quota_max`**: The maximum number of requests allowed over a quota period (Set to `-1` to disable).
- **`quota_renewal_rate`**: The time, in seconds, after which the quota is renewed.

In this example, trial users under this security policy can gain access to the `httpbin` API at a rate limit of maximum 120 times per 60 seconds (`"rate": 120, "per": 60`), with a usage quota of 1000 every hour (`"quota_max": 1000, "quota_renewal_rate": 3600`), without any request throttling (`throttle_interval: -1, throttle_retry_limit: -1`).

#### Apply the Security Policy manifest
Once you have defined your security policy manifest, apply it to your Kubernetes cluster using the `kubectl apply` command:

```bash
kubectl apply -f trial-policy.yaml
```

#### Verify the Security Policy

After applying the manifest, you can verify that the security policy has been created successfully by running:

```bash
kubectl describe securitypolicy trial-policy

...
Status:
  Latest CRD Spec Hash:  901732141095659136
  Latest Tyk Spec Hash:  5475428707334545086
  linked_apis:
    Kind:       ApiDefinition
    Name:       httpbin
    Namespace:  default
    Kind:       TykOasApiDefinition
    Name:       petstore
    Namespace:  default
  pol_id:       66e9a27bfdd3040001af6246
Events:         <none>
```

From the `status` field, you can see that this security policy has been linked to `httpbin` and `petstore` APIs.


#### Security Policy Examples
##### Key-Level Per-API Rate Limits and Quota{#per-api-limit}

By configuring per-API limits, you can set specific rate limits, quotas, and throttling rules for each API in the access rights array. When these per-API settings are enabled, the API inherits the global limit settings unless specific limits and quotas are set in the `limit` field for that API.

The following manifest defines a security policy with per-API rate limits and quotas for two APIs: `httpbin` and `petstore`.

```yaml {hl_lines=["15-21", "27-33", "40-41"],linenos=true}
apiVersion: tyk.tyk.io/v1alpha1
kind: SecurityPolicy
metadata:
  name: policy-per-api-limits
spec:
  name: Policy with Per API Limits
  state: active
  active: true
  access_rights_array:
    - name: httpbin                     # Kubernetes name of referenced API
      namespace: default                # Kubernetes namespace of referenced API
      kind: ApiDefinition               # `ApiDefinition` (Default) or `TykOasApiDefinition`
      versions:
        - Default                       # The default version of Tyk Classic API is "Default"
      limit:                            # APILimit stores quota and rate limit on ACL level
        rate: 10                        # Max 10 requests per 60 seconds
        per: 60                         # Time period for rate limit
        quota_max: 100                  # Max 100 requests allowed over the quota period
        quota_renewal_rate: 3600        # Quota renewal period in seconds (1 hour)
        throttle_interval: -1           # No throttling between retries
        throttle_retry_limit: -1        # No limit on request retries
    - name: petstore
      namespace: default
      kind: TykOasApiDefinition         # Use `TykOasApiDefinition` for Tyk OAS API
      versions:
        - ""                            # The default version of Tyk OAS API is ""
      limit:
        rate: 5                         # Max 5 requests per 60 seconds
        per: 60                         # Time period for rate limit
        quota_max: 100                  # Max 100 requests allowed over the quota period
        quota_renewal_rate: 3600        # Quota renewal period in seconds (1 hour)
        throttle_interval: -1           # No throttling between retries
        throttle_retry_limit: -1        # No limit on request retries
  rate: -1                              # Disable global rate limit
  per: -1                               # Disable global rate limit period
  throttle_interval: -1                 # Disable global throttling
  throttle_retry_limit: -1              # Disable global retry limit
  quota_max: -1                         # Disable global quota
  quota_renewal_rate: 60                # Quota renewal rate in seconds (1 minute)
```

With this security policy applied:

For the `httpbin` API:
- The rate limit allows a maximum of 10 requests per 60 seconds.
- The quota allows a maximum of 100 requests per hour (3600 seconds).
- There is no throttling or retry limit (throttle_interval and throttle_retry_limit are set to -1).

For the `petstore` API:
- The rate limit allows a maximum of 5 requests per 60 seconds.
- The quota allows a maximum of 100 requests per hour (3600 seconds).
- There is no throttling or retry limit (throttle_interval and throttle_retry_limit are set to -1).

Global Rate Limits and Quota:
- All global limits (rate, quota, and throttling) are disabled (-1), so they do not apply.

By setting per-API rate limits and quotas, you gain granular control over how each API is accessed and used, allowing you to apply different limits for different APIs as needed. This configuration is particularly useful when you want to ensure that critical APIs have stricter controls while allowing more flexibility for others. Use this example as a guideline to tailor your security policies to your specific requirements.

##### Key-Level Per-Endpoint Rate Limits{#per-endpoint-rate-limit}

By configuring key-level per-endpoint limits, you can restrict the request rate for specific API clients to a specific endpoint of an API.

The following manifest defines a security policy with per-endpoint rate limits for two APIs: `httpbin` and `petstore`.

```yaml {hl_lines=["15-29", "35-49"],linenos=true}
apiVersion: tyk.tyk.io/v1alpha1
kind: SecurityPolicy
metadata:
  name: policy-per-api-limits
spec:
  name: Policy with Per API Limits
  state: active
  active: true
  access_rights_array:
    - name: httpbin                     # Kubernetes name of referenced API
      namespace: default                # Kubernetes namespace of referenced API
      kind: ApiDefinition               # `ApiDefinition` (Default) or `TykOasApiDefinition`
      versions:
        - Default                       # The default version of Tyk Classic API is "Default"
      endpoints:                        # Per-endpoint rate limits
        - path: /anything
          methods:
            - name: POST
              limit:
                rate: 5
                per: 60
            - name: PUT
              limit:
                rate: 5
                per: 60
            - name: GET
              limit:
                rate: 10
                per: 60
    - name: petstore
      namespace: default
      kind: TykOasApiDefinition         # Use `TykOasApiDefinition` for Tyk OAS API
      versions:
        - ""                            # The default version of Tyk OAS API is ""
      endpoints:                        # Per-endpoint rate limits
        - path: /pet
          methods:
            - name: POST
              limit:
                rate: 5
                per: 60
            - name: PUT
              limit:
                rate: 5
                per: 60
            - name: GET
              limit:
                rate: 10
                per: 60
  rate: -1                              # Disable global rate limit
  per: -1                               # Disable global rate limit period
  throttle_interval: -1                 # Disable global throttling
  throttle_retry_limit: -1              # Disable global retry limit
  quota_max: -1                         # Disable global quota
  quota_renewal_rate: 60                # Quota renewal rate in seconds (1 minute)
```

##### Path based permissions{#path-based-permissions}

You can secure your APIs by specifying [allowed URLs]({{<ref "security/security-policies/secure-apis-method-path">}}) (methods and paths) for each API within a security policy. This is done using the `allowed_urls` field under `access_rights_array`.

The following manifest defines a security policy that allows access only to specific URLs and HTTP methods for two APIs: `httpbin`(a Tyk Classic API) and `petstore` (a Tyk OAS API).

```yaml {hl_lines=["15-18", "24-28"],linenos=true}
apiVersion: tyk.tyk.io/v1alpha1
kind: SecurityPolicy
metadata:
  name: policy-with-allowed-urls
spec:
  name: Policy with allowed URLs
  state: active
  active: true
  access_rights_array:
    - name: httpbin                     # Kubernetes name of referenced API
      namespace: default                # Kubernetes namespace of referenced API
      kind: ApiDefinition               # `ApiDefinition` (Default) or `TykOasApiDefinition`
      versions:
        - Default                       # The default version of Tyk Classic API is "Default"
      allowed_urls:                     # Define allowed paths and methods
        - url: /get                     # Only allow access to the "/get" path
          methods:
            - GET                       # Only allow the GET method
    - name: petstore
      namespace: default
      kind: TykOasApiDefinition         # Use `TykOasApiDefinition` for Tyk OAS API
      versions:
        - ""                            # The default version of Tyk OAS API is ""
      allowed_urls:                     # Define allowed paths and methods
        - url: "/pet/(.*)"              # Allow access to any path starting with "/pet/"
          methods:
            - GET                       # Allow GET method
            - POST                      # Allow POST method
```

With this security policy applied:

- Allowed access:
    - `curl -H "Authorization: Bearer $KEY_AUTH" http://tyk-gw.org/petstore/pet/10` returns a `200 OK` response.
    - `curl -H "Authorization: Bearer $KEY_AUTH" http://tyk-gw.org/httpbin/get` returns a `200 OK` response.

- Restricted access:
    - `curl -H "Authorization: Bearer $KEY_AUTH" http://tyk-gw.org/petstore/pet` returns a `403 Forbidden` response with the message:
        
    ```json
        { "error": "Access to this resource has been disallowed" }
    ```

    - `curl -H "Authorization: Bearer $KEY_AUTH" http://tyk-gw.org/httpbin/anything` returns a `403 Forbidden` response with the message:

    ```json
        { "error": "Access to this resource has been disallowed" }
    ```

##### Partitioned policies{#partitioned-policies}

[Partitioned policies]({{<ref "basic-config-and-security/security/security-policies/partitioned-policies">}}) allow you to selectively enforce different segments of a security policy, such as quota, rate limiting, access control lists (ACL), and GraphQL complexity rules. This provides flexibility in applying different security controls as needed.

To configure a partitioned policy, set the segments you want to enable in the `partitions` field:

```yaml
apiVersion: tyk.tyk.io/v1alpha1
kind: SecurityPolicy
metadata:
  name: partitioned-policy-example
spec:
  name: Partitioned Policy Example
  state: active
  active: true
  access_rights_array:
    - name: httpbin                     # Kubernetes name of referenced API
      namespace: default                # Kubernetes namespace of referenced API
      kind: ApiDefinition               # `ApiDefinition` (Default) or `TykOasApiDefinition`
      versions:
        - Default                       # The default version of Tyk Classic API is "Default"
    - name: petstore
      namespace: default
      kind: TykOasApiDefinition         # Use `TykOasApiDefinition` if you are referencing Tyk OAS API
      versions:
        - ""                            # The default version of Tyk OAS API is ""
  partitions:
    quota: false                        # Do not enforce quota rules
    rate_limit: false                   # Do not enforce rate limiting rules
    acl: true                           # Enforce access control rules
    complexity: false                   # Do not enforce GraphQL complexity rules
```

- **`quota`**: Set to true to enforce quota rules (limits the number of requests allowed over a period).
- **`rate_limit`**: Set to true to enforce rate limiting rules (limits the number of requests per second or minute).
- **`acl`**: Set to true to enforce access control rules (controls which APIs or paths can be accessed).
- **`complexity`**: Set to true to enforce GraphQL complexity rules (limits the complexity of GraphQL queries to prevent resource exhaustion).

### Go Beyond OAS APIs

#### Set Up Manifest for GraphQL
In the example below we can see that the configuration is contained within the `graphql` configuration object. A GraphQL schema is specified within the `schema` field and the execution mode is set to `proxyOnly`. The [GraphQL public playground]({{< ref "graphql/graphql-playground#enabling-public-graphql-playground" >}}) is enabled with the path set to `/playground`.

```yaml {hl_lines=["15-17", "18-92"],linenos=false}
apiVersion: tyk.tyk.io/v1alpha1
kind: ApiDefinition
metadata:
  name: trevorblades
spec:
  name: trevorblades
  use_keyless: true
  protocol: http
  active: true
  proxy:
    target_url: https://countries.trevorblades.com
    listen_path: /trevorblades
    strip_listen_path: true
  graphql:
    enabled: true
    version: "2"
    execution_mode: proxyOnly
    schema: |
      directive @cacheControl(maxAge: Int, scope: CacheControlScope) on FIELD_DEFINITION | OBJECT | INTERFACE

      enum CacheControlScope {
        PUBLIC
        PRIVATE
      }

      type Continent {
        code: ID!
        name: String!
        countries: [Country!]!
      }

      input ContinentFilterInput {
        code: StringQueryOperatorInput
      }

      type Country {
        code: ID!
        name: String!
        native: String!
        phone: String!
        continent: Continent!
        capital: String
        currency: String
        languages: [Language!]!
        emoji: String!
        emojiU: String!
        states: [State!]!
      }

      input CountryFilterInput {
        code: StringQueryOperatorInput
        currency: StringQueryOperatorInput
        continent: StringQueryOperatorInput
      }

      type Language {
        code: ID!
        name: String
        native: String
        rtl: Boolean!
      }

      input LanguageFilterInput {
        code: StringQueryOperatorInput
      }

      type Query {
        continents(filter: ContinentFilterInput): [Continent!]!
        continent(code: ID!): Continent
        countries(filter: CountryFilterInput): [Country!]!
        country(code: ID!): Country
        languages(filter: LanguageFilterInput): [Language!]!
        language(code: ID!): Language
      }

      type State {
        code: String
        name: String!
        country: Country!
      }

      input StringQueryOperatorInput {
        eq: String
        ne: String
        in: [String]
        nin: [String]
        regex: String
        glob: String
      }

      """The `Upload` scalar type represents a file upload."""
      scalar Upload
    playground:
      enabled: true
      path: /playground
```

#### Set Up Manifest for HTTP
##### HTTP Proxy

This example creates a basic API definition that routes requests to listen path `/httpbin` to target URL `http://httpbin.org`.

Traffic routing can be configured under `spec.proxy`:
- `target_url` defines the upstream address (or target URL) to which requests should be proxied.
- `listen_path` is the base path on Tyk to which requests for this API should be sent. Tyk listens out for any requests coming into the host at this path, on the port that Tyk is configured to run on and processes these accordingly. For example, `/api/` or `/` or `/httpbin/`.
- `strip_listen_path` removes the inbound listen path (as accessed by the client) when generating the outbound request for the upstream service. For example, consider the scenario where the Tyk base address is `http://acme.com/`, the listen path is `example/` and the upstream URL is `http://httpbin.org/`: If the client application sends a request to `http://acme.com/example/get` then the request will be proxied to `http://httpbin.org/example/get`

```yaml {hl_lines=["10-13"],linenos=false}
apiVersion: tyk.tyk.io/v1alpha1
kind: ApiDefinition
metadata:
  name: httpbin
spec:
  name: httpbin
  use_keyless: true
  protocol: http
  active: true
  proxy:
    target_url: http://httpbin.org
    listen_path: /httpbin
    strip_listen_path: true
```

##### HTTP Host-based Proxy

`spec.domain` is the domain to bind this API to. This enforces domain matching for client requests.

In this example, requests to `httpbin.tyk.io` will be proxied to upstream URL `http://httpbin.org`

```yaml {hl_lines=["10-10"],linenos=false}
apiVersion: tyk.tyk.io/v1alpha1
kind: ApiDefinition
metadata:
  name: httpbin
spec:
  name: httpbin
  use_keyless: true
  protocol: http
  active: true
  domain: httpbin.tyk.io
  proxy:
    target_url: http://httpbin.org
    listen_path: /
    strip_listen_path: true
```

##### HTTPS Proxy

This example creates a API definition that routes requests to a http://httpbin.org via port 8443.

```yaml {hl_lines=["35-38"],linenos=false}
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: selfsigned-issuer
spec:
  selfSigned: { }
---
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: my-test-cert
spec:
  secretName: my-test-tls
  dnsNames:
    - foo.com
    - bar.com
  privateKey:
    rotationPolicy: Always
  issuerRef:
    name: selfsigned-issuer
    # We can reference ClusterIssuers by changing the kind here.
    # The default value is Issuer (i.e. a locally namespaced Issuer)
    kind: Issuer
    # This is optional since cert-manager will default to this value however
    # if you are using an external issuer, change this to that issuer group.
    group: cert-manager.io
---
apiVersion: tyk.tyk.io/v1alpha1
kind: ApiDefinition
metadata:
  name: httpbin
spec:
  name: httpbin
  use_keyless: true
  protocol: https
  listen_port: 8443
  certificate_secret_names:
    - my-test-tls
  active: true
  proxy:
    target_url: http://httpbin.org
    listen_path: /httpbin
    strip_listen_path: true
```

#### Set Up Manifest for TCP

This example creates a API definition that proxies request from TCP port `6380` to `tcp://localhost:6379`.

```yaml {hl_lines=["8-11"],linenos=false}
apiVersion: tyk.tyk.io/v1alpha1
kind: ApiDefinition
metadata:
  name: redis-tcp
spec:
  name: redis-tcp
  active: true
  protocol: tcp
  listen_port: 6380
  proxy:
    target_url: tcp://localhost:6379
```

#### Set Up Manifest for UDG
##### UDG v2 (Tyk 3.2 and above)

If you are on Tyk 3.2 and above, you can use the following manifest to create an UDG API. This example configures a Universal Data Graph from a [GraphQL datasource]({{<ref "universal-data-graph/datasources/graphql">}}) and a [REST Datasource]({{<ref "universal-data-graph/datasources/rest">}}).

```yaml {hl_lines=["20-39", "46-80"],linenos=false}
apiVersion: tyk.tyk.io/v1alpha1
kind: ApiDefinition
metadata:
  name: udg
spec:
  name: Universal Data Graph v2a
  use_keyless: true
  protocol: http
  active: true
  proxy:
    target_url: ""
    listen_path: /udg
    strip_listen_path: true
  version_data:
    default_version: Default
    not_versioned: true
    versions:
      Default:
        name: Default
  graphql:
    enabled: true
    execution_mode: executionEngine
    schema: |
      type Country {
        name: String
        code: String
        restCountry: RestCountry
      }

      type Query {
        countries: [Country]
      }

      type RestCountry {
        altSpellings: [String]
        subregion: String
        population: Int
      }
    version: "2"
    last_schema_update: "2022-10-12T14:27:55.511+03:00"
    type_field_configurations: []
    playground:
      enabled: true
      path: /playground
    engine:
      field_configs:
        - disable_default_mapping: false
          field_name: countries
          path:
            - "countries"
          type_name: Query
        - disable_default_mapping: true #very important for rest APIs
          field_name: restCountry
          path: []
          type_name: Country
      data_sources:
        - kind: "GraphQL"
          name: "countries"
          internal: false
          root_fields:
            - type: Query
              fields:
                - "countries"
          config:
            url: "https://countries.trevorblades.com/"
            method: "POST"
            headers: {}
            body: ""
        - kind: "REST"
          internal: false
          name: "restCountries"
          root_fields:
            - type: "Country"
              fields:
                - "restCountry"
          config:
            url: "https://restcountries.com/v2/alpha/{{ .object.code }}"
            method: "GET"
            body: ""
            headers: {}
```

##### UDG v1 (Tyk 3.1 or before)

If you are on Tyk 3.1, you can use the following manifest to create an UDG API. This example creates a Universal Data Graph with GraphQL datasource and HTTP JSON datasource.

```yaml
apiVersion: tyk.tyk.io/v1alpha1
kind: ApiDefinition
metadata:
  name: udg
spec:
  name: Universal Data Graph Example
  use_keyless: true
  protocol: http
  active: true
  proxy:
    target_url: ""
    listen_path: /udg
    strip_listen_path: true
  graphql:
    enabled: true
    execution_mode: executionEngine
    schema: |
      type Country {
        name: String
        code: String
        restCountry: RestCountry
      }

      type Query {
        countries: [Country]
      }

      type RestCountry {
        altSpellings: [String]
        subregion: String
        population: String
      }
    type_field_configurations:
      - type_name: Query
        field_name: countries
        mapping:
          disabled: false
          path: countries
        data_source:
          kind: GraphQLDataSource
          data_source_config:
            url: "https://countries.trevorblades.com"
            method: POST
            status_code_type_name_mappings: []
      - type_name: Country
        field_name: restCountry
        mapping:
          disabled: true
          path: ""
        data_source:
          kind: HTTPJSONDataSource
          data_source_config:
            url: "https://restcountries.com/v2/alpha/{{ .object.code }}"
            method: GET
            default_type_name: RestCountry
            status_code_type_name_mappings:
              - status_code: 200
    playground:
      enabled: true
      path: /playground
```


## Synchronize Tyk Environment With GitHub Repository

Tyk Sync enables you to export and import Tyk configurations directly from Git, keeping environments aligned without manual configuration updates. This section covers the setup and use of Tyk Sync, providing steps to ensure consistent configurations across different environments.

### Set up Tyk Sync
#### Installation
Currently the application is available via [Docker](https://hub.docker.com/r/tykio/tyk-sync) and [Packagecloud](https://packagecloud.io/tyk/tyk-sync).

#### Docker

To install Tyk Sync using Docker, follow these steps:

##### Pull the Docker image from the Tyk repository

Make sure to specify the version tag you need. For example, to pull version v1.5.0, use the following command:

```bash
SYNC_VERSION=v1.5.0
docker pull tykio/tyk-sync:$SYNC_VERSION
```

All docker images are available on the [Tyk Sync Docker Hub](https://hub.docker.com/r/tykio/tyk-sync/tags) page.

##### Run Tyk Sync

```bash
SYNC_VERSION=v1.5.0
docker run tykio/tyk-sync:$SYNC_VERSION [command] [flag]
```

If you want to dump your API configurations to the local file system or sync configurations saved locally to Tyk, use Docker [bind mounts](https://docs.docker.com/storage/bind-mounts):

```bash
docker run -v /path/to/local/directory:/app/data tykio/tyk-sync:$SYNC_VERSION [command] [flag]
```
Replace [command] with the specific Tyk Sync command you want to execute.


#### Specifying target Tyk installation

##### Tyk Dashboard
For Dashboard users, you can provide the necessary connection details using the `--dashboard` and `--secret` options.

```bash
tyk-sync --dashboard <DASHBOARD_URL> --secret <SECRET> [command] [flags]
```

DASHBOARD_URL is the fully qualified dashboard target URL (e.g. `http://localhost:3000`) and SECRET refers to the API access key use to access your Dashboard API. For dashboard users, you can get it from the “Users” page under “Tyk Dashboard API Access Credentials”.

If you prefer not to provide the secret via the command line, you can set the environment variable `TYKGIT_DB_SECRET` instead. This method keeps your secret secure and avoids exposure in command history.

```bash
export TYKGIT_DB_SECRET=<SECRET>
tyk-sync --dashboard <DASHBOARD_URL> [command] [flags]
```

##### Open Source Gateway
For open source Gateway users, you can provide the necessary connection details using the `--gateway` and `--secret` options.

```bash
tyk-sync --gateway <GATEWAY_URL> --secret <SECRET> [command] [flags]
```

GATEWAY_URL is the fully qualified gateway target URL (e.g. `http://localhost:8080`) and SECRET refers to the API secret (`secret` parameter in your tyk.conf file) used to access your Gateway API.

If you prefer not to provide the secret via the command line, you can set the environment variable `TYKGIT_GW_SECRET` instead. This method keeps your secret secure and avoids exposure in command history.

```bash
export TYKGIT_GW_SECRET=<SECRET>
tyk-sync --gateway <GATEWAY_URL> [command] [flags]
```

2. Export configurations from your development environment:

```bash
tyk-sync dump -d http://localhost:3000 -s <dashboard-secret> -t dev-backup
```

This command exports all configurations from your development Tyk Dashboard to a local directory named `dev-backup`.

3. Import configurations to your staging environment:

```bash
tyk-sync publish -d http://staging-dashboard:3000 -s <staging-secret> -p dev-backup
```

This command imports the configurations from the `dev-backup` directory to your staging Tyk Dashboard.


### Automate API Configuration Management with Tyk Sync
By integrating GitHub Actions, teams can schedule backups to cloud storage, sync configurations from a Git repository, and update local API definitions directly to the Tyk Dashboard. These workflows ensure configurations are securely maintained, aligned across environments, and easily managed within the API lifecycle.


#### Backup API Configurations with Github Actions
API platform teams can automate configuration backups using GitHub Actions. By setting up a scheduled GitHub Action, API configurations can be periodically exported and stored in cloud storage, like AWS S3. This approach ensures backups remain up-to-date, offering a reliable way to safeguard data and simplify restoration if needed.


##### Create a GitHub Action workflow

1. In your repository, create a new file `.github/workflows/tyk-backup.yml`.
2. Add the following content to the `tyk-backup.yml` file:

```yaml
name: Tyk Backup

on:
  schedule:
    - cron: '0 0 * * *'  # Runs every day at midnight

jobs:
  backup:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Create Backup Directory
      run: |
        BACKUP_DIR="backup/$(date +%Y-%m-%d)"
        mkdir -p $BACKUP_DIR
        echo "BACKUP_DIR=$BACKUP_DIR" >> $GITHUB_ENV

    - name: Set Permissions for Backup Directory
      run: |
        sudo chown -R 1001:1001 ${{ github.workspace }}/backup

    - name: Dump API Configurations
      run: |
        docker run --user 1001:1001 -v ${{ github.workspace }}:/app/data tykio/tyk-sync:${TYK_SYNC_VERSION} dump --target /app/data/${{ env.BACKUP_DIR }} --dashboard ${TYK_DASHBOARD_URL} --secret ${TYK_DASHBOARD_SECRET}
      env:
        TYK_SYNC_VERSION: ${{ vars.TYK_SYNC_VERSION }}
        TYK_DASHBOARD_URL: ${{ secrets.TYK_DASHBOARD_URL }}
        TYK_DASHBOARD_SECRET: ${{ secrets.TYK_DASHBOARD_SECRET }}

    - name: Upload to S3
      uses: jakejarvis/s3-sync-action@v0.5.1
      with:
        args: --acl private --follow-symlinks --delete
      env:
        AWS_S3_BUCKET: ${{ secrets.AWS_S3_BUCKET }}
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        AWS_REGION: 'us-east-1'  # Change to your region
        SOURCE_DIR: ${{ env.BACKUP_DIR }}
```

##### Set up secrets

1. Go to your GitHub repository.
2. Navigate to Settings > Secrets and variables > Actions.
3. Add the following variable:
    - `TYK_SYNC_VERSION`: The version of Tyk Sync you want to use.
4. Add the following secrets:
   - `TYK_DASHBOARD_URL`: The URL of your Tyk Dashboard.
   - `TYK_DASHBOARD_SECRET`: The secret key for your Tyk Dashboard.
   - `AWS_S3_BUCKET`: The name of your AWS S3 bucket.
   - `AWS_ACCESS_KEY_ID`: Your AWS access key ID.
   - `AWS_SECRET_ACCESS_KEY`: Your AWS secret access key.

##### Commit and push changes

Commit the `tyk-backup.yml` file and push it to the main branch of your repository.

##### Verify backups

The GitHub Action will run every day at midnight, dumping API configurations into a backup directory and uploading them to your specified S3 bucket.


#### Synchronize API configurations with GitHub Actions
API platform teams can use GitHub Actions to sync API configurations, policies, and templates from a Git repository to Tyk. Triggered by repository changes, the action generates a .tyk.json file and applies updates with the sync command, keeping the Tyk setup aligned with the repository.

##### Setup GitHub repository
Organize your repository with the following structure:

- `/apis/` for API definition files.
- `/policies/` for security policy files.
- `/assets/` for API template files.

##### Create a GitHub Action workflow

1. In your repository, create a new file `.github/workflows/tyk-sync.yml`.
2. Add the following content to the `tyk-sync.yml` file:

```yaml
name: Tyk Sync

on:
  push:
    branches:
      - main

jobs:
  sync:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Create .tyk.json
      run: |
        echo '{' > .tyk.json
        echo '  "type": "apidef",' >> .tyk.json
        echo '  "files": [' >> .tyk.json
        find . -type f -name '*.json' -path './apis/*' -exec echo '    {"file": "{}"},' \; | sed '$ s/,$//' >> .tyk.json
        echo '  ],' >> .tyk.json
        echo '  "policies": [' >> .tyk.json
        find . -type f -name '*.json' -path './policies/*' -exec echo '    {"file": "{}"},' \; | sed '$ s/,$//' >> .tyk.json
        echo '  ],' >> .tyk.json
        echo '  "assets": [' >> .tyk.json
        find . -type f -name '*.json' -path './assets/*' -exec echo '    {"file": "{}"},' \; | sed '$ s/,$//' >> .tyk.json
        echo '  ]' >> .tyk.json
        echo '}' >> .tyk.json
        cat .tyk.json

    - name: Sync with Tyk
      run: |
        docker run tykio/tyk-sync:${TYK_SYNC_VERSION} version
        docker run -v ${{ github.workspace }}:/app/data tykio/tyk-sync:${TYK_SYNC_VERSION} sync --path /app/data --dashboard ${TYK_DASHBOARD_URL} --secret ${TYK_DASHBOARD_SECRET}
      env:
        TYK_SYNC_VERSION: ${{ vars.TYK_SYNC_VERSION }}
        TYK_DASHBOARD_URL: ${{ secrets.TYK_DASHBOARD_URL }}
        TYK_DASHBOARD_SECRET: ${{ secrets.TYK_DASHBOARD_SECRET }}
```

##### Set up secrets

1. Go to your GitHub repository.
2. Navigate to Settings > Secrets and variables > Actions.
3. Add the following variable:
    - `TYK_SYNC_VERSION`: The version of Tyk Sync you want to use (e.g., v2.0.0).
4. Add the following secrets:
    - `TYK_DASHBOARD_URL`: The URL of your Tyk Dashboard.
    - `TYK_DASHBOARD_SECRET`: The secret key for your Tyk Dashboard.

##### Commit and push changes

Commit the `tyk-sync.yml` file and push it to the main branch of your repository.

##### Verify synchronisation

Each time there is a change in the repository, the GitHub Action will be triggered. It will create the `.tyk.json` file including all JSON files in the repository and use the `sync` command to update the Tyk installation.


#### Update API Definitions locally
For API developers managing definitions locally, Tyk Sync's publish or update commands can upload local API definitions directly to the Tyk Dashboard, streamlining updates and keeping definitions in sync during development. Follow these steps to update your API definitions locally.

##### Prepare your API Definition

Create your API definition file and save it locally. For example, save it as *api1.json* in a directory structure of your choice.

##### Create a .tyk.json index file

In the root directory of your API definitions, create a `.tyk.json` file to list all API definition files that Tyk Sync should process.

Example `.tyk.json`:
```json
{
  "type": "apidef",
  "files": [
    { 
        "file": "api1.json" 
    }
  ]
}
```

##### Install Tyk Sync via Docker

If you haven't installed Tyk Sync, you can do so via Docker:

```bash
docker pull tykio/tyk-sync:v2.0.0
```

##### Publish API Definitions to Tyk

Use the `publish` command to upload your local API definitions to Tyk. Use Docker bind mounts to access your local files.

```bash
docker run -v /path/to/your/directory:/app/data tykio/tyk-sync:v2.0.0 publish \
  --path /app/data \
  --dashboard [DASHBOARD_URL] \
  --secret [SECRET]
```

##### Update API Definitions to Tyk

Similarly, to update existing API definitions, use the update command.

```bash
docker run -v /path/to/your/directory:/app/data tykio/tyk-sync:v2.0.0 update \
  --path /app/data \
  --dashboard [DASHBOARD_URL] \
  --secret [SECRET]
```

##### Verify the update

Log in to your Tyk Dashboard to verify that the API definitions have been published or updated successfully.


### Tyk Sync Commands

#### Dump Command

| Aspect        | Details                                                                                              |
|---------------|------------------------------------------------------------------------------------------------------|
| **Command**   | `tyk-sync dump`                                                                                       |
| **Usage**     | ```tyk-sync dump -d DASHBOARD_URL [-s SECRET] [-t PATH]```                                                 |
| **Flags**     | `-d, --dashboard DASHBOARD_URL`: Tyk Dashboard URL (required)<br>                                    `-h, --help`: Help for the dump command<br>                                   `-t, --target PATH`: Target directory for output files (optional)<br>                                    `-s, --secret SECRET`: API secret for Dashboard access (optional)<br>                                    `--apis IDS`: Specific API IDs to dump<br>                                    `--oas-apis IDS`: Specific OAS API IDs to dump<br>                                    `--policies IDS`: Specific policy IDs to dump<br>                                    `--templates IDS`: Specific template IDs to dump |
| **Example**   | ```tyk-sync dump --dashboard http://tyk-dashboard:3000 --secret your-secret ```|
| **Example** | ```tyk-sync dump --dashboard http://tyk-dashboard:3000 --secret your-secret --target /path/to/backup --apis c2ltcGxlLWdyYXBoLWRldi90eWthcGktc2NoZW1h,baa5d2b65f1b45385dac3aeb658fa04c ``` |

#### Examples Command

| Aspect        | Details                                                                                              |
|---------------|------------------------------------------------------------------------------------------------------|
| **Command**   | `tyk-sync examples`                                                                                   |
| **Usage**     | ```tyk-sync examples [flags]```<br>```tyk-sync examples [command]```                                           |
| **Subcommands**| `publish`: Publish a specific example<br> `show`: Show details of a specific example              |
| **Flags**     | `-h, --help`: Help for examples command                                                             |
| **Example**   | ```tyk-sync examples ```                                                                         |

#### Examples Show Command

| Aspect        | Details                                                                                              |
|---------------|------------------------------------------------------------------------------------------------------|
| **Command**   | ```tyk-sync examples show```                                                                              |
| **Usage**     | ```tyk-sync examples show [flags]```                                                                      |
| **Flags**     | `-h, --help`: Help for show command<br> `-l, --location string`: Location of the example           |
| **Example**   | ```tyk-sync examples show --location="udg/vat-checker" ```                                       |

#### Examples Publish Command

| Aspect        | Details                                                                                              |
|---------------|------------------------------------------------------------------------------------------------------|
| **Command**   | ```tyk-sync examples publish```                                                                           |
| **Usage**     | ```tyk-sync examples publish [flags]```                                                                   |
| **Flags**     | `-b, --branch string`: Branch to use (default "refs/heads/main")<br> `-d, --dashboard string`: Dashboard target URL<br> `-g, --gateway string`: Gateway target URL<br> `-h, --help`: Help for publish command<br> `-k, --key string`: Key file location for auth<br> `-l, --location string`: Location of the example<br> `-s, --secret string`: API secret<br> `--test`: Use test publisher, output to stdio |
| **Example**   | ```tyk-sync examples publish -d="http://localhost:3000" -s="b2d420ca5302442b6f20100f76de7d83" -l="udg/vat-checker" ``` |

#### Publish Command

| Aspect        | Details                                                                                              |
|---------------|------------------------------------------------------------------------------------------------------|
| **Command**   | ```tyk-sync publish```                                                                                    |
| **Usage**     | ```tyk-sync publish {-d DASHBOARD_URL \| -g GATEWAY_URL} [-s SECRET] [-b BRANCH] [-k SSHKEY] [-o ORG_ID] REPOSITORY_URL```<br><br>```tyk-sync publish {-d DASHBOARD_URL \| -g GATEWAY_URL} [-s SECRET] [-o ORG_ID] -p PATH``` |
| **Flags**     |  `-b, --branch BRANCH`: Git branch (default "refs/heads/master")<br> `-d, --dashboard DASHBOARD_URL`: Dashboard URL<br> `-g, --gateway GATEWAY_URL`: Gateway URL<br> `-h, --help`: Help for publish command<br> `-k, --key SSHKEY`: SSH key file location<br> `-p, --path PATH`: Source file directory<br> `-s, --secret SECRET`: API secret<br> `--test`: Use test publisher<br> `--apis IDS`: Specific API IDs to publish<br> `--oas-apis IDS`: Specific OAS API IDs to publish<br> `--policies IDS`: Specific policy IDs to publish<br> `--templates IDS`: Specific template IDs to publish |
| **Example**   | ```tyk-sync publish -d http://tyk-dashboard:3000 -s your-secret -p /app/data --apis 726e705e6afc432742867e1bd898cb23 ```|
| **Example** | ```tyk-sync publish -d http://tyk-dashboard:3000 -s your-secret -b develop https://github.com/your-repo/your-apis ``` |

#### Sync Command

| Aspect        | Details                                                                                              |
|---------------|------------------------------------------------------------------------------------------------------|
| **Command**   | `tyk-sync sync`                                                                                       |
| **Usage**     | ```tyk-sync sync {-d DASHBOARD_URL \| -g GATEWAY_URL} [-s SECRET] [-b BRANCH] [-k SSHKEY] [-o ORG_ID] REPOSITORY_URL```<br><br>```tyk-sync sync {-d DASHBOARD_URL \| -g GATEWAY_URL} [-s SECRET] [-o ORG_ID] -p PATH``` |
| **Flags**     | `-b, --branch BRANCH`: Git branch (default "refs/heads/master")<br> `-d, --dashboard DASHBOARD_URL`: Dashboard URL<br> `-g, --gateway GATEWAY_URL`: Gateway URL<br> `-h, --help`: Help for sync command<br> `-k, --key SSHKEY`: SSH key file location<br> `-o, --org ORG_ID`: Override organization ID<br> `-p, --path PATH`: Source file directory<br> `-s, --secret SECRET`: API secret<br> `--test`: Use test publisher<br> `--apis IDS`: Specific API IDs to sync (to be deprecated)<br> `--policies IDS`: Specific policy IDs to sync (to be deprecated) |
| **Example**   | ```tyk-sync sync -d http://tyk-dashboard:3000 -s your-secret https://github.com/your-repo/your-apis ```|
| **Example** | ```tyk-sync sync -d http://tyk-dashboard:3000 -s your-secret -p /path/to/your/apis ``` |

#### Update Command

| Aspect        | Details                                                                                              |
|---------------|------------------------------------------------------------------------------------------------------|
| **Command**   | `tyk-sync update`                                                                                     |
| **Usage**     | ```tyk-sync update {-d DASHBOARD_URL \| -g GATEWAY_URL} [-s SECRET] [-b BRANCH] [-k SSHKEY] [-o ORG_ID] REPOSITORY_URL```<br><br>```tyk-sync update {-d DASHBOARD_URL \| -g GATEWAY_URL} [-s SECRET] [-o ORG_ID] -p PATH``` |
| **Flags**     | `-b, --branch BRANCH`: Git branch (default "refs/heads/master")<br> `-d, --dashboard DASHBOARD_URL`: Dashboard URL<br> `-g, --gateway GATEWAY_URL`: Gateway URL<br> `-h, --help`: Help for update command<br> `-k, --key SSHKEY`: SSH key file location<br> `-p, --path PATH`: Source file directory<br> `-s, --secret SECRET`: API secret<br> `--test`: Use test publisher<br> `--apis IDS`: Specific API IDs to update<br> `--oas-apis IDS`: Specific OAS API IDs to update<br> `--policies IDS`: Specific policy IDs to update<br> `--templates IDS`: Specific template IDs to update |
| **Example**   | ```tyk-sync update -d http://tyk-dashboard:3000 -s your-secret -p /app/data --apis 726e705e6afc432742867e1bd898cb23```|
| **Example** | ```tyk-sync update -d http://tyk-dashboard:3000 -s your-secret -b develop https://github.com/your-repo/your-apis ``` |


## Automate Multi-Environment Deployments

Setting up automated multi-environment deployments reduces configuration drift and manual errors. This section shows how to integrate Tyk with CI/CD pipelines, providing steps to ensure consistent deployments across development, staging, and production.

### Set up a CI/CD pipeline for API deployment

1. Create a `.github/workflows/deploy-apis.yml` file:

```yaml
name: Deploy APIs
on:
  push:
    branches: [ main ]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Install Tyk Sync
      run: |
        go install github.com/TykTechnologies/tyk-sync
    - name: Deploy to Tyk
      run: |
        tyk-sync sync -d ${{ secrets.TYK_DASHBOARD_URL }} -s ${{ secrets.TYK_DASHBOARD_SECRET }} -p ./
```

This GitHub Actions workflow file defines a job that runs on every push to the main branch. It installs Tyk Sync and uses it to synchronize your API configurations with your Tyk Dashboard.

2. Configure your GitHub repository secrets with your Tyk Dashboard URL and secret.

In your GitHub repository settings, add two secrets:
- `TYK_DASHBOARD_URL`: The URL of your Tyk Dashboard
- `TYK_DASHBOARD_SECRET`: Your Tyk Dashboard API secret


## Troubleshooting and FAQ

### What Features Are Supported By Tyk Operator?

#### API Types
Tyk supports various API types, including HTTP, HTTPS, TCP, TLS, and GraphQL. It also includes Universal Data Graph versions for unified data access and federation, allowing seamless querying across multiple services.

| Type                           | Support | Supported From | Comments                     |
|--------------------------------|---------|----------------|------------------------------|
| HTTP                           | ✅      | v0.1           | Standard HTTP proxy for API requests. |
| HTTPS                          | ✅      | v0.4           | Secure HTTP proxy using SSL/TLS encryption. |
| TCP                            | ✅      | v0.1           | Handles raw TCP traffic, useful for non-HTTP APIs. |
| TLS                            | ✅      | v0.1           | Handles encrypted TLS traffic for secure communication. |
| GraphQL - Proxy                | ✅      | v0.1           | Proxy for GraphQL APIs, routing queries to the appropriate service. |
| Universal Data Graph v1        | ✅      | v0.1           | Supports Universal Data Graph v1 for unified data access. |
| Universal Data Graph v2        | ✅      | v0.12          | Supports the newer Universal Data Graph v2 for more advanced data handling. |
| GraphQL - Federation           | ✅      | v0.12          | Supports GraphQL Federation for querying multiple services as one API. |

#### Management of APIs
Tyk offers flexible API management features such as setting active/inactive status, categorizing and naming APIs, versioning, and defining ownership within teams or organizations for streamlined administration.

| Type                           | Support | Supported From | Comments                     |
|--------------------------------|---------|----------------|------------------------------|
| API Name                       | ✅      | v0.1           | Assign and manage names for your APIs. |
| API Status (inactive/active)   | ✅      | v0.2           | Toggle API status between active and inactive. |
| API Categories                 | ✅      | v0.1           | Categorize APIs for easier management. |
| API ID                         | ✅      | v0.1           | Assign unique IDs to APIs for tracking and management. |
| API Ownership                  | ✅      | v0.12          | Define ownership of APIs within teams or organizations. |
| API Versioning                 | ✅      | v0.1           | Enable version control for APIs. |

#### Traffic Routing
Tyk enables traffic routing through path-based or host-based proxies and allows redirection to specific target URLs, providing control over how requests are directed to backend services.

| Type                        | Supported | Supported From | Comments                     |
| --------------------------- | --------- | -------------- | ---------------------------- |
| Path-Based Proxy            | ✅        | v0.1           | Route traffic based on URL path. |
| Host-Based Proxy            | ✅        | v0.1           | Route traffic based on the request host. |
| Target URL                  | ✅        | v0.1           | Redirect traffic to a specific target URL. |

#### Client to Gateway Authentication and Authorization
Tyk provides multiple authentication options for client-to-gateway interactions, including keyless access, JWT, client mTLS, IP allow/block lists, and custom authentication plugins for enhanced security.

| Type                          | Supported | Supported From | Comments                                        |
| ----------------------------- | --------- | -------------- | ----------------------------------------------- |
| Keyless                       | ✅        | v0.1           | No authentication required, open access.        |
| Auth Token                    | ✅        | v0.1           | Requires an authentication token (Bearer token).|
| JWT                           | ✅️        | v0.5           | Uses JSON Web Tokens for secure authentication. |
| OpenID Connect                | ❌        | -              | Recommended to use JWT for OIDC authentication. |
| OAuth2                        | ❌        | -              | OAuth2 not supported, JWT is recommended.       |
| Client mTLS                   | ✅        | v0.11          | Supports static client mutual TLS authentication. |
| HMAC                          | ❌        | -              | HMAC authentication is not implemented.         |
| Basic Authentication          | ✅        | v0.12          | Only supports enabling with default metadata.   |
| Custom Authentication Plugin (Go)   | ✅        | v0.11          | Custom authentication plugin written in Go.     |
| Custom Authentication Plugin (gRPC) | ✅        | v0.1           | Custom authentication plugin using gRPC.        |
| Multiple Authentication       | ✅        | v0.14          | Chain multiple authentication methods.          |
| IP Allowlist                  | ✅        | v0.5           | Allows access only from specific IP addresses.  |
| IP Blocklist                  | ✅        | v0.5           | Blocks access from specific IP addresses.       |

#### Gateway to Upstream Authentication
Tyk supports secure upstream connections through mutual TLS, certificate pinning, and public key verification to ensure data integrity between the gateway and backend services.

| Type                                            | Supported | Supported From | Comments                     |
|-------------------------------------------------|-----------|----------------|------------------------------|
| Upstream Certificates mTLS                      | ✅        | v0.9           | Mutual TLS authentication for upstream connections. |
| Public Key Certificate Pinning                  | ✅        | v0.9           | Ensures that the upstream certificate matches a known key. |
| Upstream Request Signing                        | ❌        | -              | Upstream request signing is not implemented. |

#### API-level (Global) Features
Tyk offers global features for APIs, such as detailed traffic logging, CORS management, rate limiting, header transformations, and analytics plugins, with support for tagging, load balancing, and dynamic variables.

| Feature                              | Supported | Supported From | Comments                                                               |
|--------------------------------------|-----------|----------------|------------------------------------------------------------------------|
| Detailed recording (in Log Browser)  | ✅        | v0.4.0         | Records detailed API traffic logs for analysis. |
| Config Data                          | ✅        | v0.8.2         | Stores additional configuration data for APIs. |
| Context Variables                    | ✅        | v0.1           | Enables dynamic context-based variables in APIs. |
| Cross Origin Resource Sharing (CORS) | ✅        | v0.2           | Manages CORS settings for cross-domain requests. |
| Service Discovery                    | ⚠️         | -              | Service discovery is untested in this version. |
| Segment Tags                         | ✅        | v0.1           | Tags APIs for segmentation across environments. |
| Internal API (not exposed by Gateway)| ✅        | v0.6.0         | Internal APIs are not exposed via the Gateway. |
| Global (API-level) Header Transform  | ✅        | v0.1.0         | Transforms request and response headers at the API level. |
| Global (API-level) Rate Limit        | ✅        | v0.10          | Sets rate limits globally for APIs. |
| Custom Plugins                       | ✅        | v0.1           | Supports the use of custom plugins for API processing. |
| Analytics Plugin                     | ✅        | v0.16.0        | Integrates analytics plugins for API monitoring. |
| Batch Requests                       | ❌        | -              | Batch requests are not supported. |
| Custom Analytics Tags (Tag Headers)  | ✅        | v0.10.0        | Custom tags for API analytics data. |
| Expire Analytics After               | ❌        | -              | Not supported in this version. |
| Do not track Analytics (per API)     | ✅        | v0.1.0         | Disable analytics tracking on specific APIs. |
| Webhooks                             | ❌        | -              | Webhook support is not available. |
| Looping                              | ✅        | v0.6           | Enables internal looping of API requests. |
| Round Robin Load Balancing           | ✅        | -              | Supports round-robin load balancing across upstream servers. |

#### Endpoint-level Features
For specific API endpoints, Tyk includes features like caching, circuit breaking, request validation, URL rewriting, and response transformations, allowing for precise control over request processing and response handling at an endpoint level.

| Endpoint Middleware               | Supported | Supported From | Comments                                       |
|-----------------------------------|-----------|----------------|------------------------------------------------|
| Allow list                        | ✅️        | v0.8.2         | Allows requests only from approved sources.    |
| Block list                        | ✅️        | v0.8.2         | Blocks requests from disapproved sources.      |
| Cache                             | ✅        | v0.1           | Caches responses to reduce latency.            |
| Advance Cache                     | ✅        | v0.1           | Provides advanced caching capabilities.        |
| Circuit Breaker                   | ✅        | v0.5           | Prevents service overload by breaking circuits. |
| Track Endpoint                    | ✅        | v0.1           | Tracks API endpoint usage for analysis.        |
| Do Not Track Endpoint             | ✅        | v0.1           | Disables tracking for specific endpoints.      |
| Enforced Timeouts                 | ✅        | v0.1           | Ensures timeouts for long-running requests.    |
| Ignore Authentication             | ✅        | v0.8.2         | Bypasses authentication for selected endpoints.|
| Internal Endpoint                 | ✅        | v0.1           | Restricts access to internal services.         |
| URL Rewrite                       | ✅️        | v0.1           | Modifies request URLs before processing.       |
| Validate Request                  | ✅        | v0.8.2         | Validates incoming requests before forwarding. |
| Rate Limit                        | ❌        | -              | Rate limiting is not supported per endpoint.   |
| Request Size Limit                | ✅️        | v0.1           | Limits the size of requests to prevent overload.|
| Request Method Transform          | ✅        | v0.5           | Modifies HTTP methods for incoming requests.   |
| Request Header Transform          | ✅        | v0.1           | Transforms request headers.                    |
| Request Body Transform            | ✅        | v0.1           | Transforms request bodies for processing.      |
| Request Body JQ Transform         | ⚠️         | v0.1           | Requires JQ support on the Gateway Docker image.|
| Response Header Transform         | ✅        | v0.1           | Transforms response headers.                   |
| Response Body Transform           | ✅        | v0.1           | Transforms response bodies.                    |
| Response Body JQ Transform        | ⚠️         | v0.1           | Requires JQ support on the Gateway Docker image.|
| Mock Response                     | ✅        | v0.1           | Simulates API responses for testing.           |
| Virtual Endpoint                  | ✅        | v0.1           | Allows creation of dynamic virtual endpoints.  |
| Per-Endpoint Plugin               | ❌        | -              | Plugin support per endpoint is not available.  |
| Persist Graphql                   | ❌        | -              | Not supported in this version.                 |





### Tyk Operator changes not applied

**Problem:** Changes made through Tyk Operator are not reflected in your Tyk installation.

**Solution:**

1. Check Kubernetes events:
   ```bash
   kubectl get events --sort-by=.metadata.creationTimestamp
   ```
   This command shows recent events in your cluster, which may provide clues about why the changes weren't applied.

2. Verify Operator logs:
   ```bash
   kubectl logs -l app=tyk-operator
   ```
   This command shows logs from the Tyk Operator pod, which may contain error messages or other useful information.

### How are Tyk configurations synchronized to Git?

Tyk Sync allows you to dump configurations to a local directory, which can then be committed to a Git repository. This enables version control and easy synchronization across environments.

For example:
1. Dump configurations: `tyk-sync dump -d http://dashboard:3000 -s secret -t ./configs`
2. Commit to Git: 
   ```
   cd configs
   git add .
   git commit -m "Update Tyk configurations"
   git push
   ```

### Can I sync multiple APIs to a single Git repository?

Yes, you can store multiple API definitions, policies, and other Tyk resources in a single Git repository. Tyk Sync and Tyk Operator can work with multiple resources in the same directory.

Your repository structure might look like this:
```
tyk-configs/
├── apis/
│   ├── api1.yaml
│   └── api2.yaml
├── policies/
│   ├── policy1.yaml
│   └── policy2.yaml
└── tyk-operator/
    └── operator-context.yaml
```

### How do I handle environment-specific configurations?

Use Tyk Operator's `OperatorContext` resource to define environment-specific variables. You can also use Kubernetes secrets and ConfigMaps to manage sensitive or environment-specific data.

Example `OperatorContext`:
```yaml
apiVersion: tyk.tyk.io/v1alpha1
kind: OperatorContext
metadata:
  name: production-context
spec:
  env:
    TYK_DB_ORGID: "prod-org-id"
    TYK_DB_APIAUTH: "prod-api-secret"
```

This YAML defines environment-specific variables for a production context, which can be referenced in your API definitions and policies.


### Can I use Tyk Operator with multiple Tyk installations?

Yes, you can use Tyk Operator to manage multiple Tyk installations. You'll need to create separate `OperatorContext` resources for each installation:

```yaml
apiVersion: tyk.tyk.io/v1alpha1
kind: OperatorContext
metadata:
  name: prod-context
spec:
  env:
    TYK_MODE: pro
    TYK_URL: http://tyk-gateway-prod:8080
    TYK_AUTH: prod-secret
---
apiVersion: tyk.tyk.io/v1alpha1
kind: OperatorContext
metadata:
  name: staging-context
spec:
  env:
    TYK_MODE: pro
    TYK_URL: http://tyk-gateway-staging:8080
    TYK_AUTH: staging-secret
```

Then, you can specify which context to use in your API and Policy resources:

```yaml
apiVersion: tyk.tyk.io/v1alpha1
kind: ApiDefinition
metadata:
  name: my-api
spec:
  name: My API
  context: prod-context
  # ... other API configuration
```

### How do I roll back changes made with Tyk Sync?

To roll back changes made with Tyk Sync:

1. If you're using Git, check out the previous version of your configurations:
   ```bash
   git checkout <previous-commit-hash>
   ```

2. Use Tyk Sync to publish the previous version:
   ```bash
   tyk-sync sync -d http://dashboard:3000 -s <secret> -p ./
   ```

It's a good practice to maintain separate branches or tags for different environments to make rollbacks easier.

### Can I use Tyk Operator with non-Kubernetes Tyk installations?

While Tyk Operator is designed to work within a Kubernetes environment, you can still use it to manage non-Kubernetes Tyk installations. You'll need to:

1. Run Tyk Operator in a Kubernetes cluster.
2. Configure the `OperatorContext` to point to your external Tyk installation:

```yaml
apiVersion: tyk.tyk.io/v1alpha1
kind: OperatorContext
metadata:
  name: external-tyk
spec:
  env:
    TYK_MODE: pro
    TYK_URL: http://external-tyk-gateway:8080
    TYK_AUTH: external-secret
```

This allows you to manage your external Tyk installation using Kubernetes resources.

### How do I migrate from Tyk Classic to Tyk OAS definitions?

To migrate from Tyk Classic to Tyk OAS definitions:

1. Export your existing API definitions using Tyk Sync:
   ```bash
   tyk-sync dump -d http://dashboard:3000 -s <secret> -t ./classic-apis
   ```

2. Use the Tyk OAS Converter tool (if available) or manually convert your Classic definitions to OAS format.

3. Update your CI/CD pipelines and Tyk Operator configurations to work with the new OAS definitions.

4. Gradually replace Classic definitions with OAS definitions in your Tyk installation.

## Conclusion

With Tyk’s automation tools, you now have a set of options for streamlining API management, from handling deployments within Kubernetes to establishing consistency across multiple environments. By integrating these tools, you can simplify complex API workflows, maintain secure configurations, and save time through reduced manual intervention.

To continue building on what you’ve set up here, explore the following topics:

- **Security Policies**: For a deeper dive into securing your APIs, read our [guide on security policies](/basic-config-and-security/security/security-policies/), covering token management, user roles, and access control.
- **Advanced Tyk API Management**: Leverage more of Tyk’s API capabilities for custom integrations and further automation possibilities. You can learn more about Tyk's custom integrations [here](/advanced-configuration/)
- **CI/CD Integrations**: Learn how to embed Tyk automation into your CI/CD pipeline, enabling continuous deployment and reducing release cycle times.