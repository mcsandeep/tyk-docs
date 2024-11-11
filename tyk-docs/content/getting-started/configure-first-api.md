---
aliases:
- /quickstart-configure-first-api
date: 2020-06-24
description: How to decide on which Tyk deployment option is best for you
linkTitle: Getting Started
tags:
- Tyk API Management
- Open Source
- Self-Managed
- Tyk Cloud
- API Gateway
title: Tyk QuickStart Configure Your First API
---


## Overview
This guide helps you get started with Tyk Cloud by covering the basics:

- **Set up your API**: Create and configure a new API in the Tyk Dashboard.
- **Create API keys**: Generate API keys and assign them to your APIs for secure access.
- **Monitor API performance**: Track traffic, logs, and performance analytics.

Follow these steps to quickly create and manage your APIs in Tyk Cloud.

## Prerequisites

Before you begin, make sure you have:
- [A Tyk Cloud account](create-account).
- Admin access to the Tyk Dashboard.
- (optional) A backend service that your API will proxy (e.g., a RESTful API) - or you can use the httpbin service.


## Set Up Your API

Start by creating a new API in Tyk Cloud:

1. **Log in to the Tyk Dashboard**.
2. **Navigate to APIs** and click **Create New API**.
3. **Configure API Details**:
   - **API Name**: Name your API (e.g., `My First API`).
   - **Target URL**: Provide the URL of your backend service (e.g., `https://my-backend-service.com`).
   - **API Slug**: Define the path through which your API will be accessible (e.g., `/my-first-api/`).

  {{< img src="/img/getting-started/apis-create-new-api.png" alt="Create New API" >}}

<br>

4. **Connect to Your Desired Gateway**
  {{< img src="/img/getting-started/apis-connect-gateways.png" alt="Connect Gateways" >}}

<br>

5. **Configure Settings**
  {{< img src="/img/getting-started/apis-configure-settings.png" alt="Configure Settings" >}}

<br>

6. **Authentication**: Choose the desired authentication method (e.g., **API Key**).

Save your API configuration once complete.


## Create an API Key

The Tyk Dashboard provides the simplest way to generate a new API key. Follow these steps:

1. **Select "Keys"** from the **System Management** section.
   
   {{< img src="/img/getting-started/apis-sidebar-security.png" alt="Keys Menu" >}}

<br>

2. **Click "Add Key"** to generate a new key.

   {{< img src="/img/getting-started/apis-add-key.png" alt="Add Key" >}}

<br>

3. **Add a Policy or API to Your Key**:
   - You can either add your key to an existing **Policy** or assign it to an individual **API**.
   - For this guide, we will assign the key to an API. You can:
     - Scroll through your **API Name list**,
     - Use the **Search field**,
     - Or **Group by Authentication Type** or **Category** to filter APIs.
   - Leave all other options at their default settings.

4. **Add Configuration Details**:
   - **Enable Detailed Logging**: This is optional and disabled by default.
   - **Key Alias**: Assign an alias to your key for easier identification.
   - **Key Expiry**: Set an expiry time from the drop-down list. This is required.
   - **Tags**: Add tags for filtering data in Analytics. Tags are case-sensitive.
   - **Metadata**: Add metadata such as user IDs, which can be used by middleware components.

5. **Click "CREATE"**:
   - Once the key is created, a **Key successfully generated** pop-up will be displayed showing your key. **Copy the key** to your clipboard and save it for future reference as it will not be shown again.

   {{< img src="/img/getting-started/apis-create-key.png" alt="APIs Create Key" >}}
<br>
   - And that should result in a successfully generated key!

   {{< img src="/img/getting-started/apis-keys-success.png" alt="Key Success" >}}
  

## Monitor Traffic and Analyze API Performance

With your API live, monitor its traffic and analyze performance:

### View Traffic Analytics

1. **Navigate to the Analytics Section** in the dashboard.
2. **View Traffic Metrics**: Review metrics such as request count, response times, and error rates.
3. **Analyze Data**: Use traffic trends to identify performance issues or optimize API behavior.

{{< img src="/img/getting-started/apis-analytics.png" alt="APIs Analytics" >}}



#### Example Traffic Analytics Response

```json
{
  "requests": 1500,
  "errors": 5,
  "latency": {
    "average": 120,
    "p95": 180
  }
}
```

- **Requests**: The total number of API requests made within the monitored period.

- **Errors**: The number of failed requests, indicating issues or errors encountered during API calls.

- **Latency**:
  - **Average**: The average time (in milliseconds) it takes for the API to respond to a request.
  - **95th Percentile (p95)**: The time within which 95% of the requests are responded to, providing insight into the upper range of response times.



### View Log Data

1. **Go to the Logs Section** of your API.
2. **Search and Filter Logs**: Use filters to drill down by response status, endpoint, or client IP.
3. **Review Detailed Logs**: View full request and response data to troubleshoot issues.

#### Example Log Entry

```json
{
  "timestamp": "2024-09-05T12:00:00Z",
  "method": "GET",
  "path": "/my-api/users",
  "status": 200,
  "response_time": 95
}
```
- **Timestamp**: The date and time when the API request was made (ISO 8601 format).

- **Method**: The HTTP method used for the request (e.g., GET, POST).

- **Path**: The API endpoint that was accessed.

- **Status**: The HTTP status code returned by the API, indicating the result of the request (e.g., 200 for success).

- **Response Time**: The time (in milliseconds) taken for the API to respond to the request.


## Next Steps

Congratulations! You've successfully created, secured, and deployed your first API in Tyk Cloud. Next, explore more advanced features like [rate-limiting](getting-started/key-concepts/rate-limiting/) or [OAuth2](api-management/authentication-authorization).

Explore more features in your dashboard to optimize and scale your API offerings.