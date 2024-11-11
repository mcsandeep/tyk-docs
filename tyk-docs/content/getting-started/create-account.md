---
aliases:
- /create-account
date: 2020-06-24
description: How to decide on which Tyk deployment option is best for you
linkTitle: Getting Started
tags:
- Tyk API Management
- Open Source
- Self-Managed
- Tyk Cloud
- API Gateway
title: Create Tyk Account
aliases:
    - /tyk-cloud/initial-portal-config
---


Welcome to Tyk! This guide will walk you through the process of creating your account and getting started with our powerful API management platform.

## Choosing Your Tyk Solution

Tyk offers multiple deployment options to suit your needs:

- **Tyk Cloud**: A fully managed service for easy API management at any scale.
- **Tyk Self-Managed**: Install the full lifecycle API management solution in your own infrastructure.
- **Tyk Open Source**: The core API Gateway, freely available and open source.

For this guide, we'll focus on creating an account for Tyk Cloud, which offers a free 48 hour trial.

## Creating Your Tyk Cloud Account

### Step 1: Visit the Sign-Up Page

Navigate to the Tyk sign-up page at [https://tyk.io/sign-up/](https://tyk.io/sign-up/).

### Step 2: Choose "Start Your 48-hour Free Trial"

On the sign-up page, select the "Start your 48-hour free trial" option to begin your Tyk Cloud experience.

### Step 3: Complete the Account Creation Form

Fill out the account creation form with your details:

- First Name
- Last Name
- Email Address
- Password
- Company Name (if applicable)
- Work Role and How We Can Help

### Step 4: Choose Your Setup

After completing the form, you can select which setup you'd like to kickoff your Tyk journey with.

- If you're a new user, we suggest starting with the automatic setup. This will preconfigure your deployments, automatically setting up your control pane and gateways so you can familiarize yourself with what Tyk offers.

- If you want to have more control, skip the automatic setup and personalize it yourself. You can tailor your setup to your organization's specific needs, manually setting up the teams, environments, control panes, and gateways. 

{{< img src="/img/2.10/create-account-setup-choice.png" alt="Create Account Setup" >}}


### Step 5: Verify Your Email

Check your email inbox for a verification message from Tyk. Click the verification link to confirm your email address.

### Step 6: Access the Developer Portal

Once your account is set up, you can access the Tyk Developer Portal. The portal allows you to manage your APIs, view analytics, and customize your API consumer experience.

Watch our video on configuring your Tyk Cloud Developer Portal.

{{< youtube 8_SPUake84w >}}

1. From the Control Plane Dashboard, select **Pages** from the **Portal Management** menu
2. Click **Add Page**

{{< img src="/img/getting-started/create-account-portal-pages.png" alt="Add Portal Page" >}}

3. In the Settings, give your page a name and slug. Below we've called it Home
4. Select **Check to make this page the Home page**
5. Select **Default Home page template** from the Page type drop-down list
6. You can leave the Registered Fields sections for now

{{< img src="/img/getting-started/create-account-portal-pages.png" alt="Portal Home page settings" >}}

7. Click **Save**.

You should now be able to access your Portal from **Open Your Portal** from the **Your Developer Portal** menu.

{{< img src="/img/getting-started/portal_menu.png" alt="Portal Menu" >}}

## Further Portal Configuration

Our Developer Portal is completely customizable. See [Portal Customization]({{< ref "tyk-developer-portal/customise" >}}) for more details.


## What Happens Next?

Once you've created your account, Tyk will automatically:

- Assigns Billing Admin Role: You are designated as the Billing Admin for your organization, granting you full access to manage billing details and subscription plans.

- Activates 48-Hour Free Trial: Your account is enrolled in a 48-hour free trial of Tyk Cloud, allowing you to explore its features and capabilities without immediate commitment.

- Creates Initial Organization: An organization is automatically established, serving as the primary entity for managing your environments, APIs, and users.

- Establishes Default Team: A default team is set up within your organization, providing a collaborative space for managing APIs and related resources.

- Deploys Control Plane: A control plane is deployed in your selected home region, centralizing the management of your APIs, policies, and configurations.

- Deploys Gateway: A Tyk Gateway is deployed to manage and route incoming API traffic, handling authentication, rate limiting, and analytics to ensure secure, reliable access.

For certain Tyk Cloud configurations, you may also get an Edge Gateway deployment option, allowing gateways to be positioned closer to users for lower latency and optimized routing. This is ideal for multi-region or global API setups but may require additional configuration or regional deployment options through Tyk’s Multi Data Centre Bridge (MDCB) if set up manually.

## Accessing Your Tyk Cloud Dashboard

After verifying your email, you can log in to your Tyk Cloud Dashboard. This is where you'll manage your APIs, analytics, and more.


{{< note success >}}
**Note**  

After the 48-hour free trial of Tyk Cloud ends, your infrastructure (control plane, gateway, and organization settings) will be deactivated unless you upgrade to a paid plan. Here’s what happens:

- Limited Access: Control plane access and API traffic routing through the gateway will be suspended.
- Data Retention: Your configurations (APIs, policies, user settings) are temporarily retained, allowing you to pick up where you left off if you upgrade within a grace period.
- Billing Admin Role: You’ll still be able to manage billing and subscription options.

Upgrading restores full functionality, letting you continue from where you paused. To avoid disruption, consider exploring paid plans before your trial ends.

{{< /note >}}

## Next Steps

Now that you have your Tyk account set up, here are some recommended next steps:

- **Explore the Dashboard**: Familiarize yourself with the [Tyk Cloud interface](/tyk-dashboard).
- **Create Your First API**: Follow our guide on [setting up and securing your first API](/getting-started/configure-first-api).
- **Customize Your Portal**: Learn how to customize the developer portal for your API consumers.
- **Set Up Email Notifications**: Configure email notifications for important events and updates.

## Need Help?

If you encounter any issues or have questions during the setup process, don't hesitate to reach out to our support team at support@tyk.io.

Remember, Tyk offers powerful features for API management, security, and performance. Take advantage of your trial period to explore all that Tyk has to offer!