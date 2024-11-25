# Azure Functions App Setup

Follow these steps to create an Azure Functions app.

!!! question "Where did these commands come from?"

    The `func` commands mentioned below were recommended by the Azure Functions web application, specifically the
    "create function" dialog that pops up when adding a function to an existing Azure Functions app.

## Install Azure Functions core tools

Installing the Azure Functions core tools grants access to the `func` program.

```shell
npm install --global azure-functions-core-tools
```

## Initialize app

```shell
mkdir FunctionApp;
cd FunctionApp;

func init;
# when prompted, select "dotnet (isolated process)" for the worker runtime
# when prompted, select "c#-isolated" for the language
```

## Generate function

```shell
func new
# when prompted, select "BlobTrigger" as the template
# when prompted, use "LoggingFunction" as the function name
```

## Configure .Net

Install the latest version of the .Net framework:

```shell
brew install dotnet
```

The new app was likely initialized to use an older version of .Net.
To upgrade the app's .Net version:

1. Import the project into Rider.
2. Right-click the project in the solution view of the explorer panel.
3. Select "Properties...".
4. Upgrade the .Net version using the dropdown menu.

To verify the update:

1. Go to the file system view of the explorer panel.
2. Open the `csproj` file.
3. Verify the value within the `TargetFramework` tags.

!!! tip "Updating the C# project file"

    Most configurations that impact the `csproj` file -- version upgrades, package installations, etc. -- can be
    performed in the Rider UI, which will generate the corresponding XML.
    Direct editing of the project file is rarely required.

## Run storage emulator

The new function triggers on blob storage activity.
Therefore, to test the function locally, a local emulator must pretend to be an Azure Storage account.
Enter [Azurite](https://learn.microsoft.com/en-us/azure/storage/common/storage-use-azurite?tabs=docker-hub):

```shell
docker pull mcr.microsoft.com/azure-storage/azurite;

docker run --detach \
    --publish 10000:10000 \
    --publish 10001:10001 \
    --publish 10002:10002 \
    mcr.microsoft.com/azure-storage/azurite;
```

!!! info "Azurite ports"

    By default, Azurite uses three ports for its three services: its blob service uses port 10,000, its queue service
    uses port 10,001, and its table service uses port 10,002.

## Run storage explorer

To interact with the storage emulator:

1. Download and run [Azure Storage Explorer](https://azure.microsoft.com/en-us/products/storage/storage-explorer/).
2. In the "Get Started" tab, select "Attach to a resource".
3. Select "Local storage emulator".
4. Accept the default setup.
5. Expand the resulting storage account in the explorer panel.
6. Right-click "Blob Containers".
7. Select "Create Blob Container".
8. Name the new blob container `samples-workitems`.

!!! info "Blob container names"

    Technically, the new blob container can be named anything, but `samples-workitems` is the name already used by the
    generated app.
    Try searching the project for where it appears!

## Simulate environment variable

When running in Azure, functions get environment variables from their parent app's settings.
When running locally, however, functions will pull environment variables from `local.settings.json`.

To demonstrate this, add a string to the `Values` object:

```json title="local.settings.json" hl_lines="6"
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "UseDevelopmentStorage=true",
    "FUNCTIONS_WORKER_RUNTIME": "dotnet-isolated",
    "GREETING": "Hello, world!"
  }
}
```

Then update `LoggingFunction` to log the value of the environment variable:

```c# title="LoggingFunction.cs"
_logger.LogInformation(
    "Greeting: {Greeting}",
    Environment.GetEnvironmentVariable("GREETING")
);
```

## Start app

```shell
func start
```

## Trigger function

Create a file that will be used to trigger the function:

```shell
echo "bar" > foo.txt
```

1. Return to Azure Storage Explorer.
2. Open the `samples-workitems` container.
3. Select "Upload" → "Upload Files...".
4. Upload `foo.txt` using the defaults.
5. Return to the running app.

Log output should show:

1. The function was triggered
2. The name of the blob/file that triggered the function
3. The content of the blob
4. The value of the `GREETING` environment variable

## Deploy function

### From local machine

!!! danger "Deploying from a local machine"

    Early in development, it may make sense to deploy a work-in-progress function directly from a local machine.
    It shouldn't be long, however, until a remote pipeline assumes deployment responsibilities.

Deploying a function requires the Azure CLI:

```shell
brew install azure-cli
```

Log in with your Azure credentials, then publish the function to the target Azure Functions app:

```shell
az login;
func azure functionapp publish FunctionApp;
```

### From GitHub pipeline

Microsoft has developed a [GitHub action](https://github.com/Azure/functions-action) for deploying functions to Azure.
This action may use an Azure "publish profile" in XML format.
To download an Azure app's publish profile, find the app in the Azure web UI.
At the top of the page will be a button labeled "Get publish profile".
