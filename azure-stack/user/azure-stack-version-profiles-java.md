---
title: Use API version profiles with Java in Azure Stack Hub 
description: Learn how to use API version profiles with Java in Azure Stack Hub.
author: sethmanheim

ms.topic: article
ms.date: 1/13/2022
ms.author: sethm
ms.reviewer: weshi1
ms.lastreviewed: 1/13/2022

# Intent: As an Azure Stack user, I want to use API version profiles with Java in Azure stack so I can benefit from the use of profiles.
# Keyword: azure stack api profiles java

---


# Use API version profiles with Java in Azure Stack Hub

The Java SDK for the Azure Stack Hub Resource Manager provides tools to help you build and manage your infrastructure. Resource providers in the SDK include compute, networking, storage, app services, and Azure Key Vault. You can find the [Hybrid Java Samples repository](https://github.com/Azure-Samples/Hybrid-Java-Samples) on GitHub. This article will help you set up your environment, get the right credentials, grab the repository, and create a resource group in Azure Stack Hub.

Using the Java SDK enables a true hybrid cloud developer experience. Switching the version dependencies in the `POM.xml` in the Java SDK enable hybrid cloud development by helping you switch between global Azure resources to resources in Azure Stack Hub.

::: moniker range=">=azs-2102"

To use the latest version of the services, use the **latest** profile as the dependency.

You can target your app to resource in Azure tack Hub by taking your existing **com.azure.resourcemanager** dependency and change the version from `x.y.z` to `x.y.z-hybrid`. The hybrid packages, which provide support for Azure Stack Hub, use a `-hybrid` suffix at the end of the version, for example, `1.0.0-hybrid`. This will point to a static collection of endpoints associated with the version.

To get the the latest profile, take your existing **com.azure.resourcemanager** dependency and change the version to **latest**. The **latest** profile Java packages provide a consistent experience with Azure. The packages share the same group ID as Azure **com.azure.resourcemanager**. The artifact ID and namespaces are also the same as global Azure. This helps in porting your Azure app to Azure Stack Hub. To find more about the endpoints used in Azure Stack Hub as par of the hybrid profile, see the [Summary
of API profiles](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

The profile is specified in the `pom.xml` file in the Maven project as a dependency. The profile loads modules automatically if you choose the right class from the dropdown list (as you would with .NET).
## Set up your development environment

To prepare your environment for running the SDK, you can use the IDE that you prefer such as Eclipse or Visual Studio Code. But you will need to have Git, the Java SDK, and Apache Maven installed. You can find details about the prerequisites for the setting up your development environment at [Use the Azure SDK for Java](/azure/developer/java/sdk/overview)

1. Install Git. You can find the official instructions to install Git at [Getting Started - Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Install the Java SDK and set your `JAVA_HOME` environment variable to the location of the binaries for Java Development Kit. You can find the downloadable installation media instructions for the [OpenJDK](https://www.microsoft.com/openjdk). Install version 8 or greater of the Java Developer Kit.

3. Install Apache Maven. You can find instruction at [the Apache Maven Project](https://maven.apache.org/). Install Apache Maven is 3.0 or above. 

## Set up access to Azure Stack Hub

You will use a service principal to access resources in your Azure Stack Hub system.

1. You need a subscription to the user (tenant) of an Azure Stack Hub environment. For instructions on how to create a subscription, see [Create subscriptions to offers in Azure Stack Hub](../operator/azure-stack-subscribe-plan-provision-vm.md).
2. Get your service principal (SPN) and a client secret. For instructions on how to create a service principal for Azure Stack Hub, see [Provide applications access to Azure Stack Hub](../operator/give-app-access-to-resources.md). The client ID is also known as the application ID when creating a service principal. If you do not have access, request the SPN and secret from your cloud operator.
3. Add your service principal as a **contributor/owner** to your subscription.  For instructions on how to assign a role to service principal, see [Provide applications access to Azure Stack Hub](../operator/give-app-access-to-resources.md).
4. Validate that you have the following credentials:

    | Name | Key | Description |
    | --- | --- | --- |
    | Client ID | clientId | The service principal application ID saved when the service principal was created. |
    | Client Secret | clientSecret | The service principal application secret saved when the service principal was created. |
    | Client Object ID | clientObjectId | The ID of service principal. You can retrieve the ID with the following cmdlet: `Get-AzADServicePrincipal -DisplayName $name`. |
    | Subscription ID | subscriptionId | You use the [subscription ID](../operator/service-plan-offer-subscription-overview.md#subscriptions) to access offers in Azure Stack Hub. |
    | Tenant ID | tenantId | Your Azure Stack Hub [tenant ID](../operator/azure-stack-identity-overview.md). To find the tenant ID for your Azure Stack Hub, see the instructions [here](../operator/azure-stack-csp-ref-operations.md). |
    | Resource Manager URL | resourceManagerUrl | See the [Azure Stack Hub Resource Manager endpoint](azure-stack-version-profiles-ruby.md?#the-azure-stack-hub-resource-manager-endpoint) article. |
    | Location | location | Azure regions are global datacenters where Azure compute, networking, and storage resources are located. When creating an Azure resource, a customer needs to select a resource location. Azure Stack Hub may have locations specific to the datacenter hosting the system. |

    For example:

    ```json
    {
        "clientId": "0cbd71bb-xxxx-4305-9250-8a8efa19082e",
        "clientSecret": "3qx7xxxx5RQaf8o2hUtvVuGvTda2CWghNbLg",
        "clientObjectId": "cbe4b8d0-xxxx-47c3-8ae4-f652a5e55aaf",
        "subscriptionId": "246b1785-xxxx-40d8-a0f0-d94b15dc002c",
        "tenantId": "246b1785-xxxx-40d8-a0f0-d94b15dc002c",
        "resourceManagerUrl": "https://management.azure.com/",
        "location": "Boston"
     }
    ```

## Run an SDK sample

Clone the GitHub repository that contains the sample, add your service principal values to the app configuration, update the application `POM.xml`, compile, and run the Maven project.

1. Clone the samples repository to your development machine.

    ```bash
    git clone https://github.com/Azure-Samples/Hybrid-Java-Samples
    ```

2. Copy `azureAppSpConfig.json.dist` in the root folder of the repository and rename as `azureAppSpConfig.json`. Add the credentials for your system and service principal.

3. Install the correct dependency packages, open the `pom.xml` file in your Java app. In this sample,, the `pom` is located at `\resourcegroup\pom.xml`. Add a dependency in the `<dependencies>` section as shown in the following code:

    ```xml
    <dependency>
    <groupId>com.azure.resourcemanager</groupId>
    <artifactId>azure-resourcemanager</artifactId>
    <version>1.0.0-hybrid</version>
    </dependency>
     ```

    The set of packages that need to be installed depends on the profile version you want to use. The package names for the profile versions are: `com.azure.resourcemanager`

4. Create and delete a resource group in your Azure Stack Hub environment.

    1. Open the `resourcegroup` subfolder.

        ```bash
        cd resourcegroup
        ```

    2. Run the sample with the following Maven commands:

        ```bash
        mvn clean compile
        mvn exec:java
        ```

## More samples and steps

You can use the samples in the Azure Stack Hub Java SDK to perform the following tasks:

1. Samples for managing resource groups:
    - Create a resource group
    - Update a resource group
    - Create another resource group
    - List resource groups
    - Delete a resource group

2. Samples for Key vault in Azure Stack Hub:
    - Create a key vault
    - Set a secret
    - Get a secret
    - Delete a key vault

3. Samples for storage service in Azure Stack Hub:
    - Create a storage account
    - Get and regenerate storage account access keys
    - Create another storage account
    - List storage accounts
    - Delete a storage account

4. Samples for working with VMs in Azure Stack Hub:
    - Create a virtual machine with managed OS Disk
    - Attach data disks
    - Detach data disks
    - List virtual machines
    - Delete a virtual machine

::: moniker-end
::: moniker range="<=azs-2008"
## Java and API version profiles

To use the latest versions of all the services, use the **latest** profile as the dependency.

  - To use the latest profile, the dependency is **com.microsoft.azure**.

  - To use the latest supported services available in Azure Stack Hub, use the
    **com.microsoft.azure.profile\_2019\_03\_01\_hybrid** profile.

    - The profile is specified in the **Pom.xml** file as a dependency, which loads modules automatically if you choose the right class from the dropdown list (as you would with .NET).

  - Dependencies appear as follows:

     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta-1</version>
     </dependency>
     ```

  - To use specific API versions for a resource type in a specific resource provider, use the specific API versions defined through Intellisense.

You can combine all of the options in the same app.

## Install the Azure Java SDK

Follow these steps to install the Java SDK:

1. Follow the official instructions to install Git. See [Getting Started - Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Follow the instructions to install the [Java SDK](https://zulu.org/download/) and [Maven](https://maven.apache.org/). The correct version is version 8 of the Java Developer Kit. The correct version of Apache Maven is 3.0 or above. To complete the quickstart, the `JAVA_HOME` environment variable must be set to the install location of the Java Development Kit. For more info, see [Create your first function with Java and Maven](/azure/azure-functions/functions-create-first-java-maven).

3. To install the correct dependency packages, open the **Pom.xml** file in your Java app. Add a dependency, as shown in the following code:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta-1</version>
   </dependency>
   ```

4. The set of packages that need to be installed depends on the profile version you want to use. The package names for the profile versions are:

   - **com.microsoft.azure.profile\_2019\_03\_01\_hybrid**
   - **com.microsoft.azure**
     - **latest**

5. If not available, create a subscription and save the subscription ID for later use. For instructions on how to create a subscription, see [Create subscriptions to offers in Azure Stack Hub](../operator/azure-stack-subscribe-plan-provision-vm.md).

6. Create a service principal and save the client ID and the client secret. For instructions on how to create a service principal for Azure Stack Hub, see [Provide applications access to Azure Stack Hub](../operator/give-app-access-to-resources.md). The client ID is also known as the application ID when creating a service principal.

7. Make sure your service principal has the contributor/owner role on your subscription. For instructions on how to assign a role to service principal, see [Provide applications access to Azure Stack Hub](../operator/give-app-access-to-resources.md).

## Prerequisites

To use the Azure Java SDK with Azure Stack Hub, you must supply the following values, and then set values with environment variables. To set the environmental variables, see the instructions following the table for your operating system.

| Value                     | Environment variables | Description                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Tenant ID                 | `AZURE_TENANT_ID`            | Your Azure Stack Hub [tenant ID](../operator/azure-stack-identity-overview.md).                                                          |
| Client ID                 | `AZURE_CLIENT_ID`             | The service principal application ID saved when the service principal was created in the previous section.                                                                                              |
| Subscription ID           | `AZURE_SUBSCRIPTION_ID`      | You use the [subscription ID](../operator/service-plan-offer-subscription-overview.md#subscriptions) to access offers in Azure Stack Hub.                |
| Client Secret             | `AZURE_CLIENT_SECRET`        | The service principal application secret saved when the service principal was created.                                                                                                                                   |
| Resource Manager Endpoint | `ARM_ENDPOINT`              | See the [Azure Stack Hub Resource Manager endpoint](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-hub-resource-manager-endpoint) article. |
| Location                  | `RESOURCE_LOCATION`    | **Local** for Azure Stack Hub.                                                                                                                                                                                                |

To find the tenant ID for your Azure Stack Hub, see the instructions [here](../operator/azure-stack-csp-ref-operations.md). To set your environment variables, use the procedures in the following sections:

### Microsoft Windows

To set the environment variables in a Windows command prompt, use the following format:

```shell
Set AZURE_TENANT_ID=<Your_Tenant_ID>
```

### MacOS, Linux, and Unix-based systems

In Unix-based systems, use the following command:

```shell
Export AZURE_TENANT_ID=<Your_Tenant_ID>
```

### Trust the Azure Stack Hub CA root certificate

If you are using the Azure Stack Development Kit (ASDK), you must trust the CA root certificate on your remote machine. You do not need to trust the CA root certificate with Azure Stack Hub integrated systems.

#### Windows

1. Export the Azure Stack Hub self-signed certificate to your desktop.

1. In a command prompt, change the directory to `%JAVA_HOME%\bin`.

1. Run the following command:

   ```shell
   .\keytool.exe -importcert -noprompt -file <location of the exported certificate here> -alias root -keystore %JAVA_HOME%\lib\security\cacerts -trustcacerts -storepass changeit
   ```

### The Azure Stack Hub Resource Manager endpoint

Azure Resource Manager is a management framework that allows admins to deploy, manage, and monitor Azure resources. Azure Resource Manager can handle these tasks as a group, rather than individually, in a single operation.

You can get the metadata info from the Resource Manager endpoint. The endpoint returns a JSON file with the info required to run your code.

Note the following considerations:

- The **ResourceManagerUrl** in the ASDK is: `https://management.local.azurestack.external/`.

- The **ResourceManagerUrl** in integrated systems is: `https://management.region.<fqdn>/`, where `<fqdn>` is your fully qualified domain name.

To retrieve the metadata required: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

Sample JSON file:

```json
{
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication":
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## Existing API profiles

- **com.microsoft.azure.profile\_2019\_03\_01\_hybrid**: Latest profile built for Azure Stack Hub. Use this profile for services to be most compatible with Azure Stack Hub, as long as you're on 1904 or later.

- **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**: Profile built for Azure Stack Hub. Use this profile for services to be compatible with Azure Stack Hub versions 1808 or later.

- **com.microsoft.azure**: Profile consisting of the latest versions of all services. Use the latest versions of all the services.

For more information about Azure Stack Hub and API profiles, see the [Summary
of API profiles](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## Azure Java SDK API profile usage

The following code authenticates the service principal on Azure Stack Hub. It creates a token using the tenant ID and the authentication base, which is specific to Azure Stack Hub:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionID(subscriptionID);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionID());
```

This code enables you to use the API profile dependencies to deploy your app successfully to Azure Stack Hub.

## Define Azure Stack Hub environment setting functions

To register the Azure Stack Hub cloud with the correct endpoints, use the following code:

```java
// Get Azure Stack Hub cloud endpoints
final HashMap<String, String> settings = getActiveDirectorySettings(armEndpoint);

AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceID", settings.get("audience"));
                    put("activeDirectoryGraphResourceID", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".vault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

The `getActiveDirectorySettings` call in the previous code retrieves the endpoints from the metadata endpoints. It states the environment variables from the call that's made:

```java
public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {

    HashMap<String, String> adSettings = new HashMap<String, String>();
    try {

        // create HTTP Client
        HttpClient httpClient = HttpClientBuilder.create().build();

        // Create new getRequest with below mentioned URL
        HttpGet getRequest = new HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
                             armEndpoint));

        // Add additional header to getRequest which accepts application/xml data
        getRequest.addHeader("accept", "application/xml");

        // Execute request and catch response
        HttpResponse response = httpClient.execute(getRequest);

        // Check for HTTP response code: 200 = success
        if (response.getStatusLine().getStatusCode() != 200) {
            throw new RuntimeException("Failed : HTTP error code : " + response.getStatusLine().getStatusCode());
        }

        String responseStr = EntityUtils.toString(response.getEntity());
        JSONObject responseJson = new JSONObject(responseStr);
        adSettings.put("galleryEndpoint", responseJson.getString("galleryEndpoint"));
        JSONObject authentication = (JSONObject) responseJson.get("authentication");
        String audience = authentication.get("audiences").toString().split("\"")[1];
        adSettings.put("login_endpoint", authentication.getString("loginEndpoint"));
        adSettings.put("audience", audience);
        adSettings.put("graphEndpoint", responseJson.getString("graphEndpoint"));

    } catch (ClientProtocolException cpe) {
        cpe.printStackTrace();
        throw new RuntimeException(cpe);
    } catch (IOException ioe) {
        ioe.printStackTrace();
        throw new RuntimeException(ioe);
    }
    return adSettings;
}
```

## Samples using API profiles

Use the following GitHub samples as references for creating solutions with .NET and Azure Stack Hub API profiles:

- [Manage resource groups](https://github.com/Azure-Samples/Hybrid-Java-Samples/tree/resourcegroup-2019-03-01-hybrid)

- [Manage storage accounts](https://github.com/Azure-Samples/Hybrid-Java-Samples/tree/storage-2019-03-01-hybrid)

- [Manage a Virtual Machine](https://github.com/Azure-Samples/Hybrid-Java-Samples/tree/vm-2019-03-01-hybrid)

- [Manage secrets](https://github.com/Azure-Samples/Hybrid-Java-Samples/tree/secret-2019-03-01-hybrid)

### Sample unit test project

1. Clone the repository using the following command:

   ```shell
   git clone https://github.com/Azure-Samples/Hybrid-Java-Samples.git -b resourcegroup-2019-03-01-hybrid
   ```

2. Create an Azure service principal and assign a role to access the subscription. For instructions on creating a service principal, see [Use Azure PowerShell to create a service principal with a certificate](../operator/give-app-access-to-resources.md).

3. Retrieve the following required environment variables:

   - `AZURE_TENANT_ID`
   - `AZURE_CLIENT_ID`
   - `AZURE_CLIENT_SECRET`
   - `AZURE_SUBSCRIPTION_ID`
   - `ARM_ENDPOINT`
   - `RESOURCE_LOCATION`

4. Set the following environment variables using the info retrieved from the service principal you created using the command prompt:

   - `export AZURE_TENANT_ID={your tenant ID}`
   - `export AZURE_CLIENT_ID={your client ID}`
   - `export AZURE_CLIENT_SECRET={your client secret}`
   - `export AZURE_SUBSCRIPTION_ID={your subscription ID}`
   - `export ARM_ENDPOINT={your Azure Stack Hub Resource Manager URL}`
   - `export RESOURCE_LOCATION={location of Azure Stack Hub}`

   In Windows, use **set** instead of **export**.

5. Use the `getActiveDirectorySettings` function to retrieve the Azure Resource Manager metadata endpoints.

    ```java
    // Get Azure Stack Hub cloud endpoints
    final HashMap<String, String> settings = getActiveDirectorySettings(armEndpoint);
    ```

6. In the **Pom.xml** file, add the following dependency to use the **2019-03-01-hybrid** profile for Azure Stack Hub. This dependency installs the modules associated with this profile for the Compute, Networking, Storage, Key Vault, and App Services resource providers:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
      <artifactId>azure</artifactId>
      <version>1.0.0-beta-1</version>
    </dependency>
    ```

7. In the command prompt that was open to set the environment variables, enter the following command:

   ```shell
   mvn clean compile exec:java
   ```

::: moniker-end

## Next steps

For more information about API profiles, see:

- [Version profiles in Azure Stack Hub](azure-stack-version-profiles.md)
- [Resource provider API versions supported by profiles](azure-stack-profiles-azure-resource-manager-versions.md)
