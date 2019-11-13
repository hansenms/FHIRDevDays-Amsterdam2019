# FHIR DevDays November 2019

This is the [Azure API for FHIR](https://azure.microsoft.com/en-us/services/azure-api-for-fhir/) tutorial for FHIR DevDays in Amsterdam 2019.

The presentation will be on Wednesday, November 20th, 2019 at 11:55am (Wiechert room).

[Slides](FHIRDevDaysAmsterdam2019-Tutorial.pdf) (with tutorial screenshot).

## Prerequisites

In order to work through this tutorial, you should have:

1. [Azure subscription](https://azure.microsoft.com/free/).
1. [Synthea](https://github.com/synthetichealth/synthea) for generating data. You can grab some [pre-generated data](https://mihansenlondon.blob.core.windows.net/fhir-data/fhir_r4.zip).
1. (optional) A text editor, e.g. [Visual Studio Code](https://code.visualstudio.com/download)
1. (optional) Azure CLI. Available in cloud shell.
1. (optional) Az Powershell module. Available in cloud shell.
1. (optional) AzureAd Powershell module. Available in cloud shell.

## Simple Javascript app

In this tutorial, we will deploy a small javascript app, which reads data from a FHIR service.

1. [Deploy](https://docs.microsoft.com/en-us/azure/healthcare-apis/fhir-paas-portal-quickstart) an instance of the Azure API for FHIR.

1. [Enable CORS](https://docs.microsoft.com/en-us/azure/healthcare-apis/configure-cross-origin-resource-sharing) for the Azure API for FHIR.

1. Create an [Azure AD public client](https://docs.microsoft.com/en-us/azure/healthcare-apis/register-public-azure-ad-client-app) application. You can optionally use the Azure CLI for doing this:

    ```bash
    resourceApp=$(az ad sp list --filter "displayName eq 'Azure Healthcare APIs'" | jq .[0])
    resourceAppId=$(echo $resourceApp | jq -r .appId)
    resourceScopeId=$(echo $resourceApp | jq -r .oauth2Permissions[0].id)
    echo "[{\"resourceAppId\": \"$resourceAppId\", \"resourceAccess\": { \"id\": \"$resourceScopeId\", \"type\":\"scope\"}}]" > manifest.json
    az ad app create --display-name "devdays-demo-client" --native-app --oauth2-allow-implicit-flow --reply-urls "http://localhost:9090/index.html" --required-resource-accesses @manifest.json
    ```
    Make a note of the client app id. In the portal, check that Access Token and ID Token are allowed under implicit flow.

1. Test the [Azure API for FHIR using Postman](https://docs.microsoft.com/en-us/azure/healthcare-apis/access-fhir-postman-tutorial). Make sure the Postman reply URL is registered in the client app.

1. Clone this repo and go to the `src/fhir-app/index.html` file and edit:

    1. Authority
    1. FHIR server URL
    1. Client id.

1. Run the app with `npm start`. Make sure you have registered the reply URL (`http://localhost:9090/index.html`) in the client application. You should be able to sign in and see a list of patients in the FHIR service.

1. Create an Azure Web App and upload the `index.html` file to the service. You can also use the `az webapp up -l <location> -n <name>` to automatically create a web app and upload the contents of the current folder. Make sure you have the URL of the new web app registered as a reply URL on the client application. Check that the web app works when deployed.

## Large Sandbox

There is a larger sandbox that demonstrates more FHIR in the cloud capabilities. It can be found at:

[https://github.com/Microsoft/fhir-server-samples](https://github.com/Microsoft/fhir-server-samples)

It deploys a FHIR service with several apps and tools:

![FHIR sandbox](https://raw.githubusercontent.com/microsoft/fhir-server-samples/master/images/fhir-server-samples-paas.png)

1. Deploy the sandbox by following the instructions in the Github repo.
1. Import Synthea patients in the FHIR server using the importer function. Hint: Drop the bundles in the `fhir-import` container in the storage account associated with the importer function.
1. Use the dashboard to browse the FHIR patients.
1. Use the SMART on FHIR apps.
1. Use the token in the "About me" page to access the FHIR service using Postman.
1. Use data factor to export data.
1. Analyse the exported data using Data Bricks.
