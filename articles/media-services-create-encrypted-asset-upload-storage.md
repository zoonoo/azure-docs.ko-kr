<properties urlDisplayName="Create Encrypted Asset and Upload to Storage" pageTitle="암호화된 자산을 만들어 Azure 저장소에 업로드" metaKeywords="" description="암호화된 자산을 만든 다음 업로드하여 미디어 콘텐츠를 미디어 서비스로 가져오는 방법에 대해 알아봅니다." metaCanonical="" services="media-services" documentationCenter="" title="How to: Create an encrypted Asset and upload to storage" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />



<h1><a name="create-asset"> </a><span class="short header">방법: 암호화된 자산 만들기 및 저장소에 업로드</span></h1>

이 문서는 Azure 미디어 서비스 프로그래밍을 소개하는 시리즈 중 하나입니다. 이전 항목은 [미디어 서비스를 위한 컴퓨터 설정](http://go.microsoft.com/fwlink/?LinkID=301751&clcid=0x409)입니다.

미디어 콘텐츠를 미디어 서비스로 가져오려면 먼저 자산을 만들어 여기에 파일을 추가한 후 자산을 업로드합니다. 이 프로세스를 콘텐츠 수집이라고 합니다.  

자산을 만들 때는 세 가지의 암호화 옵션을 지정할 수 있습니다. 

- **AssetCreationOptions.None**: 암호화 없음. 암호화하지 않은 자산을 생성하려면 이 옵션을 설정해야 합니다.
- **AssetCreationOptions.CommonEncryptionProtected**: CENC(Common Encryption Protected) 파일의 경우. 예: PlayReady로 이미 암호화된 파일 집합 
- **AssetCreationOptions.StorageEncrypted**: 저장소 암호화. Azure 저장소로 업로드하기 전에 암호화되지 않은 입력 파일을 암호화합니다.

> WACOM.NOTE
> 미디어 서비스는 DRM(Digital Rights Manager)처럼 네트워크상이 아니라 자산에 대해 디스크상의 저장소 암호화 기능을 제공합니다.

아래 샘플 코드는 다음을 수행합니다. 

- 빈 자산을 만듭니다.
- 자산과 연결하려는 AssetFile 인스턴스를 생성합니다.
- 자산에 대한 액세스 권한과 기간을 정의하는 AccessPolicy 인스턴스를 생성합니다.
- 자산에 액세스 권한을 제공하는 Locator 인스턴스를 생성합니다.
- 단일 미디어 파일을 미디어 서비스로 업로드합니다. 

<pre><code>
static private IAsset CreateEmptyAsset(string assetName, AssetCreationOptions assetCreationOptions)
{
    var asset = _context.Assets.Create(assetName, assetCreationOptions);

    Console.WriteLine("Asset name: " + asset.Name);
    Console.WriteLine("Time created: " + asset.Created.Date.ToString());

    return asset;
}

static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
{
    var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
    var asset = CreateEmptyAsset(assetName, assetCreationOptions);

    var fileName = Path.GetFileName(singleFilePath);

    var assetFile = asset.AssetFiles.Create(fileName);

    Console.WriteLine("Created assetFile {0}", assetFile.Name);
    Console.WriteLine("Upload {0}", assetFile.Name);

    assetFile.Upload(singleFilePath);
    Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

    return asset;
}
</code></pre>

다음 코드는 자산을 만들고 여러 파일을 업로드하는 방법을 보여 줍니다.

<pre><code>
static public IAsset CreateAssetAndUploadMultipleFiles( AssetCreationOptions assetCreationOptions, string folderPath)
{
    var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

    var asset = CreateEmptyAsset(assetName, assetCreationOptions);

    var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                        AccessPermissions.Write | AccessPermissions.List);
    var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

    var blobTransferClient = new BlobTransferClient();
	blobTransferClient.NumberOfConcurrentTransfers = 20;
    blobTransferClient.ParallelTransferThreadCount = 20;

    blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

    var filePaths = Directory.EnumerateFiles(folderPath);

    Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

    if (!filePaths.Any())
    {
        throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
    }

    var uploadTasks = new List<Task>();
    foreach (var filePath in filePaths)
    {
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        Console.WriteLine("Created assetFile {0}", assetFile.Name);
                
        // It is recommended to validate AccestFiles before upload. 
        Console.WriteLine("Start uploading of {0}", assetFile.Name);
        uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
    }

    Task.WaitAll(uploadTasks.ToArray());
    Console.WriteLine("Done uploading the files");

    blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

    locator.Delete();
    accessPolicy.Delete();

    return asset;
}

static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
{
    if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
    {
        Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
    }
}

</code></pre>

<h2>다음 단계</h2>
이제 미디어 서비스에 자산을 업로드했으므로 [미디어 프로세서를 가져오는 방법][] 항목으로 이동하세요.

[미디어 프로세서를 가져오는 방법]: ../media-services-get-media-processor/

<!--HONumber=35.1-->
