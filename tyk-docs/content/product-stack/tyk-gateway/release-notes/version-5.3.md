---
title: Tyk Gateway 5.3 LTS Release Notes
date: 2024-03-27T15:51:11Z
description:
  "Release notes documenting updates, enhancements, and changes for Tyk Gateway versions within the 5.3.X series."
tags: ["Tyk Gateway", "Release notes", "changelog", "v5.3", "5.3.0", "5.3.1", "5.3.3", "5.3.5", "5.3.7", "5.3.8" ]
---

<!-- Required. oss or licensed. Choose one of the following:
    **Licensed Protected Product**
    Or
    ****Open Source** ([Mozilla Public License](https://github.com/TykTechnologies/tyk/blob/master/LICENSE.md))**
-->

**Open Source** ([Mozilla Public License](https://github.com/TykTechnologies/tyk/blob/master/LICENSE.md))

**This page contains all release notes for version 5.3.X displayed in a reverse chronological order**

## Support Lifetime

<!-- Required. replace X.Y with this release and set the correct quarter of the year -->

Our minor releases are supported until our next minor comes out.

---

## 5.3.8 Release Notes

### Release Date 07 November 2024

### Release Highlights

This release focuses mainly on bug fixes. For a comprehensive list of changes, please refer to the detailed [changelog]({{< ref "#Changelog-v5.3.8">}}) below.

### Breaking Changes

This release has no breaking changes.

### Dependencies

<!--Required. Use this section to announce the following types of dependencies compatible with the release:
Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.
3rd party dependencies and tools -->

#### Compatibility Matrix For Tyk Components

<!-- Required. Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.
An illustrative example is shown below. -->

| Gateway Version | Recommended Releases                                               | Backwards Compatibility |
| --------------- | ------------------------------------------------------------------ | ----------------------- |
| 5.3.8           | MDCB v2.5.1                                                        | MDCB v2.5.1             |
|                 | Operator v0.17                                                     | Operator v0.16          |
|                 | Sync v1.4.3                                                        | Sync v1.4.3             |
|                 | Helm Chart (tyk-stack, tyk-oss, tyk-dashboard, tyk-gateway) v2.0.0 | Helm all versions       |
|                 | EDP v1.8.3                                                         | EDP all versions        |
|                 | Pump v1.9.0                                                        | Pump all versions       |
|                 | TIB (if using standalone) v1.5.1                                   | TIB all versions        |

#### 3rd Party Dependencies & Tools

<!-- Required. Third-party dependencies encompass tools (GoLang, Helm etc.), databases (PostgreSQL, MongoDB etc.) and external software libraries. This section should be a table that presents the third-party dependencies and tools compatible with the release. Compatible is used in the sense of those versions tested with the releases. Such information assists customers considering upgrading to a specific release.

Additionally, a disclaimer statement was added below the table, for customers to check that the third-party dependency they decide to install remains in support.

An example is given below for illustrative purposes only. Tested Versions and Compatible Versions information will require discussion with relevant squads and QA. -->

| Third Party Dependency                                        | Tested Versions       | Compatible Versions   | Comments                                                                                   |
| ------------------------------------------------------------- | --------------------- | --------------------- | ------------------------------------------------------------------------------------------ |
| [Go](https://go.dev/dl/)                                      |  1.22 (GW)            |  1.22 (GW)            | [Go plugins]({{< ref "plugins/supported-languages/golang" >}}) must be built using Go 1.22 |
| [Redis](https://redis.io/download/)                           | 6.2.x, 7.x            | 6.2.x, 7.x            | Used by Tyk Gateway                                                                        |
| [OpenAPI Specification](https://spec.openapis.org/oas/v3.0.3) | v3.0.x                | v3.0.x                | Supported by [Tyk OAS]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc" >}})           |

Given the potential time difference between your upgrade and the release of this version, we recommend users verify the
ongoing support of third-party dependencies they install, as their status may have changed since the release.

### Deprecations

This is an advanced notice that the dedicated External OAuth, OpenID Connect (OIDC) authentication options, and SQLite support will be deprecated starting in version 5.7.0. We recommend that users of the [External OAuth]({{< ref "basic-config-and-security/security/authentication-authorization/ext-oauth-middleware" >}}) and [OpenID Connect]({{< ref "basic-config-and-security/security/authentication-authorization/openid-connect" >}}) methods migrate to Tyk's dedicated [JWT Auth]({{< ref "basic-config-and-security/security/authentication-authorization/json-web-tokens#about-jwts" >}}) method. Please review your API configurations, as the Gateway logs will provide notifications for any APIs utilizing these methods.


### Upgrade Instructions

If you are upgrading to 5.3.8, please follow the detailed [upgrade instructions](#upgrading-tyk).

### Downloads

- [Docker image to pull](https://hub.docker.com/r/tykio/tyk-gateway/tags?page=&page_size=&ordering=&name=v5.3.8)
  - ```bash
    docker pull tykio/tyk-gateway:v5.3.8
    ```
- Helm charts
  - [tyk-charts v2.0.0]({{<ref "product-stack/tyk-charts/release-notes/version-2.0.md">}})
- [Source code tarball for OSS projects](https://github.com/TykTechnologies/tyk/releases)

### Changelog {#Changelog-v5.3.8}

<!-- Required. The change log should include the following ordered set of sections below that briefly summarise the features, updates and fixed issues of the release.
Here it is important to explain the benefit of each changelog item. As mentioned by James in a previous Slack message (https://tyktech.slack.com/archives/C044R3ZTN6L/p1686812207060839?thread_ts=1686762128.651249&cid=C044R3ZTN6L):
"...it is important to document the customer impact for the work delivered, so we can share it with prospects/install base. For example:
"New Chart delivers x and y benefit to a and b customer use cases. The business impact for them will be this and that" -->

#### Added
<ul>
<li>
<details>
<summary>Deprecation notice of External OAuth and OpenID Connect options</summary>
A deprecation notice for External OAuth and OpenID Connect (OIDC) authentication mechanisms has been implemented in the Gateway logs starting from version 5.3.8. This provides advanced notification to users regarding any APIs configured with these authentication methods in preparation for future upgrades where these middleware options may be removed in version 5.7.0.
</details>
</li>
</ul>

#### Fixed

<ul>
<li>
<details>
<summary>Memory consumption reduced in Gateway for large payloads</summary>

This update fixes a bug that caused increased memory usage when proxying large response payloads that was introduced in version 5.3.1, restoring memory requirements to the levels seen in version 5.0.6. Users experiencing out-of-memory errors with 1GB+ file downloads will notice improved performance and reduced latency.
</details>
</li>
<li>
<details>
<summary>Path-based permissions in combined policies not preserved</summary>

We resolved an issue that caused path-based permissions in policies to be lost when policies were combined, potentially omitting URL values and restricting access based on the merge order. It ensures that all applicable policies merge their allowed URL access rights, regardless of the order in which they are applied.
</details>
</li>
<li>
<details>
<summary>Enhanced flexibility in Tyk OAS schema validation</summary>

A backwards compatibility issue in the way that the Gateway handles Tyk OAS API definitions has been addressed by reducing the strictness of validation against the expected schema. Since Tyk version 5.3, the Gateway has enforced strict validation, potentially causing problems for users downgrading from newer versions. With this change, Tyk customers can move between versions seamlessly, ensuring their APIs remain functional and avoiding system performance issues.
</details>
</li>
<li>
<details>
<summary>Fix for API key loss on worker Gateways due to keyspace sync interruption</summary>

This update resolves an issue where API keys could be lost if the [keyspace synchronization]({{<ref "product-stack/tyk-enterprise-mdcb/advanced-configurations/synchroniser">}}) between control and data planes was interrupted. The solution now enforces a resynchronization whenever a connection is re-established between MDCB and the data plane, ensuring key data integrity and seamless API access.
</details>
</li>
</ul>

---

## 5.3.7 Release Notes

### Release Date 22 October 2024

### Release Highlights

This patch release for Tyk Gateway addresses critical stability issues for users running Tyk Gateway within the data
plane, connecting to the control plane or Tyk Hybrid. Affected users should upgrade immediately to version 5.3.7 to
avoid service interruptions and ensure reliable operations with the control plane or Tyk Hybrid.

For a comprehensive list of changes, please refer to the detailed [changelog]({{< ref "#Changelog-v5.3.7">}}) below.

### Breaking Changes

There are no breaking changes in this release.

### Deprecations

There are no deprecations in this release.

### Upgrade Instructions

When upgrading to 5.3.7 please follow the [detailed upgrade instructions](#upgrading-tyk).

### Dependencies

<!--Required. Use this section to announce the following types of dependencies compatible with the release:
Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.
3rd party dependencies and tools -->

#### Compatibility Matrix For Tyk Components

<!-- Required. Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.
An illustrative example is shown below. -->

| Gateway Version | Recommended Releases                                               | Backwards Compatibility |
| --------------- | ------------------------------------------------------------------ | ----------------------- |
| 5.3.7           | MDCB v2.5.1                                                        | MDCB v2.5.1             |
|                 | Operator v0.17                                                     | Operator v0.16          |
|                 | Sync v1.4.3                                                        | Sync v1.4.3             |
|                 | Helm Chart (tyk-stack, tyk-oss, tyk-dashboard, tyk-gateway) v2.0.0 | Helm all versions       |
|                 | EDP v1.8.3                                                         | EDP all versions        |
|                 | Pump v1.9.0                                                        | Pump all versions       |
|                 | TIB (if using standalone) v1.5.1                                   | TIB all versions        |

#### 3rd Party Dependencies & Tools

<!-- Required. Third-party dependencies encompass tools (GoLang, Helm etc.), databases (PostgreSQL, MongoDB etc.) and external software libraries. This section should be a table that presents the third-party dependencies and tools compatible with the release. Compatible is used in the sense of those versions tested with the releases. Such information assists customers considering upgrading to a specific release.

Additionally, a disclaimer statement was added below the table, for customers to check that the third-party dependency they decide to install remains in support.

An example is given below for illustrative purposes only. Tested Versions and Compatible Versions information will require discussion with relevant squads and QA. -->

| Third Party Dependency                                        | Tested Versions | Compatible Versions | Comments                                                                                   |
| ------------------------------------------------------------- | --------------- | ------------------- | ------------------------------------------------------------------------------------------ |
| [Go](https://go.dev/dl/)                                      | 1.22            | 1.22                | [Go plugins]({{< ref "plugins/supported-languages/golang" >}}) must be built using Go 1.22 |
| [Redis](https://redis.io/download/)                           | 6.2.x, 7.x      | 6.2.x, 7.x          | Used by Tyk Gateway                                                                        |
| [OpenAPI Specification](https://spec.openapis.org/oas/v3.0.3) | v3.0.x          | v3.0.x              | Supported by [Tyk OAS]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc" >}})           |

Given the potential time difference between your upgrade and the release of this version, we recommend users verify the
ongoing support of third-party dependencies they install, as their status may have changed since the release.

### Downloads

- [Docker image to pull](https://hub.docker.com/r/tykio/tyk-gateway/tags?page=&page_size=&ordering=&name=v5.3.7)
  - ```bash
    docker pull tykio/tyk-gateway:v5.3.7
    ```
- Helm charts
  - [tyk-charts v2.0.0]({{<ref "product-stack/tyk-charts/release-notes/version-2.0.md">}})
- [Source code tarball for OSS projects](https://github.com/TykTechnologies/tyk/releases)

### Changelog {#Changelog-v5.3.7}

#### Fixed

 <!-- This section should be a bullet point list that describes the issues fixed in the release. For each fixed issue explain:
 - What problem the issue caused
 - How was the issue fixed
 - Link to (new) documentation created as a result of a fix. For example, a new configuration parameter may have been introduced and documented for the fix
 - For OSS - Link to the corresponding issue if possible on GitHub to allow the users to see further info.
 Each change log item should be expandable. The first line summarises the changelog entry. It should be then possible to expand this to reveal further details about the changelog item. This is achieved using HTML as shown in the example below. -->
 <ul>
 <li>
 <details>
 <summary>Resolved gateway panic on reconnecting to MDCB control plane or Tyk Cloud</summary>
In version 5.3.6, Tyk Gateway could encounter a panic when attempting to reconnect to the control plane after it was restarted. This patch version has resolved this issue, ensuring stable connectivity between the gateway and control plane following reconnections and reducing the need for manual intervention.
 </details>
 </li>
 </ul>

<!--
#### Security Fixes
This section should be a bullet point list that should be included when any security fixes have been made in the release, e.g. CVEs. For CVE fixes, consideration needs to be made as follows:
1. Dependency-tracked CVEs - External-tracked CVEs should be included on the release note.
2. Internal scanned CVEs - Refer to the relevant engineering and delivery policy.

For agreed CVE security fixes, provide a link to the corresponding entry on the NIST website. For example:

- Fixed the following CVEs:
    - [CVE-2022-33082](https://nvd.nist.gov/vuln/detail/CVE-2022-33082)
-->

---

## 5.3.6 Release Notes

### Release Date 04 October 2024

{{< note success >}} **Important Update**<br> <br> <b>Date</b>: 12 October 2024<br> <b>Topic</b>: Gateway panic when
reconnecting to MDCB control plane or Tyk Cloud<br> <b>Workaround</b>: Restart Gateway<br> <b>Affected Product</b>: Tyk
Gateway as an Edge Gateway<br> <b>Affected versions</b>: v5.6.0, v5.3.6, and v5.0.14<br> <b>Issue Description:</b><br>

<p>We have identified an issue affecting Tyk Gateway deployed as a data plane connecting to the Multi-Data Center Bridge (MDCB) control plane or Tyk Cloud. In the above mentioned Gateway versions a panic may occur when gateway reconnect to the control plane after the control plane is restarted.
<p>Our engineering team is actively working on a fix, and a patch (versions 5.6.1, 5.3.7, and 5.0.15) will be released soon.<br>
<b>Recommendations:</b><br>
<ul>
<li><b>For users on versions 5.5.0, 5.3.5, and 5.0.13</b><br>
We advise you to delay upgrading to the affected versions (5.6.0, 5.3.6, or 5.0.14) until the patch is available.

<li><b>For users who have already upgraded to 5.6.0, 5.3.6, or 5.0.14 and are experiencing a panic in the gateway:</b><br>
Restarting the gateway process will restore it to a healthy state. If you are operating in a *Kubernetes* environment, Tyk Gateway instance should automatically restart, which ultimately resolves the issue.<br>
</ul>
<p>We appreciate your understanding and patience as we work to resolve this. Please stay tuned for the upcoming patch release, which will address this issue.
{{< /note >}}

### Release Highlights

This release primarily focuses on bug fixes. For a comprehensive list of changes, please refer to the detailed
[changelog]({{< ref "#Changelog-v5.3.6">}}) below.

### Breaking Changes

Docker images are now based on [distroless](https://github.com/GoogleContainerTools/distroless). No shell is shipped in
the image.

If moving from an version of Tyk older than 5.3.0 please read the explanation provided with [5.3.0 release]({{< ref "#TykOAS-v5.3.0">}}).

### Deprecations

There are no deprecations in this release.

### Upgrade Instructions

When upgrading to 5.3.6 please follow the [detailed upgrade instructions](#upgrading-tyk).

### Dependencies

<!--Required. Use this section to announce the following types of dependencies compatible with the release:
Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.
3rd party dependencies and tools -->

#### Compatibility Matrix For Tyk Components

<!-- Required. Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.
An illustrative example is shown below. -->

| Gateway Version | Recommended Releases                                               | Backwards Compatibility |
| --------------- | ------------------------------------------------------------------ | ----------------------- |
| 5.3.6           | MDCB v2.5.1                                                        | MDCB v2.5.1             |
|                 | Operator v0.17                                                     | Operator v0.16          |
|                 | Sync v1.4.3                                                        | Sync v1.4.3             |
|                 | Helm Chart (tyk-stack, tyk-oss, tyk-dashboard, tyk-gateway) v2.0.0 | Helm all versions       |
|                 | EDP v1.8.3                                                         | EDP all versions        |
|                 | Pump v1.9.0                                                        | Pump all versions       |
|                 | TIB (if using standalone) v1.5.1                                   | TIB all versions        |

#### 3rd Party Dependencies & Tools

<!-- Required. Third-party dependencies encompass tools (GoLang, Helm etc.), databases (PostgreSQL, MongoDB etc.) and external software libraries. This section should be a table that presents the third-party dependencies and tools compatible with the release. Compatible is used in the sense of those versions tested with the releases. Such information assists customers considering upgrading to a specific release.

Additionally, a disclaimer statement was added below the table, for customers to check that the third-party dependency they decide to install remains in support.

An example is given below for illustrative purposes only. Tested Versions and Compatible Versions information will require discussion with relevant squads and QA. -->

| Third Party Dependency                                        | Tested Versions | Compatible Versions | Comments                                                                                   |
| ------------------------------------------------------------- | --------------- | ------------------- | ------------------------------------------------------------------------------------------ |
| [Go](https://go.dev/dl/)                                      | 1.22            | 1.22                | [Go plugins]({{< ref "plugins/supported-languages/golang" >}}) must be built using Go 1.22 |
| [Redis](https://redis.io/download/)                           | 6.2.x, 7.x      | 6.2.x, 7.x          | Used by Tyk Gateway                                                                        |
| [OpenAPI Specification](https://spec.openapis.org/oas/v3.0.3) | v3.0.x          | v3.0.x              | Supported by [Tyk OAS]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc" >}})           |

Given the potential time difference between your upgrade and the release of this version, we recommend users verify the
ongoing support of third-party dependencies they install, as their status may have changed since the release.

### Downloads

- [Docker image to pull](https://hub.docker.com/r/tykio/tyk-gateway/tags?page=&page_size=&ordering=&name=v5.3.6)
  - ```bash
    docker pull tykio/tyk-gateway:v5.3.6
    ```
- Helm charts
  - [tyk-charts v2.0.0]({{<ref "product-stack/tyk-charts/release-notes/version-2.0.md">}})
- [Source code tarball for OSS projects](https://github.com/TykTechnologies/tyk/releases)

### Changelog {#Changelog-v5.3.6}

<!-- Required. The change log should include the following ordered set of sections below that briefly summarise the features, updates and fixed issues of the release.
Here it is important to explain the benefit of each changelog item. As mentioned by James in a previous Slack message (https://tyktech.slack.com/archives/C044R3ZTN6L/p1686812207060839?thread_ts=1686762128.651249&cid=C044R3ZTN6L):
"...it is important to document the customer impact for the work delivered, so we can share it with prospects/install base. For example:
"New Chart delivers x and y benefit to a and b customer use cases. The business impact for them will be this and that" -->

#### Changed

<!-- This should be a bullet-point list of updated features. Explain:

- Why was the update necessary?
- How does the update benefit users?
- Link to documentation of the updated feature
- For OSS - Link to the corresponding issue if possible on GitHub to allow the users to see further info.

Each change log item should be expandable. The first line summarises the changelog entry. It should be then possible to expand this to reveal further details about the changelog item. This is achieved using HTML as shown in the example below. -->
<ul>
<li>
<details>
<summary>Upgrade to Go 1.22 for Tyk Gateway</summary>

The Tyk Gateway has been upgraded from Golang 1.21 to Golang 1.22, bringing enhanced performance, strengthened security,
and access to the latest features available in the new Golang release.

</details>
</li>

<li>
<details>
<summary>Introducing Distroless Containers for Tyk Gateway (2024 LTS)</summary>

In this release, we've enhanced the security of the Tyk Gateway image by changing the build process to support
[distroless](https://github.com/GoogleContainerTools/distroless) containers. This significant update addresses critical
CVEs associated with Debian, ensuring a more secure and minimal runtime environment. Distroless containers reduce the
attack surface by eliminating unnecessary packages, which bolsters the security of your deployments.

</details>
</li>

</ul>

#### Fixed

<ul>
<li>
<details>
<summary>Custom Response Plugins not working for Tyk OAS APIs</summary>

We have resolved an issue where custom [response plugins]({{< ref "plugins/plugin-types/response-plugins" >}}) were not being
triggered for Tyk OAS APIs. This fix ensures that all [supported]({{< ref "getting-started/using-oas-definitions/oas-reference" >}})
custom plugins are invoked as expected when using Tyk OAS APIs.

</details>
</li>

<li>
<details>
<summary>Data plane gateways sometimes didn't synchronise policies and APIs on start-up</summary>

We have enhanced the initial synchronization of Data Plane gateways with the Control Plane to ensure more reliable
loading of policies and APIs on start-up. A synchronous initialization process has been implemented to avoid sync
failures and reduce the risk of service disruptions caused by failed loads. This update ensures smoother and more
consistent syncing of policies and APIs in distributed deployments.

</details>
</li>

<li>
<details>
<summary>Quota wasn't respected under extreme load</summary>

We have fixed an issue where the quota limit was not being consistently respected during request spikes, especially in
deployments with multiple gateways. The problem occurred when multiple gateways cached the current and remaining quota
counters at the end of quota periods. To address this, a distributed lock mechanism has been implemented, ensuring
coordinated quota resets and preventing discrepancies across gateways.

</details>
</li>

<li>
<details>
<summary>Restored Key Creation Speed in Gateway 4.0.13 and Later</summary>

We have addressed a performance regression identified in Tyk Gateway versions 4.0.13 and later, where key creation for
policies with a large number of APIs (100+) became significantly slower. The operation, which previously took around 1.5
seconds in versions 4.0.0 to 4.0.12, was taking over 20 seconds in versions 4.0.13 and beyond. This issue has been
resolved by optimizing Redis operations during key creation, restoring the process to its expected speed of
approximately 1.5 seconds, even with a large number of APIs in the policy.

</details>
</li>
</ul>

#### Security Fixes

<!-- This section should be a bullet point list that should be included when any security fixes have been made in the release, e.g. CVEs. For CVE fixes, consideration needs to be made as follows:
1. Dependency-tracked CVEs - External-tracked CVEs should be included on the release note.
2. Internal scanned CVEs - Refer to the relevant engineering and delivery policy.

For agreed CVE security fixes, provide a link to the corresponding entry on the NIST website. For example:

- Fixed the following CVEs:
    - [CVE-2022-33082](https://nvd.nist.gov/vuln/detail/CVE-2022-33082)
-->

<ul>
<li>
<details>
<summary>High priority CVEs fixed</summary>

Fixed the following high priority CVEs identified in the Tyk Gateway, providing increased protection against security
vulnerabilities:

- [CVE-2024-6104](https://nvd.nist.gov/vuln/detail/CVE-2024-6104)
</details>
</li>
</ul>

---

## 5.3.5 Release Notes

### Release Date 26 September 2024

### Release Highlights

This release fixes some issues related to the way that Tyk performs URL path matching, introducing two new Gateway
configuration options to control path matching strictness. For a comprehensive list of changes, please refer to the
detailed [changelog]({{< ref "#Changelog-v5.3.5">}}) below.

### Breaking Changes

There are no breaking changes in this release, however if moving from an version of Tyk older than 5.3.0 please read the
explanation provided with [5.3.0 release]({{< ref "#TykOAS-v5.3.0">}}).

### Deprecations

There are no deprecations in this release.

### Upgrade Instructions

When upgrading to 5.3.5 please follow the [detailed upgrade instructions](#upgrading-tyk).

### Dependencies

<!--Required. Use this section to announce the following types of dependencies compatible with the release:
Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.
3rd party dependencies and tools -->

#### Compatibility Matrix For Tyk Components

<!-- Required. Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.
An illustrative example is shown below. -->

| Gateway Version | Recommended Releases                                               | Backwards Compatibility |
| --------------- | ------------------------------------------------------------------ | ----------------------- |
| 5.3.5           | MDCB v2.5.1                                                        | MDCB v2.5.1             |
|                 | Operator v0.17                                                     | Operator v0.16          |
|                 | Sync v1.4.3                                                        | Sync v1.4.3             |
|                 | Helm Chart (tyk-stack, tyk-oss, tyk-dashboard, tyk-gateway) v2.0.0 | Helm all versions       |
|                 | EDP v1.8.3                                                         | EDP all versions        |
|                 | Pump v1.9.0                                                        | Pump all versions       |
|                 | TIB (if using standalone) v1.5.1                                   | TIB all versions        |

#### 3rd Party Dependencies & Tools

<!-- Required. Third-party dependencies encompass tools (GoLang, Helm etc.), databases (PostgreSQL, MongoDB etc.) and external software libraries. This section should be a table that presents the third-party dependencies and tools compatible with the release. Compatible is used in the sense of those versions tested with the releases. Such information assists customers considering upgrading to a specific release.

Additionally, a disclaimer statement was added below the table, for customers to check that the third-party dependency they decide to install remains in support.

An example is given below for illustrative purposes only. Tested Versions and Compatible Versions information will require discussion with relevant squads and QA. -->

| Third Party Dependency                                        | Tested Versions       | Compatible Versions   | Comments                                                                                   |
| ------------------------------------------------------------- | --------------------- | --------------------- | ------------------------------------------------------------------------------------------ |
| [Go](https://go.dev/dl/)                                      | 1.19 (GQL), 1.21 (GW) | 1.19 (GQL), 1.21 (GW) | [Go plugins]({{< ref "plugins/supported-languages/golang" >}}) must be built using Go 1.21 |
| [Redis](https://redis.io/download/)                           | 6.2.x, 7.x            | 6.2.x, 7.x            | Used by Tyk Gateway                                                                        |
| [OpenAPI Specification](https://spec.openapis.org/oas/v3.0.3) | v3.0.x                | v3.0.x                | Supported by [Tyk OAS]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc" >}})           |

Given the potential time difference between your upgrade and the release of this version, we recommend users verify the
ongoing support of third-party dependencies they install, as their status may have changed since the release.

### Downloads

- [Docker image to pull](https://hub.docker.com/r/tykio/tyk-gateway/tags?page=&page_size=&ordering=&name=v5.3.5)
  - ```bash
    docker pull tykio/tyk-gateway:v5.3.5
    ```
- Helm charts
  - [tyk-charts v2.0.0]({{<ref "product-stack/tyk-charts/release-notes/version-2.0.md">}})
- [Source code tarball for OSS projects](https://github.com/TykTechnologies/tyk/releases)

### Changelog {#Changelog-v5.3.5}

<!-- Required. The change log should include the following ordered set of sections below that briefly summarise the features, updates and fixed issues of the release.
Here it is important to explain the benefit of each changelog item. As mentioned by James in a previous Slack message (https://tyktech.slack.com/archives/C044R3ZTN6L/p1686812207060839?thread_ts=1686762128.651249&cid=C044R3ZTN6L):
"...it is important to document the customer impact for the work delivered, so we can share it with prospects/install base. For example:
"New Chart delivers x and y benefit to a and b customer use cases. The business impact for them will be this and that" -->

#### Added

<ul>
<li>
<details>
<summary>Implemented Gateway configuration options to set URL path matching strictness</summary>

We have introduced two new options in the `http_server_options` [Gateway
configuration]({{< ref "tyk-oss-gateway/configuration#http_server_options" >}}) that will enforce prefix and/or suffix matching
when Tyk performs checks on whether middleware or other logic should be applied to a request:

- `enable_path_prefix_matching` ensures that the start of the request path must match the path defined in the API
  definition
- `enable_path_suffix_matching` ensures that the end of the request path must match the path defined in the API
  definition
- combining `enable_path_prefix_matching` and `enable_path_suffix_matching` will ensure an exact (explicit) match is
  performed

These configuration options provide control to avoid unintended matching of paths from Tyk's default _wildcard_ match.
Use of regex special characters when declaring the endpoint path in the API definition will automatically override these
settings for that endpoint. Tyk recommends that exact matching is employed, but both options default to `false` to avoid
introducing a breaking change for existing users.

The example Gateway configuration file `tyk.conf.example` has been updated to set the recommended exact matching with:

- `http_server_options.enable_path_prefix_matching = true`
- `http_server_options.enable_path_suffix_matching = true`
- `http_server_options.enable_strict_routes = true`
</details>
</li>
</ul>

#### Fixed

<ul>
<li>
<details>
<summary>Incorrectly configured regex in policy affected Path-Based Permissions authorization</summary>

Fixed an issue when using granular [Path-Based
Permissions]({{< ref "security/security-policies/secure-apis-method-path" >}}) in access policies and keys that led to authorization
incorrectly being granted to endpoints if an invalid regular expression was configured in the key/policy. Also fixed an issue
where path-based parameters were not correctly handled by Path-Based Permissions. Now Tyk's authorization check correctly
handles both of these scenarios granting access only to the expected resources.

</details>
</li>
<li>
<details>
<summary>Missing path parameter could direct to the wrong endpoint</summary>

Fixed an issue where a parameterized endpoint URL (e.g. `/user/{id}`) would be invoked if a request is made that omits
the parameter. For example, a request to `/user/` will now be interpreted as a request to `/user` and not to
`/user/{id}`.

</details>
</li>
</ul>

---

## 5.3.4 Release Notes

### Release Date August 26th 2024

### Release Highlights

Gateway 5.3.4 was version bumped only, to align with Dashboard 5.3.4. Subsequently, no changes were encountered in
release 5.3.4. For further information please see the release notes for Dashboard
[v5.3.4]({{< ref "product-stack/tyk-dashboard/release-notes/version-5.3.md" >}})

### Breaking Changes

**Attention**: Please read this section carefully.

There are no breaking changes in this release, however if moving from an version of Tyk older than 5.3.0 please read the
explanation provided with [5.3.0 release]({{< ref "#TykOAS-v5.3.0">}}).

### Deprecations

There are no deprecations in this release.

### Upgrade Instructions

When upgrading to 5.3.4 please follow the [detailed upgrade instructions](#upgrading-tyk).

### Dependencies

<!--Required. Use this section to announce the following types of dependencies compatible with the release:
Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.
3rd party dependencies and tools -->

#### Compatibility Matrix For Tyk Components

<!-- Required. Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.
An illustrative example is shown below. -->

| Gateway Version | Recommended Releases                                               | Backwards Compatibility |
| --------------- | ------------------------------------------------------------------ | ----------------------- |
| 5.3.4           | MDCB v2.5.1                                                        | MDCB v2.5.1             |
|                 | Operator v0.17                                                     | Operator v0.16          |
|                 | Sync v1.4.3                                                        | Sync v1.4.3             |
|                 | Helm Chart (tyk-stack, tyk-oss, tyk-dashboard, tyk-gateway) v1.4.0 | Helm all versions       |
|                 | EDP v1.8.3                                                         | EDP all versions        |
|                 | Pump v1.9.0                                                        | Pump all versions       |
|                 | TIB (if using standalone) v1.5.1                                   | TIB all versions        |

#### 3rd Party Dependencies & Tools

<!-- Required. Third-party dependencies encompass tools (GoLang, Helm etc.), databases (PostgreSQL, MongoDB etc.) and external software libraries. This section should be a table that presents the third-party dependencies and tools compatible with the release. Compatible is used in the sense of those versions tested with the releases. Such information assists customers considering upgrading to a specific release.

Additionally, a disclaimer statement was added below the table, for customers to check that the third-party dependency they decide to install remains in support.

An example is given below for illustrative purposes only. Tested Versions and Compatible Versions information will require discussion with relevant squads and QA. -->

| Third Party Dependency                                        | Tested Versions       | Compatible Versions   | Comments                                                                                   |
| ------------------------------------------------------------- | --------------------- | --------------------- | ------------------------------------------------------------------------------------------ |
| [Go](https://go.dev/dl/)                                      | 1.19 (GQL), 1.21 (GW) | 1.19 (GQL), 1.21 (GW) | [Go plugins]({{< ref "plugins/supported-languages/golang" >}}) must be built using Go 1.21 |
| [Redis](https://redis.io/download/)                           | 6.2.x, 7.x            | 6.2.x, 7.x            | Used by Tyk Gateway                                                                        |
| [OpenAPI Specification](https://spec.openapis.org/oas/v3.0.3) | v3.0.x                | v3.0.x                | Supported by [Tyk OAS]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc" >}})           |

Given the potential time difference between your upgrade and the release of this version, we recommend users verify the
ongoing support of third-party dependencies they install, as their status may have changed since the release.

### Downloads

- [Docker image to pull](https://hub.docker.com/r/tykio/tyk-gateway/tags?page=&page_size=&ordering=&name=v5.3.4)
  - ```bash
    docker pull tykio/tyk-gateway:v5.3.4
    ```
- Helm charts
  - [tyk-charts v1.4]({{< ref "product-stack/tyk-charts/release-notes/version-1.4.md" >}})
- [Source code tarball for OSS projects](https://github.com/TykTechnologies/tyk/releases)

### Changelog {#Changelog-v5.3.4}

Since this release was version bumped only to align with Dashboard v5.3.4, no changes were encountered in this release.

---

## 5.3.3 Release Notes

### Release Date August 2nd 2024

### Breaking Changes

**Attention**: Please read this section carefully.

There are no breaking changes in this release, however if moving from an version of Tyk older than 5.3.0 please read the
explanation provided with [5.3.0 release]({{< ref "#TykOAS-v5.3.0">}}).

### Deprecations

There are no deprecations in this release.

### Upgrade Instructions

When upgrading to 5.3.3 please follow the [detailed upgrade instructions](#upgrading-tyk).

### Release Highlights

#### Bug Fixes

This release primarily focuses on bug fixes. For a comprehensive list of changes, please refer to the detailed
[changelog]({{< ref "#Changelog-v5.3.3">}}) below.

#### FIPS Compliance

Tyk Gateway now offers [FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) compliance. For further
details please consult [Tyk API Management
FIPS support]({{< ref "developer-support/special-releases-and-features/fips-release" >}}).

### Dependencies

<!--Required. Use this section to announce the following types of dependencies compatible with the release:
Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.
3rd party dependencies and tools -->

#### Compatibility Matrix For Tyk Components

<!-- Required. Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.
An illustrative example is shown below. -->

| Gateway Version | Recommended Releases                                               | Backwards Compatibility |
| --------------- | ------------------------------------------------------------------ | ----------------------- |
| 5.3.3           | MDCB v2.5.1                                                        | MDCB v2.5.1             |
|                 | Operator v0.17                                                     | Operator v0.16          |
|                 | Sync v1.4.3                                                        | Sync v1.4.3             |
|                 | Helm Chart (tyk-stack, tyk-oss, tyk-dashboard, tyk-gateway) v1.4.0 | Helm all versions       |
|                 | EDP v1.8.3                                                         | EDP all versions        |
|                 | Pump v1.9.0                                                        | Pump all versions       |
|                 | TIB (if using standalone) v1.5.1                                   | TIB all versions        |

#### 3rd Party Dependencies & Tools

<!-- Required. Third-party dependencies encompass tools (GoLang, Helm etc.), databases (PostgreSQL, MongoDB etc.) and external software libraries. This section should be a table that presents the third-party dependencies and tools compatible with the release. Compatible is used in the sense of those versions tested with the releases. Such information assists customers considering upgrading to a specific release.

Additionally, a disclaimer statement was added below the table, for customers to check that the third-party dependency they decide to install remains in support.

An example is given below for illustrative purposes only. Tested Versions and Compatible Versions information will require discussion with relevant squads and QA. -->

| Third Party Dependency                                        | Tested Versions       | Compatible Versions   | Comments                                                                                   |
| ------------------------------------------------------------- | --------------------- | --------------------- | ------------------------------------------------------------------------------------------ |
| [Go](https://go.dev/dl/)                                      | 1.19 (GQL), 1.21 (GW) | 1.19 (GQL), 1.21 (GW) | [Go plugins]({{< ref "plugins/supported-languages/golang" >}}) must be built using Go 1.21 |
| [Redis](https://redis.io/download/)                           | 6.2.x, 7.x            | 6.2.x, 7.x            | Used by Tyk Gateway                                                                        |
| [OpenAPI Specification](https://spec.openapis.org/oas/v3.0.3) | v3.0.x                | v3.0.x                | Supported by [Tyk OAS]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc" >}})           |

Given the potential time difference between your upgrade and the release of this version, we recommend users verify the
ongoing support of third-party dependencies they install, as their status may have changed since the release.

### Downloads

- [Docker image to pull](https://hub.docker.com/r/tykio/tyk-gateway/tags?page=&page_size=&ordering=&name=v5.3.3)
  - ```bash
    docker pull tykio/tyk-gateway:v5.3.3
    ```
- Helm charts
  - [tyk-charts v1.4]({{< ref "product-stack/tyk-charts/release-notes/version-1.4.md" >}})
- [Source code tarball for OSS projects](https://github.com/TykTechnologies/tyk/releases)

### Changelog {#Changelog-v5.3.3}

<!-- Required. The change log should include the following ordered set of sections below that briefly summarise the features, updates and fixed issues of the release.
Here it is important to explain the benefit of each changelog item. As mentioned by James in a previous Slack message (https://tyktech.slack.com/archives/C044R3ZTN6L/p1686812207060839?thread_ts=1686762128.651249&cid=C044R3ZTN6L):
"...it is important to document the customer impact for the work delivered, so we can share it with prospects/install base. For example:
"New Chart delivers x and y benefit to a and b customer use cases. The business impact for them will be this and that" -->

#### Added

<!-- This section should be a bullet point list of new features. Explain:

- The purpose of the new feature
- How does the new feature benefit users?
- Link to documentation of the new feature
- For OSS - Link to the corresponding issue if possible on GitHub to allow the users to see further info.

Each change log item should be expandable. The first line summarises the changelog entry. It should be then possible to expand this to reveal further details about the changelog item. This is achieved using HTML as shown in the example below. -->
<ul>
<li>
<details>
<summary>Added FIPS compliance</summary>

Added [FIPS compliance]({{< ref "developer-support/special-releases-and-features/fips-release" >}}) for Tyk Gateway.

</details>
</li>

<li>
<details>
<summary>Corrected ordering of OAS API paths to prevent middleware misapplication</summary>

Fixed an issue where nested API endpoints, such as '/test' and '/test/abc', might incorrectly apply middleware from the
parent path to the nested path. The fix ensures that API endpoint definitions are correctly ordered, preventing this
middleware misapplication and ensuring both the HTTP method and URL match accurately.

</details>
</li>
</ul>

---

#### Fixed

<!-- This section should be a bullet point list that describes the issues fixed in the release. For each fixed issue explain:
- What problem the issue caused
- How was the issue fixed
- Link to (new) documentation created as a result of a fix. For example, a new configuration parameter may have been introduced and documented for the fix
- For OSS - Link to the corresponding issue if possible on GitHub to allow the users to see further info.
Each change log item should be expandable. The first line summarises the changelog entry. It should be then possible to expand this to reveal further details about the changelog item. This is achieved using HTML as shown in the example below. -->
<ul>
<li>
 <details>
 <summary>Optimised key creation to reduce redundant Redis commands</summary>

Addressed an issue where creating or resetting a key caused an exponential number of Redis DeleteRawKey commands.
Previously, the key creation sequence repeated for every API in the access list, leading to excessive deletion events,
especially problematic for access lists with over 100 entries. Now, the key creation sequence executes only once, and
redundant deletion of non-existent keys in Redis has been eliminated, significantly improving performance and stability
for larger access lists.

</details>
</li>
<li>
<details>
<summary>Resolved SSE streaming issue</summary>

Fixed a bug that caused Server Side Event (SSE) streaming responses to be considered for caching, which required
buffering the response and prevented SSE from being correctly proxied.

</details>
</li>
<li>
 <details>
 <summary>Fixed Analytics Latency Reporting for MDCB Setups</summary>

Resolved an issue where Host and Latency fields (Total and Upstream) were not correctly reported for edge gateways in
MDCB setups. The fix ensures accurate Host values and Latency measurements are now captured and displayed in analytics
data.

</details>
</li>
</ul>

---

## 5.3.2 Release Notes

### Release Date 5th June 2024

### Breaking Changes

**Attention**: Please read this section carefully.

There are no breaking changes in this release, however if moving from an version of Tyk older than 5.3.0 please read the
explanation provided with [5.3.0 release]({{< ref "#TykOAS-v5.3.0">}}).

### Deprecations

There are no deprecations in this release.

### Upgrade Instructions

When upgrading to 5.3.2 please follow the [detailed upgrade instructions](#upgrading-tyk).

### Release Highlights

This release primarily focuses on bug fixes. For a comprehensive list of changes, please refer to the detailed
[changelog]({{< ref "#Changelog-v5.3.2">}}) below.

### Dependencies

<!--Required. Use this section to announce the following types of dependencies compatible with the release:
Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.
3rd party dependencies and tools -->

#### Compatibility Matrix For Tyk Components

<!-- Required. Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.
An illustrative example is shown below. -->

| Gateway Version | Recommended Releases                                               | Backwards Compatibility |
| --------------- | ------------------------------------------------------------------ | ----------------------- |
| 5.3.2           | MDCB v2.5.1                                                        | MDCB v2.5.1             |
|                 | Operator v0.17                                                     | Operator v0.16          |
|                 | Sync v1.4.3                                                        | Sync v1.4.3             |
|                 | Helm Chart (tyk-stack, tyk-oss, tyk-dashboard, tyk-gateway) v1.4.0 | Helm all versions       |
|                 | EDP v1.8.3                                                         | EDP all versions        |
|                 | Pump v1.9.0                                                        | Pump all versions       |
|                 | TIB (if using standalone) v1.5.1                                   | TIB all versions        |

#### 3rd Party Dependencies & Tools

<!-- Required. Third-party dependencies encompass tools (GoLang, Helm etc.), databases (PostgreSQL, MongoDB etc.) and external software libraries. This section should be a table that presents the third-party dependencies and tools compatible with the release. Compatible is used in the sense of those versions tested with the releases. Such information assists customers considering upgrading to a specific release.

Additionally, a disclaimer statement was added below the table, for customers to check that the third-party dependency they decide to install remains in support.

An example is given below for illustrative purposes only. Tested Versions and Compatible Versions information will require discussion with relevant squads and QA. -->

| Third Party Dependency                                        | Tested Versions       | Compatible Versions   | Comments                                                                                   |
| ------------------------------------------------------------- | --------------------- | --------------------- | ------------------------------------------------------------------------------------------ |
| [Go](https://go.dev/dl/)                                      | 1.19 (GQL), 1.21 (GW) | 1.19 (GQL), 1.21 (GW) | [Go plugins]({{< ref "plugins/supported-languages/golang" >}}) must be built using Go 1.21 |
| [Redis](https://redis.io/download/)                           | 6.2.x, 7.x            | 6.2.x, 7.x            | Used by Tyk Gateway                                                                        |
| [OpenAPI Specification](https://spec.openapis.org/oas/v3.0.3) | v3.0.x                | v3.0.x                | Supported by [Tyk OAS]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc" >}})           |

Given the potential time difference between your upgrade and the release of this version, we recommend users verify the
ongoing support of third-party dependencies they install, as their status may have changed since the release.

### Downloads

- [Docker image to pull](https://hub.docker.com/r/tykio/tyk-gateway/tags?page=&page_size=&ordering=&name=v5.3.2)
  - ```bash
    docker pull tykio/tyk-gateway:v5.3.2
    ```
- Helm charts
  - [tyk-charts v1.4]({{< ref "product-stack/tyk-charts/release-notes/version-1.4.md" >}})
- [Source code tarball for OSS projects](https://github.com/TykTechnologies/tyk/releases)

### Changelog {#Changelog-v5.3.2}

<!-- Required. The change log should include the following ordered set of sections below that briefly summarise the features, updates and fixed issues of the release.
Here it is important to explain the benefit of each changelog item. As mentioned by James in a previous Slack message (https://tyktech.slack.com/archives/C044R3ZTN6L/p1686812207060839?thread_ts=1686762128.651249&cid=C044R3ZTN6L):
"...it is important to document the customer impact for the work delivered, so we can share it with prospects/install base. For example:
"New Chart delivers x and y benefit to a and b customer use cases. The business impact for them will be this and that" -->

#### Fixed

<!-- This section should be a bullet point list that describes the issues fixed in the release. For each fixed issue explain:
- What problem the issue caused
- How was the issue fixed
- Link to (new) documentation created as a result of a fix. For example, a new configuration parameter may have been introduced and documented for the fix
- For OSS - Link to the corresponding issue if possible on GitHub to allow the users to see further info.
Each change log item should be expandable. The first line summarises the changelog entry. It should be then possible to expand this to reveal further details about the changelog item. This is achieved using HTML as shown in the example below. -->
<ul>
<li>
 <details>
 <summary>Remove sensitive information leaked from OpenTelemetry traces</summary>

In Gateway version 5.2+ and 5.3+, we discovered a bug within the OpenTelemetry tracing feature that inadvertently
transmits sensitive information. Specifically, `tyk.api.apikey` and `tyk.api.oauthid` attributes were exposing API keys.
We have fixed the issue to ensure that only the hashed version of the API key is transmitted in traces.

 </details>
</li>
<li>
<details>
<summary>APIs with common listen paths but different custom domains</summary>

Addressed an issue where an API with a custom domain might not be invoked if another API with the same listen path but
no custom domain was also deployed on the Gateway. Now APIs with custom domain names are loaded first, so requests will
be checked against these first before falling back to APIs without custom domains.

</details>
</li>
<li>
<details>
<summary>Gateway service discovery issue with consul</summary>

Addressed an issue in service discovery where an IP:port returned by Consul wasn't parsed correctly on the Gateway side,
leading to errors when proxying requests to the service. The issue primarily occurred with IP:port responses, while
valid domain names were unaffected.

</details>
</li>
<li>
<details>
<summary>Resolved Universal Data Graph Nested Field Mapping Issue</summary>

Fixed an issue with nested field mapping in UDG when used with GraphQL (GQL) operations for a field's data source.
Previously, querying only the mentioned field resulted in an error, but querying alongside another 'normal' field from
the same level worked without issue.

</details>
</li>
<li>
<details>
<summary>Added control over access to context variables from middleware when using Tyk OAS APIs</summary>

Addressed a potential issue when working with Tyk OAS APIs where request context variables are automatically made
available to relevant Tyk and custom middleware. We have introduced a control in the Tyk OAS API definition to disable
this access if required.

</details>
</li>
</ul>

---

## 5.3.1 Release Notes

### Release Date 24 April 2024

### Breaking Changes

**Attention**: Please read this section carefully.

There are no breaking changes in this release, however if moving from an version of Tyk older than 5.3.0 please read the
explanation provided with [5.3.0 release]({{< ref "#TykOAS-v5.3.0">}}).

### Deprecations

There are no deprecations in this release.

### Upgrade Instructions

When upgrading to 5.3.1 please follow the [detailed upgrade instructions](#upgrading-tyk).

### Release Highlights

This release primarily focuses on bug fixes. For a comprehensive list of changes, please refer to the detailed
[changelog]({{< ref "#Changelog-v5.3.1">}}) below.

### Dependencies

<!--Required. Use this section to announce the following types of dependencies compatible with the release:

Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.

3rd party dependencies and tools -->

#### Compatibility Matrix For Tyk Components

<!-- Required. Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.
An illustrative example is shown below. -->

| Gateway Version | Recommended Releases                                               | Backwards Compatibility |
| --------------- | ------------------------------------------------------------------ | ----------------------- |
| 5.3.1           | MDCB v2.5.1                                                        | MDCB v2.5.1             |
|                 | Operator v0.17                                                     | Operator v0.16          |
|                 | Sync v1.4.3                                                        | Sync v1.4.3             |
|                 | Helm Chart (tyk-stack, tyk-oss, tyk-dashboard, tyk-gateway) v1.3.0 | Helm all versions       |
|                 | EDP v1.8.3                                                         | EDP all versions        |
|                 | Pump v1.9.0                                                        | Pump all versions       |
|                 | TIB (if using standalone) v1.5.1                                   | TIB all versions        |

#### 3rd Party Dependencies & Tools

<!-- Required. Third-party dependencies encompass tools (GoLang, Helm etc.), databases (PostgreSQL, MongoDB etc.) and external software libraries. This section should be a table that presents the third-party dependencies and tools compatible with the release. Compatible is used in the sense of those versions tested with the releases. Such information assists customers considering upgrading to a specific release.

Additionally, a disclaimer statement was added below the table, for customers to check that the third-party dependency they decide to install remains in support.

An example is given below for illustrative purposes only. Tested Versions and Compatible Versions information will require discussion with relevant squads and QA. -->

| Third Party Dependency                                        | Tested Versions       | Compatible Versions   | Comments                                                                                   |
| ------------------------------------------------------------- | --------------------- | --------------------- | ------------------------------------------------------------------------------------------ |
| [Go](https://go.dev/dl/)                                      | 1.19 (GQL), 1.21 (GW) | 1.19 (GQL), 1.21 (GW) | [Go plugins]({{< ref "plugins/supported-languages/golang" >}}) must be built using Go 1.21 |
| [Redis](https://redis.io/download/)                           | 6.2.x, 7.x            | 6.2.x, 7.x            | Used by Tyk Gateway                                                                        |
| [OpenAPI Specification](https://spec.openapis.org/oas/v3.0.3) | v3.0.x                | v3.0.x                | Supported by [Tyk OAS]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc" >}})           |

Given the potential time difference between your upgrade and the release of this version, we recommend users verify the
ongoing support of third-party dependencies they install, as their status may have changed since the release.

### Downloads

- [Docker image to pull](https://hub.docker.com/r/tykio/tyk-gateway/tags?page=&page_size=&ordering=&name=v5.3.1)
  - ```bash
    docker pull tykio/tyk-gateway:v5.3.1
    ```
- Helm charts
  - [tyk-charts v1.3]({{< ref "product-stack/tyk-charts/release-notes/version-1.3.md" >}})
- [Source code tarball for OSS projects](https://github.com/TykTechnologies/tyk/releases)

### Changelog {#Changelog-v5.3.1}

#### Fixed

<ul>
<li>
<details>
<summary>Improved security: don't load APIs into Gateway if custom plugin bundle fails to load</summary>

Issues were addressed where Tyk failed to properly reject custom plugin bundles with signature verification failures,
allowing APIs to load without necessary plugins, potentially exposing upstream services. With the fix, if the plugin
bundle fails to load (for example, due to failed signature verification) the API will not be loaded and an error will be
logged in the Gateway.

</details>
</li>
<li>
<details>
<summary>Stability: fixed a Gateway panic that could occur when using custom JavaScript plugins with the Ignore Authentication middleware</summary>

Fixed a panic scenario that occurred when a custom JavaScript plugin that requests access to the session metadata
(`require_session:true`) is assigned to the same endpoint as the Ignore Authentication middleware. While the custom
plugin expects access to a valid session, the configuration flag doesn't guarantee its presence, only that it's passed
if available. As such, the custom plugin should be coded to verify that the session metadata is present before
attempting to use it.

</details>
</li>
<li>
<details>
<summary>Stability: Gateway could crash when custom Python plugins attempted to access storage</summary>

Fixed a bug where the Gateway could crash when using custom Python plugins that access the Redis storage. The Tyk Python
API methods `store_data` and `get_data` could fail due to connection issues with the Redis. With this fix, the Redis
connection will be created if required, avoiding the crash.

</details>
</li>
<li>
<details>
<summary>Stability: Gateway panics when arguments are missing in persist GraphQL endpoints</summary>

In some instances users were noticing gateway panics when using the **Persist GQL** middleware without arguments
defined. This issue has been fixed and the gateway will not throw panics in these cases anymore.

</details>
</li>
<li>
<details>
<summary>Missing GraphQL OTel attributes in spans when requests fail validation</summary>

In cases where `detailed_tracing` was set to `false` and the client was sending a malformed request to a GraphQL API,
the traces were missing GraphQL attributes (operation name, type and document). This has been corrected and debugging
GraphQL with OTel will be easier for users.

</details>
</li>
<li>
<details>
<summary>Incorrect naming for semantic conventions attributes in GQL spans</summary>

GQL Open Telemetry semantic conventions attribute names were missing `graphql` prefix and therefore were not in line
with the community standard. This has been fixed and all attributes have the correct prefix.

</details>
</li>
<li>
<details>
<summary>URL Rewrite middleware did not always correctly observe quotas for requests using keys created from policies</summary>

Fixed two bugs in the handling of usage quotas by the URL rewrite middleware when it was configured to rewrite to itself
(e.g. to `tyk://self`). Quota limits were not observed and the quota related response headers always contained `0`.

</details>
</li>
<li>
<details>
<summary>Tyk Dashboard License Statistics page could display incorrect number of data plane gateways</summary>

Resolved an issue in distributed deployments where the MDCB data plane gateway counter was inaccurately incremented when
a Gateway was stopped and restarted.

</details>
</li>
<li>
<details>
<summary>Unable to clear the API cache in distributed data plane Gateways from the control plane Dashboard</summary>

Addressed a bug where clearing the API cache from the Tyk Dashboard failed to invalidate the cache in distributed data
plane gateways. This fix requires MDCB 2.5.1.

</details>
</li>
<li>
<details>
<summary>Unable to load custom Go plugins compiled in RHEL 8</summary>

Fixed a bug where custom Go plugins compiled in RHEL8 environments were unable to load into Tyk Gateway due to a
discrepancy in base images between the Gateway and Plugin Compiler environments. This fix aligns the plugin compiler
base image with the gateway build environment, enabling seamless plugin functionality on RHEL8 environments.

</details>
</li>
<li>
<details>
<summary>Removed unused packages from plugin compiler image</summary>

Removed several unused packages from the plugin compiler image. The packages include: docker, buildkit, ruc, sqlite,
curl, wget, and other build tooling. The removal was done in order to address invalid CVE reporting, none of the removed
dependencies are used to provide plugin compiler functionality.

</details>
</li>
</ul>

---

## 5.3.0 Release Notes

### Release Date 5 April 2024

### Breaking Changes

<!-- Required. Use the following statement if there are no breaking changes, or explain if there are -->

**Attention: Please read this section carefully**

#### Tyk OAS APIs Compatibility Caveats - Tyk OSS {#TykOAS-v5.3.0}

This upgrade transitions Tyk OAS APIs out of [Early Access]({{< ref "developer-support/special-releases-and-features/early-access-features" >}}).

For licensed deployments (Tyk Cloud, Self Managed including MDCB), please refer to the [release notes of Tyk Dashboard 5.3.0]({{<ref "product-stack/tyk-dashboard/release-notes/version-5.3.md">}}).

- **Out of Early Access**
  - This means that from now on, all Tyk OAS APIs will be backwards compatible and in case of a downgrade from v5.3.X to
    v5.3.0, the Tyk OAS API definitions will always work.
- **Not Backwards Compatible**
  - Tyk OAS APIs in Tyk Gateway v5.3.0 are not [backwards compatible](https://tinyurl.com/3xy966xn). This means that the
    new Tyk OAS API format created by Tyk Gateway v5.3.X does not work with older versions of Tyk Gateway, i.e. you
    cannot export these API definitions from a v5.3.X Tyk Gateway and import them to an earlier version.
  - The upgrade is **not reversible**, i.e. you cannot use version 5.3.X Tyk OAS API definitions with an older version
    of Tyk Dashboard.
  - This means that if you wish to downgrade or revert to your previous version of Tyk, you will need to restore these
    API definitions from a backup. Please go to the [backup]({{< ref "#upgrade-instructions" >}}) section for detailed
    instructions on backup before upgrading to v5.3.0.
  - If you are not using Tyk OAS APIs, Tyk will maintain backward compatibility standards.
- **Not Forward Compatible**
  - Tyk OAS API Definitions prior to v5.3.0 are not [forward compatible](https://tinyurl.com/t3zz88ep) with Tyk Gateway
    v5.3.X.
  - This means that any Tyk OAS APIs created in any previous release (4.1.0-5.2.x) cannot work with the new Tyk Gateway
    v5.3.X without being migrated to its [latest format]({{<ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc">}}).
- **After upgrade (the good news)**
  - Tyk OAS API definitions that are part of the file system **are not automatically converted** to the [new
    format]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc" >}}). Subsequently, users will have to manually update their
    OAS API Definitions to the new format.
  - If users upgrade to 5.3.0, create new Tyk OAS APIs and then decide to rollback then the upgrade is non-reversible.
    Reverting to your previous version requires restoring from a backup.

**Important:** Please go to the [backup]({{< ref "#upgrade-instructions" >}}) section for detailed instructions on
backup before upgrading to v5.3.0

#### Python plugin support

Starting from Tyk Gateway version v5.3.0, Python is no longer bundled with the official Tyk Gateway Docker image to
reduce exposure to security vulnerabilities in the Python libraries.

Whilst the Gateway still supports Python plugins, you must [extend
the image]({{< ref "plugins/supported-languages/rich-plugins/python/python#install-the-python-development-packages" >}})
to add the language support.

<!-- The following "Changed error log messages" section is Optional!
Instructions: We should mention in the changelog section ALL changes in our application log messages. In case we made such changes, this section should also be added, to make sure the users don't miss this notice among other changelog lines. -->
<!-- #### Changed error log messages
Important for users who monitor Tyk components using the application logs (i.e. Tyk Gateway log, Tyk Dashboard log etc.).
We try to avoid making changes to our log messages, especially at error and critical levels. However, sometimes it's necessary. Please find the list of changes made to the application log in this release: -->

<!-- The following "|Planned Breaking Changes" section is optional!
Announce future scheduled breaking changes, e.g. Go version updates, DB driver updates etc. -->
<!-- #### Planned Breaking Changes -->

### Dependencies {#dependencies-5.3.0}

<!--Required. Use this section to announce the following types of dependencies compatible with the release:

Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.

3rd party dependencies and tools -->

#### Compatibility Matrix For Tyk Components

<!-- Required. Version compatibility with other components in the Tyk stack. This takes the form of a compatibility matrix and is only required for Gateway and Portal.
An illustrative example is shown below. -->

| Gateway Version | Recommended Releases                                               | Backwards Compatibility |
| --------------- | ------------------------------------------------------------------ | ----------------------- |
| 5.3.0           | MDCB v2.5                                                          | MDCB v2.4.2             |
|                 | Operator v0.17                                                     | Operator v0.16          |
|                 | Sync v1.4.3                                                        | Sync v1.4.3             |
|                 | Helm Chart (tyk-stack, tyk-oss, tyk-dashboard, tyk-gateway) v1.3.0 | Helm all versions       |
|                 | EDP v1.8.3                                                         | EDP all versions        |
|                 | Pump v1.9.0                                                        | Pump all versions       |
|                 | TIB (if using standalone) v1.5.1                                   | TIB all versions        |

#### 3rd Party Dependencies & Tools

<!-- Required. Third-party dependencies encompass tools (GoLang, Helm etc.), databases (PostgreSQL, MongoDB etc.) and external software libraries. This section should be a table that presents the third-party dependencies and tools compatible with the release. Compatible is used in the sense of those versions tested with the releases. Such information assists customers considering upgrading to a specific release.

Additionally, a disclaimer statement was added below the table, for customers to check that the third-party dependency they decide to install remains in support.

An example is given below for illustrative purposes only. Tested Versions and Compatible Versions information will require discussion with relevant squads and QA. -->

| Third Party Dependency                                        | Tested Versions       | Compatible Versions   | Comments                                                                                   |
| ------------------------------------------------------------- | --------------------- | --------------------- | ------------------------------------------------------------------------------------------ |
| [Go](https://go.dev/dl/)                                      | 1.19 (GQL), 1.21 (GW) | 1.19 (GQL), 1.21 (GW) | [Go plugins]({{< ref "plugins/supported-languages/golang" >}}) must be built using Go 1.21 |
| [Redis](https://redis.io/download/)                           | 6.2.x, 7.x            | 6.2.x, 7.x            | Used by Tyk Gateway                                                                        |
| [OpenAPI Specification](https://spec.openapis.org/oas/v3.0.3) | v3.0.x                | v3.0.x                | Supported by [Tyk OAS]({{< ref "tyk-apis/tyk-gateway-api/oas/x-tyk-oas-doc" >}})           |

Given the potential time difference between your upgrade and the release of this version, we recommend users verify the
ongoing support of third-party dependencies they install, as their status may have changed since the release.

### Deprecations

<!-- Required. Use the following statement if there are no deprecations, or explain if there are -->

In 5.3.0, we have simplified the configuration of response transform middleware. We encourage users to embrace the
`global_headers` mechanism as the `response_processors.header_injector` is now an optional setting and will be removed
in a future release.

<!-- Optional section!
Used to share and notify users about our plan to deprecate features, configs etc.
Once you put an item in this section, we must keep this item listed in all the following releases till the deprecation happens. -->
<!-- ##### Future deprecations
-->

### Upgrade instructions {#upgrade-5.3.0}

If you are upgrading to 5.3.0, please follow the detailed [upgrade instructions](#upgrading-tyk).

**The following steps are essential to follow before upgrading** Tyk Cloud (including Hybrid Gateways) and Self Managed
users - Please refer to the [release notes of Tyk Dashboard 5.3.0]({{<ref "product-stack/tyk-dashboard/release-notes/version-5.3.md">}}).

For OSS deployments -

1. Backup Your environment using the [usual guidance]({{<ref "upgrading-tyk">}}) documented with every release (this includes
   backup config file and database).
2. Backup all your API definitions (Tyk OAS API and Classic Definitions) by saving your API and policy files or by
   exporting them using the `GET /tyk/apis` and `Get /tyk/policies`
3. Performing the upgrade - follow the instructions in the [upgrade
   guide]({{<ref "upgrading-tyk">}}) when upgrading Tyk.

### Release Highlights

<!-- Required. Use similar ToV to previous release notes. For example for a patch release:
This release primarily focuses on bug fixes.
For a comprehensive list of changes, please refer to the detailed [changelog]({{< ref "#Changelog-vX.Y.0">}}) below.
-->

We’re thrilled to announce the release of 5.3.0, an update packed with exciting features and significant fixes to
elevate your experience with Tyk Gateway. For a comprehensive list of changes, please refer to the detailed
[changelog](#Changelog-v5.3.0) below.

#### Tyk OAS Feature Maturity

Tyk OAS is now out of [Early
Access]({{< ref "developer-support/special-releases-and-features/early-access-features" >}}) as we have reached feature maturity.
You are now able to make use of the majority of Tyk Gateway's features from your Tyk OAS APIs, so they are a credible alternative
to the legacy Tyk Classic APIs.

From Tyk 5.3.0 we support the following features when using Tyk OAS APIs with Tyk Gateway:

- Security

  - All Tyk-supported client-gateway authentication methods including custom auth plugins
  - Automatic configuration of authentication from the OpenAPI description
  - Gateway-upstream mTLS
  - CORS

- API-level (global) middleware including:

  - Response caching
  - Custom plugins for PreAuth, Auth, PostAuth, Post and Response hooks
  - API-level rate limits
  - Request transformation - headers
  - Response transformation - headers
  - Service discovery
  - Internal API

- Endpoint-level (per-path) middleware including:

  - Request validation - headers and body (automatically configurable from the OpenAPI description)
  - Request transformation - method, headers and body
  - Response transformation - headers and body
  - URL rewrite and internal endpoints
  - Mock responses (automatically configurable from the OpenAPI description)
  - Response caching
  - Custom Go Post-Plugin
  - Request size limit
  - Virtual endpoint
  - Allow and block listing
  - Do-not-track
  - Circuit breakers
  - Enforced timeouts
  - Ignore authentication

- Observability

  - Open Telemetry tracing
  - Detailed log recording (include payload in the logs)
  - Do-not-track endpoint

- Governance
  - API Versioning

#### Enhanced KV storage of API Definition Fields

Tyk is able to store configuration data from the API definition in KV systems, such as Vault and Consul, and then
reference these values during configuration of the Tyk Gateway or APIs deployed on the Gateway. Previously this was
limited to the Target URL and Listen Path but from 5.3.0 you are able to store any `string` type field from your API
definition, unlocking the ability to store sensitive information in a centralised location. For full details check out
the [documentation]({{< ref "tyk-configuration-reference/kv-store" >}}) of this powerful feature.

#### Redis v7.x Compatibility

We have upgraded Redis driver [go-redis](https://github.com/redis/go-redis) to v9. Subsequently, Tyk 5.3 is compatible
with Redis v7.x.

#### Gateway and Component Upgrades

We've raised the bar with significant upgrades to our Gateway and components. Leveraging the power and security of Go
1.21, upgrading Sarama to version 1.41.0 and enhancing the GQL engine with Go version 1.19, we ensure improved
functionality and performance to support your evolving needs seamlessly.

### Downloads

- [Docker image to pull](https://hub.docker.com/r/tykio/tyk-gateway/tags?page=&page_size=&ordering=&name=v5.3.0)
  - ```bash
    docker pull tykio/tyk-gateway:v5.3.0
    ```
- Helm charts
  - [tyk-charts v1.3]({{< ref "product-stack/tyk-charts/release-notes/version-1.3.md" >}})
- [Source code tarball for OSS projects](https://github.com/TykTechnologies/tyk/releases)

### Changelog {#Changelog-v5.3.0}

<!-- Required. The change log should include the following ordered set of sections below that briefly summarise the features, updates and fixed issues of the release.

Here it is important to explain the benefit of each changelog item. As mentioned by James in a previous Slack message (https://tyktech.slack.com/archives/C044R3ZTN6L/p1686812207060839?thread_ts=1686762128.651249&cid=C044R3ZTN6L):
"...it is important to document the customer impact for the work delivered, so we can share it with prospects/install base. For example:
"New Chart delivers x and y benefit to a and b customer use cases. The business impact for them will be this and that" -->

#### Added

<!-- This section should be a bullet point list of new features. Explain:

- The purpose of the new feature
- How does the new feature benefit users?
- Link to documentation of the new feature
- For OSS - Link to the corresponding issue if possible on GitHub to allow the users to see further info.

Each change log item should be expandable. The first line summarises the changelog entry. It should be then possible to expand this to reveal further details about the changelog item. This is achieved using HTML as shown in the example below. -->
<ul>
<li>
<details>
<summary>Additional features now supported when working with Tyk OAS APIs</summary>

The following features have been added in 5.3.0 to bring Tyk OAS to feature maturity:

- Detailed log recording (include payload in the logs)
- Enable Open Telemetry tracing
- Context variables available to middleware chain
- API-level header transforms (request and response)
- Endpoint-level cache
- Circuit breakers
- Track endpoint logs for inclusion in Dashboard aggregated data
- Do-not-track endpoint
- Enforced upstream timeouts
- Configure endpoint as Internal, not available externally
- URL rewrite
- Per-endpoint request size limit
- Request transformation - method, header
- Response transformation - header
- Custom domain certificates

</details>
</li>
<li>
<details>
<summary>Enhanced KV storage for API Definition fields</summary>

We have implemented support for all `string` type fields in the Tyk OAS and Tyk Classic API Definitions to be stored in
separate KV storage, including Hashicorp Consul and Vault.

</details>
</li>
<li>
<details>
<summary>Support for Redis v7.0.x</summary>

Tyk 5.3 refactors Redis connection logic by using
[storage v1.2.2](https://github.com/TykTechnologies/storage/releases/tag/v1.2.2), which integrates with
[go-redis](https://github.com/redis/go-redis) v9. Subsequently, Tyk 5.3 supports Redis v7.0.x.

</details>
</li>
<li>
<details>
<summary>Clearer error messages from GQL engine for invalid variables (JSON Schema)</summary>

Some of the error messages generated by the GQL engine were unclear for users, especially relating to variable
validation. The errors have been changed and are now much more clearer and helpful in cases where engine processing
fails.

</details>
</li>
<li>
<details>
<summary>Upgraded GQL Engine's Go version to 1.19</summary>

Upgraded Go version for GraphQL engine to [1.19](https://go.dev/doc/go1.19).

</details>
</li>
<li>
<details>
<summary>Enhanced semantic conventions for GraphQL spans in Gateway</summary>

We've added OpenTelemetry semantic conventions for GraphQL spans. Spans will now incorporate `<operation.type>`,
`<operation.name>` and `<document>` tags.

</details>
</li>
<li>
<details>
<summary>Added support for detailed_tracing to be configured via GQL API definitions</summary>

GraphQL APIs can now use the `detailed_tracing` setting in an API definition. With that property set to `true` any call
to a GraphQL API will create a span for each middleware involved in request processing. While it is set to `false`, only
two spans encapsulating the entire request lifecycle will be generated. This setting helps to reduce the size of traces,
which can get large for GraphQL APIs. Furthermore, this gives users an option to customize the level of tracing detail
to suit their monitoring needs.

</details>
</li>
<li>
<details>
<summary>Enhanced OpenTelemetry trace generation for UDG with mixed data sources</summary>

This release introduces an enhanced trace generation system for Universal Data Graph (UDG). It consolidates all spans
from both Tyk-managed and external data source executions into a single trace when used together. Furthermore, when UDG
solely utilizes Tyk-managed data sources, trace management is simplified and operational visibility is improved.

</details>
</li>
<li>
<details>
<summary>Disabled normalize and validate in GraphQL Engine</summary>

For GraphQL requests normalization and validation has been disabled in the GraphQL engine. Both of those actions were
performed in the Tyk Gateway and were unnecessary to be done again in the engine. This enhances performance slightly and
makes detailed OTel traces concise and easier to read.

</details>
</li>
<li>
<details>
<summary>Enhanced OAS-to-UDG converter handling of arrays of objects in OpenAPI Documents</summary>

The Tyk Dashboard API endpoint _/api/data-graphs/data-sources/import_ now handles OpenAPI schemas with arrays of
objects. This addition means users can now import more complex OpenAPI documents and transform them into UDG
configurations.

</details>
</li>
<li>
<details>
<summary>OAS-to-UDG converter support for allOf/anyOf/oneOf keywords</summary>

The OAS-to-UDG converter now seamlessly handles OpenAPI descriptions that utilize the _allOf_, _anyOf_ and _oneOf_
keywords, ensuring accurate and comprehensive conversion to a Tyk API definition. The feature expands the scope of
OpenAPI documents that the converter can handle and allows our users to import REST API data sources defined in OAS in
more complex cases.

</details>
</li>
<li>
<details>
<summary>Improved UDG's handling of unnamed object definitions in OpenAPI descriptions</summary>

The OAS-to-UDG converter can now create GraphQL types even if an object's definition doesn’t have an explicit name.

</details>
</li>
<li>
<details>
<summary>Refined handling of arrays of objects in endpoint responses by OAS-to-UDG Converter</summary>

The OAS-to-UDG converter was unable to handle a document properly if an object within the OpenAPI description had no
properties defined. This limitation resulted in unexpected behavior and errors during the conversion process. The tool
will now handle such cases seamlessly, ensuring a smoother and more predictable conversion process.

</details>
</li>
<li>
<details>
<summary>OAS-to-UDG converter support for enumerated types in OpenAPI descriptions</summary>

Previously OAS-to-UDG converter had limitations in handling enums from OpenAPI descriptions, leading to discrepancies
and incomplete conversions. With the inclusion of enum support, the OAS converter now seamlessly processes enums defined
in your OpenAPI descriptions, ensuring accurate and complete conversion to GraphQL schemas.

</details>
</li>
<li>
<details>
<summary>Expanded handling of HTTP Status Code ranges by OAS-to-GQL converter</summary>

OAS-to-UDG converter can now handle HTTP status code ranges that are defined by the OpenAPI Specification. This means
that code ranges defined as 1XX, 2XX, etc will be correctly converted by the tool.

</details>
</li>
<li>
<details>
<summary>Added support for custom rate limit keys</summary>

We have added the capability for users to define a [custom rate limit
key]({{< ref "tyk-stack/tyk-developer-portal/enterprise-developer-portal/api-access/configuring-custom-rate-limit-keys" >}})
within session metadata. This increases flexibility with rate limiting, as the rate limit can be assigned to different entities
identifiable from the session metadata (such as a client app or organization) and is particularly useful for users of Tyk's
Enterprise Developer Portal.

</details>
</li>
</ul>

#### Changed

<!-- This should be a bullet-point list of updated features. Explain:

- Why was the update necessary?
- How does the update benefit users?
- Link to documentation of the updated feature
- For OSS - Link to the corresponding issue if possible on GitHub to allow the users to see further info.

Each change log item should be expandable. The first line summarises the changelog entry. It should be then possible to expand this to reveal further details about the changelog item. This is achieved using HTML as shown in the example below. -->
<ul>
<li>
<details>
<summary>Prefetch session expiry information from MDCB to reduce API call duration in case Gateway is temporarily disconnected from MDCB</summary>

Previously, when operating in a worker configuration (in the data plane), the Tyk Gateway fetched session expiry
information from the control plane the first time an API was accessed for a given organization. This approach led to a
significant issue: if the MDCB connection was lost, the next attempt to consume the API would incur a long response
time. This delay, typically around 30 seconds, was caused by the Gateway waiting for the session-fetching operation to
time out, as it tried to communicate with the now-inaccessible control plane.

<br>Now, the worker gateway fetches the session expiry information up front, while there is an active connection to
MDCB. This ensures that this data is already available locally in the event of an MDCB disconnection.

<br>This change significantly improves the API response time under MDCB disconnection scenarios by removing the need for
the Gateway to wait for a timeout when attempting to fetch session information from the control plane, avoiding the
previous 30-second delay. This optimization enhances the resilience and efficiency of Tyk Gateway in distributed
environments.

</details>
</li>
<li>
<details>
<summary>Changes to the Tyk OAS API Definition</summary>

We have made some changes to the Tyk OAS API Definition to provide a stable contract that will now be under
breaking-change control for future patches and releases as Tyk OAS moves out of Early Access. Changes include the
removal of the unnecessary `slug` field and simplification of the custom plugin contract.

</details>
</li>
<li>
<details>
<summary>Optimized Gateway memory usage and reduced network request payload with Redis Rate Limiter</summary>

We have optimized the allocation behavior of our sliding window log rate limiter implementation ([Redis
Rate Limiter]({{< ref "getting-started/key-concepts/rate-limiting#redis-rate-limiter" >}})). Previously the complete
request log would be retrieved from Redis. With this enhancement only the count of the requests in the window is
retrieved, optimizing the interaction with Redis and decreasing the Gateway memory usage.

</details>
</li>
</ul>
 
#### Fixed
<!-- This section should be a bullet point list that describes the issues fixed in the release. For each fixed issue explain:

- What problem the issue caused
- How was the issue fixed
- Link to (new) documentation created as a result of a fix. For example, a new configuration parameter may have been
  introduced and documented for the fix
- For OSS - Link to the corresponding issue if possible on GitHub to allow the users to see further info.

Each change log item should be expandable. The first line summarises the changelog entry. It should be then possible to
expand this to reveal further details about the changelog item. This is achieved using HTML as shown in the example
below. -->

<ul>
<li>
<details>
<summary>Improved OAuth token management in Redis</summary>

In this release, we fixed automated token trimming in Redis, ensuring efficient management of OAuth tokens by
implementing a new hourly job within the Gateway and providing a manual trigger endpoint.

</details>
</li>
<li>
<details>
<summary>Tyk Gateway now validates RFC3339 Date-Time Formats</summary>

We fixed a bug in the Tyk OAS Validate Request middleware where we were not correctly validating date-time format
schema, which could lead to invalid date-time values reaching the upstream services.

</details>
</li>
<li>
<details>
<summary>Inaccurate Distributed Rate Limiting (DRL) behavior on Gateway startup</summary>

Fixed an issue when using the Distributed Rate Limiter (DRL) where the Gateway did not apply any rate limit until a DRL
notification was received. Now the rate of requests will be limited at 100% of the configured rate limit until the DRL
notification is received, after which the limit will be reduced to an even share of the total (i.e. 100% divided by the
number of Gateways) per the rate limit algorithm design.

</details>
</li>
<li>
<details>
<summary>Duplicate fields added by OAS-to-UDG converter</summary>

Fixed an issue where the OAS-to-UDG converter was sometimes adding the same field to an object type many times. This
caused issues with the resulting GQL schema and made it non-compliant with GQL specification.

</details>
</li>
<li>
<details>
<summary>Gateway issue processing queries with GQL Engine</summary>

Fixed an issue where the Gateway attempted to execute a query with GQL engine version 1 (which lacks OTel support),
while simultaneously trying to validate the same query with the OpenTelemetry (OTel) supported engine. It caused the API
to fail with an error message "Error socket hang up". Right now with OTel enabled, the gateway will enforce GQL engine
to default to version 2, so that this problem doesn't occur anymore.

</details>
</li>
<li>
<details>
<summary>Handling arrays of objects in endpoint responses by OAS-to-UDG converter</summary>

The OAS-to-UDG converter now effectively handles array of objects within POST paths. Previously, there were instances
where the converter failed to accurately interpret and represent these structures in the generated UDG configuration.

</details>
</li>
<li>
<details>
<summary>GQL Playground issues related to encoding of request response</summary>

An issue was identified where the encoding from the GQL upstream cache was causing readability problems in the response
body. Specifically, the upstream GQL cache was utilizing brotli compression and not respecting the Accept-Encoding
header. Consequently, larger response bodies became increasingly unreadable for the GQL engine due to compression,
leading to usability issues for users accessing affected content. The issue has now been fixed by adding the brotli
encoder to the GQL engine.

</details>
</li>
<li>
<details>
<summary>OAS-to-UDG converter issue with "JSON" return type</summary>

OAS-to-UDG converter was unable to correctly process Tyk OAS API definitions where "JSON" was used as one of enum
values. This issue is now fixed and whenever "JSON" is used as one of enums in the OpenAPI description, it will get
correctly transformed into a custom scalar in GQL schema.

</details>
</li>
<li>
<details>
<summary>Gateway Panic during API Edit with Virtual Endpoint</summary>

Fixed an issue where the Gateway could panic while updating a Tyk OAS API with the Virtual Endpoint middleware
configured.

</details>
</li>
<li>
<details>
<summary>Gateway panics during API Reload with JavaScript middleware bundle</summary>

Fixed an issue where reloading a bundle containing JS plugins could cause the Gateway to panic.

</details>
</li>
<li>
<details>
<summary>GraphQL introspection issue when Allow/Block List enabled</summary>

Fixed an issue where the _Disable introspection_ setting was not working correctly in cases where field-based
permissions were set (allow or block list). It was not possible to introspect the GQL schema while introspection was
technically allowed but field-based permissions were enabled. Currently, Allow/Block list settings are ignored only for
introspection queries and introspection is only controlled by the _Disable introspection_ setting.

</details>
</li>
<li>
<details>
<summary>Handling of objects without properties in OAS-to-UDG converter</summary>

The OAS-to-UDG converter was unable to handle a document properly if an object within the OpenAPI description had no
properties defined. This limitation resulted in unexpected behavior and errors during the conversion process. The tool
will now handle such cases seamlessly, ensuring a smoother and more predictable conversion process

</details>
</li>
<li>
<details>
<summary>Fixed memory leak issue in Tyk Gateway v5.2.4</summary>

Addressed a memory leak issue in Tyk Gateway linked to a logger mutex change introduced in v5.2.4. Reverting these
changes has improved connection management and enhanced system performance.

</details>
</li>

</ul>

#### Security Fixes

<!-- This section should be a bullet point list that should be included when any security fixes have been made in the release, e.g. CVEs. For CVE fixes, consideration needs to be made as follows:
1. Dependency-tracked CVEs - External-tracked CVEs should be included on the release note.
2. Internal scanned CVEs - Refer to the relevant engineering and delivery policy.

For agreed CVE security fixes, provide a link to the corresponding entry on the NIST website. For example:

- Fixed the following CVEs:
    - [CVE-2022-33082](https://nvd.nist.gov/vuln/detail/CVE-2022-33082)
-->

<ul>
<li>
<details>
<summary>High priority CVEs fixed</summary>

Fixed the following high priority CVEs identified in the Tyk Gateway, providing increased protection against security
vulnerabilities:

- [CVE-2023-39325](https://nvd.nist.gov/vuln/detail/CVE-2023-39325)
- [CVE-2023-45283](https://nvd.nist.gov/vuln/detail/CVE-2023-45283)
</details>
</li>
</ul>

## <!-- Required. use 3 hyphens --- between release notes of every patch (minors will be on a separate page) -->

<!--
Repeat the release notes section above for every patch here
-->

<!-- The footer of the release notes page. It contains a further information section with details of how to upgrade Tyk,
links to API documentation and FAQs. You can copy it from the previous release. -->

## Further Information

### Upgrading Tyk

Please refer to the [upgrading Tyk]({{< ref "upgrading-tyk" >}}) page for further guidance on the upgrade strategy.

### API Documentation

<!-- Required. Update the link to the Gateway "tyk-gateway-api" or dashboard "tyk-dashboard-api" and the Postman collection

If there were changes in any of Tyk’s API docs:

- Have API endpoints been documented in the release note summary and changelog?
- Has a link to the endpoint documentation being included?
- Has the benefit of the new/updated endpoint been explained in the release highlights and changelog?
-->

- [Tyk Gateway API]({{<ref "tyk-gateway-api/" >}})
- [Postman Collection](https://www.postman.com/tyk-technologies/workspace/tyk-public-workspace/overview)

### FAQ

Please visit our [Developer Support]({{< ref "frequently-asked-questions/faq" >}}) page for further information relating
to reporting bugs, upgrading Tyk, technical support and how to contribute.
