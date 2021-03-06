Windows Azure Media Services .NET SDK Extensions
================================================

## What is it?
A NuGet package that contains a set of extension methods and helpers for the [Windows Azure Media Services .NET SDK](https://github.com/Azure/azure-sdk-for-media-services).

## Usage
Install the [WindowsAzure.MediaServices.Extensions](https://www.nuget.org/packages/WindowsAzure.MediaServices.Extensions) Nuget package by running `Install-Package WindowsAzure.MediaServices.Extensions` in the [Package Manager Console](http://docs.nuget.org/ndocs/tools/package-manager-console).

After installing the package, the **Microsoft.WindowsAzure.MediaServices.Client.Extensions** assembly will be added to your project's references.

## Extension Methods and Helpers available

### Create an empty Asset using a selection strategy for the Storage account
Create a new empty asset within one selected Storage account based on the default account selection strategy using a single extension method for the [AssetBaseCollection](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.assetbasecollection(v=azure.10).aspx) class. There is an additional overload with _async_ support.
```csharp
CloudMediaContext context = new CloudMediaContext("%accountName%", "%accountKey%");

// Get a list of the available Storage accounts to select from.
string[] storageAccountNames = this.context.StorageAccounts.ToList().Select(c => c.Name).ToArray();

// The asset name.
string assetName = "MyAssetName";

// The options for creating the new asset.
AssetCreationOptions assetCreationOptions = AssetCreationOptions.None;

// Create a new asset empty asset within one selected Storage account using a single extension method.
IAsset asset = context.Assets.Create(assetName, storageAccountNames, assetCreationOptions);
```

### Create an Asset from a single local file
Create a new asset by uploading a local file using a single extension method for the [AssetBaseCollection](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.assetbasecollection(v=azure.10).aspx) class. There are additional overloads with different parameters and _async_ support.
```csharp
CloudMediaContext context = new CloudMediaContext("%accountName%", "%accountKey%");

// The local path to the file to upload to the new asset.
string filePath = @"C:\AssetFile.wmv";

// The options for creating the new asset.
AssetCreationOptions assetCreationOptions = AssetCreationOptions.None;

// Create a new asset and upload a local file using a single extension method.
IAsset asset = context.Assets.CreateFromFile(filePath, assetCreationOptions);
```

### Create an Asset from a local folder
Create a new asset by uploading all the files in a local folder using a single extension method for the [AssetBaseCollection](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.assetbasecollection(v=azure.10).aspx) class. There are additional overloads with different parameters and _async_ support.
```csharp
CloudMediaContext context = new CloudMediaContext("%accountName%", "%accountKey%");

// The path to an existing local folder with the files to upload to the new asset.
string folderPath = @"C:\AssetFilesFolder";

// The options for creating the new asset.
AssetCreationOptions assetCreationOptions = AssetCreationOptions.None;

// Create a new asset and upload all the files in a local folder using a single extension method.
IAsset asset = context.Assets.CreateFromFolder(folderPath, assetCreationOptions);
```

### Create an Asset from a blob
Create a new asset by copying a source blob using a single extension method for the [AssetBaseCollection](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.assetbasecollection(v=azure.10).aspx) class. There are additional overloads with different parameters and _async_ support.

This extension method works with a source blob belonging to any Storage account (not necessary bound to the Media Services account and even across different datacenters).

```csharp
CloudMediaContext context = new CloudMediaContext("%accountName%", "%accountKey%");
StorageCredentials storageCredentials = new StorageCredentials("%storageAccountName%", "%storageAccountKey%");

// Get a reference to the source blob that will be copied in the new asset.
CloudBlockBlob sourceBlob = null;

// Create a new asset and copies the sourceBlob parameter using a single extension method.
IAsset asset = context.Assets.CreateFromBlob(sourceBlob, storageCredentials, AssetCreationOptions.None);
```

### Copy an Asset
Copy all files in the source asset into the destination asset using a single extension method for the [IAsset](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.iasset(v=azure.10).aspx) interface. There is an additional overload with _async_ support.

This extension method works with:
* Regular assets
* Live archive assets (FragBlob format)
* Source and destination assets belonging to different Media Services accounts (even across different datacenters)

```csharp
CloudMediaContext context = new CloudMediaContext("%accountName%", "%accountKey%");

// Get a reference to the source asset.
string sourceAssetId = "%sourceAssetId%";
IAsset sourceAsset = context.Assets.Where(a => a.Id == sourceAssetId).First();

// Create an empty destination asset where the source asset files are going to be copied.
IAsset destinationAsset = context.Assets.Create("Asset Copy", AssetCreationOptions.None);
StorageCredentials destinationStorageCredentials = new StorageCredentials("%storageAccountName%", "%storageAccountKey%");

// Copy the files in the 'sourceAsset' instance into the 'destinationAsset' instance.
sourceAsset.Copy(destinationAsset, destinationStorageCredentials);
```

### Generate Asset Files from Blob storage
Generate the asset files of an existing asset using a single extension method for the [IAsset](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.iasset(v=azure.10).aspx) interface. You can use this method after uploading content directly to the asset container in Azure Blob storage. This method leverages the [CreateFileInfos REST API Function](https://msdn.microsoft.com/library/azure/jj683097(v=azure.10).aspx#createfileinfos). There is an additional overload with _async_ support.
```csharp
CloudMediaContext context = new CloudMediaContext("%accountName%", "%accountKey%");

// Create an empty asset.
IAsset asset = context.Assets.Create("MyAssetName", AssetCreationOptions.None);

// Upload content to the previous asset directly to its Blob storage container.
// You can use a SAS locator with Write permissions to do this.
// ...

// Generate all the asset files in the asset from its Blob storage container using a single extension method.
asset.GenerateFromStorage();
```

### Download Asset Files to a local folder
Download all the asset files in an asset using a single extension method for the [IAsset](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.iasset(v=azure.10).aspx) interface. There are additional overloads with different parameters and _async_ support.
```csharp
CloudMediaContext context = new CloudMediaContext("%accountName%", "%accountKey%");

// The asset with the asset files to download. Get a reference to it from the context.
IAsset asset = null;

// The path to an existing local folder where to download all the asset files in the asset.
string folderPath = @"C:\AssetFilesFolder";

// Download all the asset files to a local folder using a single extension method.
asset.DownloadToFolder(folderPath);
```

### Get Asset metadata
Get the parsed [Media Encoder Standard Output Metadata](https://msdn.microsoft.com/library/azure/dn783217.aspx) for the whole asset out of the metadata asset file using a single extension method for the [IAsset](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.iasset(v=azure.10).aspx) interface. There are additional overloads with different parameters and _async_ support.
```csharp
// The asset encoded with the Media Encoder Standard processor. Get a reference to it from the context.
IAsset asset = null;

// Get an enumerator with the metadata for all the asset files.
IEnumerable<AssetFileMetadata> manifestAssetFile = asset.GetMetadata();
```

### Get Asset File metadata
Get the parsed [Media Encoder Standard Output Metadata](https://msdn.microsoft.com/library/azure/dn783217.aspx) for an asset file out of the metadata asset file using a single extension method for the [IAssetFile](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.iassetfile(v=azure.10).aspx) interface. There are additional overloads with different parameters and _async_ support.
```csharp
// The asset encoded with the Media Encoder Standard processor. Get a reference to it from the context.
IAsset asset = null;

// Get a SAS locator for the asset (make sure to create one first).
ILocator sasLocator = asset.Locators.Where(l => l.Type == LocatorType.Sas).First();

// Get one of the asset files.
IAssetFile assetFile = asset.AssetFiles.ToList().Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase)).First();

// Get the metadata for the asset file.
AssetFileMetadata manifestAssetFile = assetFile.GetMetadata(sasLocator);
```

### Get manifest Asset File
Get a reference to the asset file that represents the ISM manifest using a single extension method for the [IAsset](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.iasset(v=azure.10).aspx) interface.
```csharp
// The asset with multi-bitrate content. Get a reference to it from the context.
IAsset asset = null;

// Get the asset file representing the ISM manifest.
IAssetFile manifestAssetFile = asset.GetManifestAssetFile();
```

### Create a Locator
Create a locator and its associated access policy using a single extension method for the [LocatorBaseCollection](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.locatorbasecollection(v=azure.10).aspx) class. There are additional overloads with different parameters and _async_ support.
```csharp
CloudMediaContext context = new CloudMediaContext("%accountName%", "%accountKey%");

// The asset used to create the locator. Get a reference to it from the context.
IAsset asset = null;

// The locator type.
LocatorType locatorType = LocatorType.OnDemandOrigin;

// The permissions for the locator's access policy.
AccessPermissions accessPolicyPermissions = AccessPermissions.Read | AccessPermissions.List;

// The duration for the locator's access policy.
TimeSpan accessPolicyDuration = TimeSpan.FromDays(30);

// Create a locator and its associated access policy using a single extension method.
ILocator locator = context.Locators.Create(locatorType, asset, accessPolicyPermissions, accessPolicyDuration);
```

### Get Smooth Streaming URL from an Asset
Get the Smooth Streaming URL of a multi-bitrate Smooth Streaming or MP4 asset using a single extension method for the [IAsset](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.iasset(v=azure.10).aspx) interface. This methods requires the asset to contain an ISM manifest asset file and that you previously created an Origin locator for the asset; otherwise it returns _null_.
```csharp
// The asset with multi-bitrate Smooth Streaming or MP4 content. Get a reference to it from the context.
IAsset asset = null;

// Make sure to create an Origin locator for the asset.

// Get the Smooth Streaming URL of the asset for adaptive streaming.
Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
```

### Get Smooth Streaming URL from a Locator
Get the Smooth Streaming URL of a multi-bitrate Smooth Streaming or MP4 asset using a single extension method for the [ILocator](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.ilocator(v=azure.10).aspx) interface. This methods requires the locator to be of type [LocatorType.OnDemandOrigin](https://msdn.microsoft.com/en-US/library/microsoft.windowsazure.mediaservices.client.locatortype(v=azure.10).aspx), and its asset to contain an ISM manifest asset file.
```csharp
// The Origin locator for the multi-bitrate Smooth Streaming or MP4 asset. Get a reference to it from the context.
ILocator originLocator = null;

// Get the Smooth Streaming URL of the locator's asset for adaptive streaming.
Uri smoothStreamingUri = originLocator.GetSmoothStreamingUri();
```

### Get HLS version 4 URL from an Asset
Get the HLS version 4 URL of a multi-bitrate Smooth Streaming or MP4 asset using a single extension method for the [IAsset](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.iasset(v=azure.10).aspx) interface. This methods requires the asset to contain an ISM manifest asset file and that you previously created an Origin locator for the asset; otherwise it returns _null_.
```csharp
// The asset with multi-bitrate Smooth Streaming or MP4 content. Get a reference to it from the context.
IAsset asset = null;

// Make sure to create an Origin locator for the asset.

// Get the HLS version 4 URL of the asset for adaptive streaming.
Uri hlsv4Uri = asset.GetHlsUri();
```

### Get HLS version 4 URL from a Locator
Get the HLS version 4 URL of a multi-bitrate Smooth Streaming or MP4 asset using a single extension method for the [ILocator](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.ilocator(v=azure.10).aspx) interface. This methods requires the locator to be of type [LocatorType.OnDemandOrigin](https://msdn.microsoft.com/en-US/library/microsoft.windowsazure.mediaservices.client.locatortype(v=azure.10).aspx), and its asset to contain an ISM manifest asset file.
```csharp
// The Origin locator for the multi-bitrate Smooth Streaming or MP4 asset. Get a reference to it from the context.
ILocator originLocator = null;

// Get the HLS version 4 URL of the locator's asset for adaptive streaming.
Uri hlsv4Uri = originLocator.GetHlsUri();
```

### Get HLS version 3 URL from an Asset
Get the HLS version 3 URL of a multi-bitrate Smooth Streaming or MP4 asset using a single extension method for the [IAsset](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.iasset(v=azure.10).aspx) interface. This methods requires the asset to contain an ISM manifest asset file and that you previously created an Origin locator for the asset; otherwise it returns _null_.
```csharp
// The asset with multi-bitrate Smooth Streaming or MP4 content. Get a reference to it from the context.
IAsset asset = null;

// Make sure to create an Origin locator for the asset.

// Get the HLS version 3 URL of the asset for adaptive streaming.
Uri hlsv3Uri = asset.GetHlsv3Uri();
```

### Get HLS version 3 URL from a Locator
Get the HLS version 3 URL of a multi-bitrate Smooth Streaming or MP4 asset using a single extension method for the [ILocator](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.ilocator(v=azure.10).aspx) interface. This methods requires the locator to be of type [LocatorType.OnDemandOrigin](https://msdn.microsoft.com/en-US/library/microsoft.windowsazure.mediaservices.client.locatortype(v=azure.10).aspx), and its asset to contain an ISM manifest asset file.
```csharp
// The Origin locator for the multi-bitrate Smooth Streaming or MP4 asset. Get a reference to it from the context.
ILocator originLocator = null;

// Get the HLS version 3 URL of the locator's asset for adaptive streaming.
Uri hlsv3Uri = originLocator.GetHlsv3Uri();
```

### Get MPEG-DASH URL from an Asset
Get the MPEG-DASH URL of a multi-bitrate Smooth Streaming or MP4 asset using a single extension method for the [IAsset](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.iasset(v=azure.10).aspx) interface. This methods requires the asset to contain an ISM manifest asset file and that you previously created an Origin locator for the asset; otherwise it returns _null_.
```csharp
// The asset with multi-bitrate Smooth Streaming or MP4 content. Get a reference to it from the context.
IAsset asset = null;

// Make sure to create an Origin locator for the asset.

// Get the MPEG-DASH URL of the asset for adaptive streaming.
Uri mpegDashUri = asset.GetMpegDashUri();
```

### Get MPEG-DASH URL from a Locator
Get the MPEG-DASH URL of a multi-bitrate Smooth Streaming or MP4 asset using a single extension method for the [ILocator](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.ilocator(v=azure.10).aspx) interface. This methods requires the locator to be of type [LocatorType.OnDemandOrigin](https://msdn.microsoft.com/en-US/library/microsoft.windowsazure.mediaservices.client.locatortype(v=azure.10).aspx), and its asset to contain an ISM manifest asset file.
```csharp
// The Origin locator for the multi-bitrate Smooth Streaming or MP4 asset. Get a reference to it from the context.
ILocator originLocator = null;

// Get the MPEG-DASH URL of the locator's asset for adaptive streaming.
Uri mpegDashUri = originLocator.GetMpegDashUri();
```

### Get SAS URL from an Asset File
Get the SAS URL of an asset file for progressive download using a single extension method for the [IAssetFile](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.iassetfile(v=azure.10).aspx) interface. This methods requires the parent asset to contain a SAS locator for the asset; otherwise it returns _null_. There is an additional overload that receives an [ILocator](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.ilocator(v=azure.10).aspx) as a parameter.
```csharp
// The asset with multi-bitrate MP4 content. Get a reference to it from the context.
IAsset asset = null;

// Make sure to create a SAS locator for the asset.
IAssetFile assetFile = asset.AssetFiles.ToList().Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase)).First();

// Get the SAS URL of the asset file for progressive download.
Uri sasUri = assetFile.GetSasUri();
```

### Get latest Media Processor by name
Get the latest version of a media processor filtering by its name using a single extension method for the [MediaProcessorBaseCollection](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.mediaprocessorbasecollection(v=azure.10).aspx) class.
```csharp
CloudMediaContext context = new CloudMediaContext("%accountName%", "%accountKey%");

// The media processor name.
string mediaEncoderStandardProcessorName = MediaProcessorNames.MediaEncoderStandard;
string mediaEncoderPremiumWorkflowProcessorName = MediaProcessorNames.MediaEncoderPremiumWorkflow;
string azureMediaFaceDetectorProcessorName = MediaProcessorNames.AzureMediaFaceDetector;
string azureMediaHyperlapseProcessorName = MediaProcessorNames.AzureMediaHyperlapse;
string azureMediaIndexerProcessorName = MediaProcessorNames.AzureMediaIndexer;
string azureMediaIndexer2PreviewProcessorName = MediaProcessorNames.AzureMediaIndexer2Preview;
string azureMediaMotionDetectorProcessorName = MediaProcessorNames.AzureMediaMotionDetector;
string azureMediaOCRProcessorName = MediaProcessorNames.AzureMediaOCR;
string azureMediaStabilizerProcessorName = MediaProcessorNames.AzureMediaStabilizer;
string azureMediaVideoThumbnailsProcessorName = MediaProcessorNames.AzureMediaVideoThumbnails;

// Get the latest version of a media processor by its name using a single extension method.
IMediaProcessor mediaEncoderStandardProcessor = context.MediaProcessors.GetLatestMediaProcessorByName(mediaEncoderStandardProcessorName);
IMediaProcessor mediaEncoderPremiumWorkflowProcessor = context.MediaProcessors.GetLatestMediaProcessorByName(mediaEncoderPremiumWorkflowProcessorName);
IMediaProcessor azureMediaFaceDetectorProcessor = context.MediaProcessors.GetLatestMediaProcessorByName(azureMediaFaceDetectorProcessorName);
IMediaProcessor azureMediaHyperlapseProcessor = context.MediaProcessors.GetLatestMediaProcessorByName(azureMediaHyperlapseProcessorName);
IMediaProcessor azureMediaIndexerProcessor = context.MediaProcessors.GetLatestMediaProcessorByName(azureMediaIndexerProcessorName);
IMediaProcessor azureMediaIndexer2PreviewProcessor = context.MediaProcessors.GetLatestMediaProcessorByName(azureMediaIndexer2PreviewProcessorName);
IMediaProcessor azureMediaMotionDetectorProcessor = context.MediaProcessors.GetLatestMediaProcessorByName(azureMediaMotionDetectorProcessorName);
IMediaProcessor azureMediaOCRProcessor = context.MediaProcessors.GetLatestMediaProcessorByName(azureMediaOCRProcessorName);
IMediaProcessor azureMediaStabilizerProcessor = context.MediaProcessors.GetLatestMediaProcessorByName(azureMediaStabilizerProcessorName);
IMediaProcessor azureMediaVideoThumbnailsProcessor = context.MediaProcessors.GetLatestMediaProcessorByName(azureMediaVideoThumbnailsProcessorName);
```

### Create a Job with a single Task
Create a job with a single task ready to be submitted using a single extension method for the [JobBaseCollection](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.jobbasecollection(v=azure.10).aspx) class. There is an additional overload with different parameters. 
```csharp
CloudMediaContext context = new CloudMediaContext("%accountName%", "%accountKey%");

// The media processor name used in the job's task.
string mediaProcessorName = MediaProcessorNames.MediaEncoderStandard;

// The task configuration.
string taskConfiguration = MediaEncoderStandardTaskPresetStrings.H264MultipleBitrate720p;

// The input asset for the task. Get a reference to it from the context.
IAsset inputAsset = null;

// The name for the output asset of the task.
string outputAssetName = "OutputAssetName";

// The options for creating the output asset of the task.
AssetCreationOptions outputAssetOptions = AssetCreationOptions.None;

// Create a job ready to be submitted with a single task with one input/output asset using a single extension method.
IJob job = context.Jobs.CreateWithSingleTask(mediaProcessorName, taskConfiguration, inputAsset, outputAssetName, outputAssetOptions);

// Submit the job and wait until it is completed to get the output asset.
// ...
```

### Start Job execution progress task to notify when its state or overall progress change
Start a [Task](https://msdn.microsoft.com/library/system.threading.tasks.task(v=azure.10).aspx) to monitor a job progress using a single extension method for the [IJob](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.ijob(v=azure.10).aspx) interface. The difference with the [IJob.GetExecutionProgressTask](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.ijob.getexecutionprogresstask(v=azure.10).aspx) method is that this extension invokes a callback when the job state or overall progress change. There is an additional overload with different parameters.
```csharp
CloudMediaContext context = new CloudMediaContext("%accountName%", "%accountKey%");

// The input asset for the task. Get a reference to it from the context.
IAsset inputAsset = null;

// Prepare a job ready to be submitted with a single task with one input/output asset using a single extension method.
IJob job = context.Jobs.CreateWithSingleTask(MediaProcessorNames.MediaEncoderStandard, MediaEncoderStandardTaskPresetStrings.H264MultipleBitrate720p, inputAsset, "OutputAssetName", AssetCreationOptions.None);

// Submit the job.
job.Submit();

// Start a task to monitor the job progress by invoking a callback when its state or overall progress change in a single extension method.
job = await job.StartExecutionProgressTask(
    j =>
    {
        Console.WriteLine("Current job state: {0}", j.State);
        Console.WriteLine("Current job progress: {0}", j.GetOverallProgress());
    },
    CancellationToken.None);
```

### Get Job overall progress
Get the overall progress of a job by calculating the average progress of all its tasks using a single extension method for the [IJob](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.ijob(v=azure.10).aspx) interface.
```csharp
CloudMediaContext context = new CloudMediaContext("%accountName%", "%accountKey%");

// The input asset for the task. Get a reference to it from the context.
IAsset inputAsset = null;

// Prepare a job ready to be submitted with a single task with one input/output asset using a single extension method.
IJob job = context.Jobs.CreateWithSingleTask(MediaProcessorNames.MediaEncoderStandard, MediaEncoderStandardTaskPresetStrings.H264MultipleBitrate720p, inputAsset, "OutputAssetName", AssetCreationOptions.None);

// Submit the job.
job.Submit();

// ...

// Refresh the job instance.
job = context.Jobs.Where(j => j.Id == job.Id).First();

// Get the overall progress of the job by calculating the average progress of all its tasks using a single extension method.
double jobOverallProgress = job.GetOverallProgress();
```

### Parse Media Services error messages in XML format
Parse exceptions with Windows Azure Media Services error messages in XML format.
```csharp
CloudMediaContext context = new CloudMediaContext("%accountName%", "%accountKey%");

// Create an empty asset.
IAsset asset = context.Assets.Create("MyAssetName", AssetCreationOptions.None);

try
{
    // Generate an error trying to delete the asset twice.
    asset.Delete();
    asset.Delete();
}
catch (Exception exception)
{
    // Parse the exception to get the error message from the Media Services XML response.
    Exception parsedException = MediaServicesExceptionParser.Parse(exception);
    string mediaServicesErrorMessage = parsedException.Message;
}
```
