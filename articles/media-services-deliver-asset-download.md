<properties urlDisplayName="Delivering Media Assets" pageTitle="미디어 자산을 제공하는 방법 - Azure" metaKeywords="" description="Learn about options for delivering media assets that have been uploaded to Media Services in Azure. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver an Asset by Download" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





<h1>방법: 다운로드를 통해 자산 제공</h1>
이 문서는 Azure 미디어 서비스 프로그래밍을 소개하는 시리즈 중 하나입니다. 이전 항목은 [방법: 자산 관리](../media-services-manage-assets/)입니다.

이 항목에서는 미디어 서비스에 업로드된 미디어 자산을 제공하는 옵션에 대해 논의합니다. 다양한 응용 프로그램 시나리오에서 미디어 서비스 콘텐츠를 제공할 수 있습니다. 로케이터를 사용하여 미디어 자산을 다운로드하거나 미디어 자산에 액세스할 수 있습니다. 미디어 콘텐츠를 다른 응용 프로그램 또는 다른 콘텐츠 공급자에게 보낼 수 있습니다. 성능 및 확장성 향상을 위해 Azure CDN과 같은 CDN(콘텐츠 배달 네트워크)을 사용하여 콘텐츠를 제공할 수도 있습니다.

이 예제에서는 미디어 서비스에서 미디어 자산을 다운로드하는 방법을 보여 줍니다. 이 코드에서는 작업 ID를 사용하여 미디어 서비스 계정에 연결된 작업을 쿼리하고 **OutputMediaAssets** 컬렉션(작업 실행의 결과로 반환되는 하나 이상의 출력 미디어 자산 집합)에 액세스합니다. 이 예제에서는 작업에서 출력 미디어 자산을 다운로드하는 방법을 보여 주지만, 동일한 방법을 사용하여 다른 자산을 다운로드할 수도 있습니다.

<pre><code> 
// Download the output asset of the specified job to a local folder.
static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
{
    // This method illustrates how to download a single asset. 
    // However, you can iterate through the OutputAssets
    // collection, and download all assets if there are many. 

    // Get a reference to the job. 
    IJob job = GetJob(jobId);

    // Get a reference to the first output asset. If there were multiple 
    // output media assets you could iterate and handle each one.
    IAsset outputAsset = job.OutputMediaAssets[0];

	// Create a SAS locator to download the asset
    IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
    ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);

    BlobTransferClient blobTransfer = new BlobTransferClient
    {
        NumberOfConcurrentTransfers = 20,
        ParallelTransferThreadCount = 20
    };

    var downloadTasks = new List<Task>();
    foreach (IAssetFile outputFile in outputAsset.AssetFiles)
    {
        // Use the following event handler to check download progress.
        outputFile.DownloadProgressChanged += DownloadProgress;

        string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

        Console.WriteLine("File download path:  " + localDownloadPath);

        downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

        outputFile.DownloadProgressChanged -= DownloadProgress;
    }

    Task.WaitAll(downloadTasks.ToArray());

    return outputAsset;
}

static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
{
    Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
}
</code></pre>

자산 제공에 대한 자세한 내용은 다음을 참조하세요.
<ul>
<li><a href="http://msdn.microsoft.com/ko-kr/library/jj129575.aspx">Media Services for .NET을 사용하여 자산 제공(영문)</a></li>
<li><a href="http://msdn.microsoft.com/ko-kr/library/jj129578.aspx">Media Services REST API를 사용하여 자산 제공(영문)</a></li>
</ul>

<h2>다음 단계</h2>
이 항목에서는 Azure 저장소에서 자산을 다운로드하는 방법을 설명했습니다. 자산을 제공하는 다른 방법에 대한 자세한 내용은 [스트리밍 콘텐츠를 제공하는 방법](../media-services-deliver-streaming-content/) (영문) 항목을 참조하세요.

<!--HONumber=35_1-->
