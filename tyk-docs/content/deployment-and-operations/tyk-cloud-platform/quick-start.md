---
title: "Free Trial"
date: 2023-07-24
tags: ["Tyk Stack", "Tyk Cloud", "SaaS"]
description: "Getting started with Tyk Cloud"
weight: 1
menu:
  main:
    parent: "Tyk Cloud"
---

Welcome to the Tyk Cloud Platform! 
This documentation is your gateway to effortlessly deploying and utilizing Tyk on our servers. This guide walks you through the process of creating your first API using the intuitive Tyk Dashboard, eliminating the need for complex setup procedures. 
No installation required!

## Step 1: Sign Up for Tyk Cloud

To begin your Tyk Cloud journey, follow these simple steps to sign up for an account:

* Navigate to the [Tyk Cloud sign up form](https://tyk.io/sign-up/#cloud).
* Fill in the required information and click on "Next step: Create your password."
* Provide your email address and choose a robust password for your account.
* Select your home region, where your data will be securely stored.
* Opt for the "Set up API platform automatically" option (you can still personalize your setup later).
* Wait a couple of minutes and congratulations, your API platform was deployed!

By default, a cloud data plane will be deployed for you. You can also deploy hybrid data planes on your own infrastructure. 

## Step 2: Get started with your first API with Tyk Dashboard




With your Tyk Cloud account set up, it's time to create your first API:

* Click on "Manage my APIs" to access the Tyk Dashboard directly. If you closed your window in the meantime, follow these steps to reach the Tyk Dashboard in Tyk Cloud:
  * Go to the "Deployments" section under "OPERATIONS" on the left-hand side.
  * Select the control plane that was deployed for you.
  * Click on the "Manage APIs" button under "API MANAGER DASHBOARD" in the "Tyk component links" section to access the Tyk Dashboard.

{{< img src="/img/cloud/tyk-cloud-tyk-onboarding-dashboard.png" alt="Accessing Tyk Dashboard from Tyk Cloud" width="500px" >}}

* Click the "Design new API" button to start the API definition creation process.

{{< img src="/img/cloud/tyk-cloud-create-api.png" alt="Accessing Creating an API" width="500px" >}}

* Give your API a name - We’ll use “httpbin” for the rest of this quick start.
* In the "Type" section, please select "HTTP."
* Keep https://httpbin.org/ as the upstream URL and click on "Configure API."
* Finally, click on the button "CONFIGURE API."

{{< img src="/img/cloud/create-api-tyk-cloud.png" alt="Accessing Creating an API" width="500px" >}}

* Select to which gateway you want to deploy this API, select the "edge" tags to deploy to the cloud data plane.

{{< img src="/img/cloud/tyk-cloud-select-cloud-gateway.png" alt="Accessing Creating an API" width="500px" >}}

* Customize your API settings, including authentication, rate limits, and caching, as per your requirements.
* Click "Save" to create your API. Congratulations! You've just set up your first API.

## Step 3: Set up a Policy and Key

Implementing a policy and key system is crucial when creating an API. In this step, we will show you how to do this.

#### Implementing a Policy:

* Click on "Policies" under the "System Management" section on the left-hand side.
* Click on the button "ADD POLICY."

{{< img src="/img/cloud/tyk-cloud-add-policy.png" alt="Policy section" width="500px" >}}

* In the "Access Rights" section, please select "httpbin."

{{< img src="/img/cloud/tyk-cloud-policy-access-rights.png" alt="Policy section" width="500px" >}}

* Go to the "Configurations" section and add a Policy Name (eg., Default Policy httpbin).
* Under the "Settings" section, add an expiry date (eg., 2 weeks).
* Click on the buttons "Create Policy" and "Update."

{{< img src="/img/cloud/tyk-cloud-policy-configurations.png" alt="Policy section" width="500px" >}}

#### Implementing a Key:

* Click on "Keys" under the "System Management" section on the left-hand side.
* Click on the button "ADD KEY."

{{< img src="/img/cloud/tyk-cloud-add-key.png" alt="Key section" width="500px" >}}

* In the "Access Rights" section, please select the previously created Policy (eg., Default Policy httpbin).

{{< img src="/img/cloud/tyk-cloud-key-access-rights.png" alt="Key section" width="500px" >}}

* Go to the "Configurations" section and give your key an alias (eg., platform_team).
* Click on the button "Create Key."

{{< img src="/img/cloud/tyk-cloud-key-configurations.png" alt="Key section" width="500px" >}}

Congratulations! Your key has now been created!

Now that your API is created, you can explore and manage it through the Tyk Dashboard.

If this documentation is not clear to you, please watch this video on how to create your first API using the Tyk Dashboard:
https://tyk-5898904.hs-sites.com/share/hubspotvideo/131350338337



## Next Steps

This quick start guide has covered the essentials to get you up and running with Tyk Cloud. As you explore further, you might want to learn more about [creating APIs]({{< ref "../../getting-started/create-api" >}}) and [importing APIs definition]({{< ref "../../getting-started/import-apis" >}}) in Tyk Dashboard or [managing the infrastructure and environments]({{< ref "../../tyk-cloud/environments-&-deployments/managing-organisations" >}}) in Tyk Cloud.



