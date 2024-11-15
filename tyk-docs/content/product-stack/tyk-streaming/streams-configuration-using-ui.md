---
title: Configure Streams API using dashboard
description: Guide to creating a streaming api using dashboard
tags: [ "streaming", "events", "event-driven architecture", "event-driven architectures", "kafka" ]
publishdate: 2024-11-15
---
# Creating a Streams API Using the Dashboard UI

This page provides a detailed guide for creating, configuring, and securing a **Streams API** using the Dashboard UI. Follow these steps to set up real-time streaming with authentication and access control.

---

## Prerequisites

- Access to the Dashboard.
- Proper permissions to create APIs, policies, and keys.
- A clear understanding of your streaming input, processing, and output requirements.

---

## Steps to Create a Streams API

### 1. Access the Streams & Events Section
1. Click on **Streams & Events** from the sidebar.  
   {{< img src="/img/streams/sidebar-navigation.png" alt="Sidebar Navigation" width="670px" height="500px" >}}

2. This opens a wizard-like form for creating a Streams API.  
   {{< img src="/img/streams/streams-and-events-wizard.png" alt="Streams & Events Wizard" width="670px" height="500px" >}}

### 2. Define API Name and Type
1. Enter a **unique API name**.  
2. Select the **Streams** option.  
   {{< img src="/img/streams/streams-option.png" alt="Streams Option" width="670px" height="500px" >}}

3. Click **Continue**.

### 3. Configure API Inputs, Processors, and Outputs
1. On the next screen, configure your Streams API:
   - **Input**: Select one or more data sources.  
   - **Processor**: Choose a single processor for handling the data.  
   - **Output**: Define one or more output destinations.  
     {{< img src="/img/streams/selection.png" alt="Output Selection" width="670px" height="500px" >}}

2. For manual configuration:
   - Enable the **Advanced** checkbox to create a custom YAML template.  

3. Click **Finish** to proceed to the API Details page.

### 4. Review and Finalize API Details
1. On the **API Details page**, review the auto-generated YAML configuration.  
   {{< img src="/img/streams/api-details-page.png" alt="API Details Page" width="670px" height="500px" >}}

2. Configure additional settings:
   - **Authentication**: Choose an authentication mechanism (e.g., API Key, OAuth2).  
   - **Gateway Status**: Set to **Active** or **Disabled**.
   - **Access**: Select **Internal** (restricted) or **External** (public) access.

3. Click **Save API**.  

   - The API is now created, and a unique **API ID** is assigned.
   - The API will appear in the **APIs listing screen**.

---

## Steps to Access the Streams API

### 5.1 Create a Policy
1. Navigate to **Policies** in the sidebar and click **Add Policy**.  

2. Select the newly created Streams API.  

3. Configure the following:
   - **Limits**: Define **Rate Limiting**, **Throttling**, and **Usage Quota**.
   - **Configuration**: Provide a policy name and set a key expiration interval.  

4. Click **Create Policy** to save.

### 5.2 Generate a Key
1. Go to **Keys** from the sidebar and click **Add Key**.  

2. On the key creation page:
   - Select the newly created policy under the **Access Rights** tab.  

   - Review the applied limits for the API.

3. Click **Create Key**.  

   - A popup will display the **Key Hash** and **Key ID**.  

   - Use the **Key ID** to access the protected Streams API.

---

## Summary

By completing the steps outlined above, you will have:

- A functional **Streams API** with real-time data delivery capabilities.
- Policies and keys for authentication and access control.

You can now use the **Key ID** to securely access the API for your applications.

---
