<properties 
	pageTitle="기존 Blob을 미디어 서비스 자산에 복사" 
	description="이 항목에서는 기존 Blob을 미디어 서비스 자산에 복사하는 방법을 보여줍니다." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="10/05/2015" 
	ms.author="juliako"/>

#기존 Blob을 미디어 서비스 자산에 복사

이 항목에서는 저장소 계정에서 새로운 Microsoft Azure 미디어 서비스 자산으로 Blob을 복사하는 방법을 보여줍니다.

Blob은 미디어 서비스 계정에 연결된 저장소 계정 또는 미디어 서비스 계정에 연결되지 않은 저장소 계정에 존재할 수 있습니다. 이 항목에서는 저장소 계정에서 미디어 서비스 자산으로 Blob을 복사하는 방법을 보여줍니다. 데이터 센터 간에 복사할 수도 있습니다. 하지만 이 경우 요금이 발생할 수 있습니다. 가격 책정에 대한 자세한 내용은 [데이터 전송](https://azure.microsoft.com/pricing/#header-11)을 참조하세요.

>[AZURE.NOTE] 미디어 서비스 API를 사용하지 않고 미디어 서비스에서 생성된 Blob 컨테이너의 콘텐츠를 변경하려고 하면 안 됩니다.

##샘플 다운로드

[여기](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/)에서 샘플을 가져와서 실행합니다.

##필수 조건

- 신규 또는 기존 Azure 구독의 미디어 서비스 계정 2개. [미디어 서비스 계정을 만드는 방법](media-services-create-account.md)(영문)을 참조하십시오.
- 운영 체제: Windows 10, Windows 7, Windows 2008 R2 또는 Windows 8.
- .NET Framework 4.5.
- Visual Studio 2010 SP1(Professional, Premium, Ultimate 또는 Express) 이상.

##프로젝트 설정

이 섹션에서는 C# 콘솔 응용 프로그램 프로젝트를 만들고 설정합니다.

1. Visual Studio 2012 또는 Visual Studio 2010 SP1을 사용하여 C# 콘솔 응용 프로그램 프로젝트가 포함된 새 솔루션을 만듭니다. 
2. 이름에 CopyExistingBlobsIntoAsset을 입력하고 확인을 클릭합니다.
1. Nuget을 사용하여 미디어 서비스 관련된 DLL에 참조를 추가합니다. Visual Studio 주 메뉴에서 도구 -> 라이브러리 패키지 관리자 -> 패키지 관리자 콘솔을 선택합니다. 콘솔 창에 Install-Package windowsazure.mediaservices를 입력하고 Enter를 누릅니다.
1. 이 프로젝트에 필요한 다른 참조를 추가합니다: System.Configuration.
1. 기본적으로 Programs.cs 파일에 추가한 using 문을 다음 중 하나로 바꿉니다.
		
		using System;
		using System.Linq;
		using System.Configuration;
		using System.IO;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Collections.Generic;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure;
		using System.Web;
		using Microsoft.WindowsAzure.Storage.Blob;
		using Microsoft.WindowsAzure.Storage.Auth;

1. appSettings 섹션을 .config 파일에 추가하고 미디어 서비스 및 저장소 키에 기반한 값과 이름 값을 업데이트합니다.

		<appSettings>
		  <add key="MediaServicesAccountName" value="Media-Services-Account-Name"/>
		  <add key="MediaServicesAccountKey" value="Media-Services-Account-Key"/>
		  <add key="MediaServicesStorageAccountName" value="Media-Services-Storage-Account-Name"/>
		  <add key="MediaServicesStorageAccountKey" value="Media-Services-Storage-Account-Key"/>
		  <add key="ExternalStorageAccountName" value="External-Storage-Account-Name"/>
		  <add key="ExternalStorageAccountKey" value="External-Storage-Account-Key"/>
		</appSettings>


##저장소 계정에서 미디어 서비스 자산으로 Blob 복사

아래 코드 예제에서는 다음 작업을 수행합니다.

1. CloudMediaContext 인스턴스를 만듭니다. 
1. CloudStorageAccount 인스턴스인 \_sourceStorageAccount 및 \_destinationStorageAccount를 만듭니다.
1. 로컬 디렉터리에서 \_sourceStorageAccount에 있는 Blob 컨테이너로 부드러운 스트리밍 파일을 업로드합니다.
1. 새 자산을 만듭니다. 이 자산용으로 만든 Blob 컨테이너는 \_destinationStorageAccount에 위치합니다.
1. Azure 저장소 SDK를 사용하여 자산과 연결된 컨테이너에 지정된 Blob을 복사합니다.

>[AZURE.NOTE]로케이터가 만료된 경우 복사 작업에서 예외가 발생하지 않습니다.

1. .ism 파일을 주 파일로 사용하도록 설정합니다.
1. 자산과 연결된 OnDemandOrigin 로케이터의 부드러운 스트리밍 URL을 만듭니다. 
		
		class Program
		{
		    // Read values from the App.config file. 
		    static string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
		    static string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		    static string _storageAccountName = ConfigurationManager.AppSettings["MediaServicesStorageAccountName"];
		    static string _storageAccountKey = ConfigurationManager.AppSettings["MediaServicesStorageAccountKey"];
		    static string _externalStorageAccountName = ConfigurationManager.AppSettings["ExternalStorageAccountName"];
		    static string _externalStorageAccountKey = ConfigurationManager.AppSettings["ExternalStorageAccountKey"];
		
		    private static MediaServicesCredentials _cachedCredentials = null;
		    private static CloudMediaContext _context = null;
		
		    private static CloudStorageAccount _sourceStorageAccount = null;
		    private static CloudStorageAccount _destinationStorageAccount = null;
		
		    static void Main(string[] args)
		    {
		        _cachedCredentials = new MediaServicesCredentials(
		                        _accountName,
		                        _accountKey);
		        // Use the cached credentials to create CloudMediaContext.
		        _context = new CloudMediaContext(_cachedCredentials);
		
		        // In this example the storage account from which we copy blobs is not 
		        // associated with the Media Services account into which we copy blobs.
		        // But the same code will work for coping blobs from a storage account that is 
		        // associated with the Media Services account.
		        //
		        // Get a reference to a storage account that is not associated with a Media Services account
		        // (an external account).  
		        StorageCredentials externalStorageCredentials =
		            new StorageCredentials(_externalStorageAccountName, _externalStorageAccountKey);
		        _sourceStorageAccount = new CloudStorageAccount(externalStorageCredentials, true);
		
		        //Get a reference to the storage account that is associated with a Media Services account. 
		        StorageCredentials mediaServicesStorageCredentials =
		            new StorageCredentials(_storageAccountName, _storageAccountKey);
		        _destinationStorageAccount = new CloudStorageAccount(mediaServicesStorageCredentials, false);
		
		        // Upload Smooth Streaming files into a storage account.
		        string localMediaDir = @"C:\supportFiles\streamingfiles";
		        CloudBlobContainer blobContainer =
		            UploadContentToStorageAccount(localMediaDir);
		
		        // Create a new asset and copy the smooth streaming files into 
		        // the container that is associated with the asset.
		        IAsset asset = CreateAssetFromExistingBlobs(blobContainer);
		
		        // Get the streaming URL for the smooth streaming files 
		        // that were copied into the asset.   
		        string urlForClientStreaming = CreateStreamingLocator(asset);
		        Console.WriteLine("Smooth Streaming URL: " + urlForClientStreaming);
		
		        Console.ReadLine();
		    }
		
		    /// <summary>
		    /// Uploads content from a local directory into the specified storage account.
		    /// In this example the storage account is not associated with the Media Services account.
		    /// </summary>
		    /// <param name="localPath">The path from which to upload the files.</param>
		    /// <returns>The container that contains the uploaded files.</returns>
		    static public CloudBlobContainer UploadContentToStorageAccount(string localPath)
		    {
		        CloudBlobClient externalCloudBlobClient = _sourceStorageAccount.CreateCloudBlobClient();
		        CloudBlobContainer externalMediaBlobContainer = externalCloudBlobClient.GetContainerReference("streamingfiles");
		
		        externalMediaBlobContainer.CreateIfNotExists();
		
		        // Upload files to the blob container.  
		        DirectoryInfo uploadDirectory = new DirectoryInfo(localPath);
		        foreach (var file in uploadDirectory.EnumerateFiles())
		        {
		            CloudBlockBlob blob = externalMediaBlobContainer.GetBlockBlobReference(file.Name);
		
		            blob.UploadFromFile(file.FullName, FileMode.Open);
		        }
		
		        return externalMediaBlobContainer;
		    }
		
		    /// <summary>
		    /// Creates a new asset and copies blobs from the specifed storage account.
		    /// </summary>
		    /// <param name="mediaBlobContainer">The specified blob container.</param>
		    /// <returns>The new asset.</returns>
		    static public IAsset CreateAssetFromExistingBlobs(CloudBlobContainer mediaBlobContainer)
		    {
		        // Create a new asset. 
		        IAsset asset = _context.Assets.Create("CopyBlob_" + Guid.NewGuid(), AssetCreationOptions.None);
		
		        IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy", TimeSpan.FromHours(24), AccessPermissions.Write);
		        ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
		
		        CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();
		
		        // Get the asset container URI and Blob copy from mediaContainer to assetContainer. 
		        string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
		
		        CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);
		
		        if (assetContainer.CreateIfNotExists())
		        {
		            assetContainer.SetPermissions(new BlobContainerPermissions
		            {
		                PublicAccess = BlobContainerPublicAccessType.Blob
		            });
		        }
		
		        var blobList = mediaBlobContainer.ListBlobs();
		        foreach (var sourceBlob in blobList)
		        {
		            var assetFile = asset.AssetFiles.Create((sourceBlob as ICloudBlob).Name);
		            CopyBlob(sourceBlob as ICloudBlob, assetContainer);
		            assetFile.ContentFileSize = (sourceBlob as ICloudBlob).Properties.Length;
		            assetFile.Update();
		        }
		
		        destinationLocator.Delete();
		        writePolicy.Delete();
		
		        // Since we copied a set of Smooth Streaming files, 
		        // set the .ism file to be the primary file. 
		        SetISMFileAsPrimary(asset);
		
		        return asset;
		    }
		
		    /// <summary>
		    /// Creates the OnDemandOrigin locator in order to get the streaming URL.
		    /// </summary>
		    /// <param name="asset">The asset that contains the smooth streaming files.</param>
		    /// <returns>The streaming URL.</returns>
		    static public string CreateStreamingLocator(IAsset asset)
		    {
		        var ismAssetFile = asset.AssetFiles.ToList().
		            Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).First();
		
		        // Create a 30-day readonly access policy. 
            // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
		        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
		            TimeSpan.FromDays(30),
		            AccessPermissions.Read);
		
		        // Create a locator to the streaming content on an origin. 
		        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
		            policy,
		            DateTime.UtcNow.AddMinutes(-5));
		
		        return originLocator.Path + ismAssetFile.Name + "/manifest";
		    }
		
		    /// <summary>
		    /// Copies the specified blob into the specified container.
		    /// </summary>
		    /// <param name="sourceBlob">The source container.</param>
		    /// <param name="destinationContainer">The destination container.</param>
		    static private void CopyBlob(ICloudBlob sourceBlob, CloudBlobContainer destinationContainer)
            {
                var signature = sourceBlob.GetSharedAccessSignature(new SharedAccessBlobPolicy
                {
                    Permissions = SharedAccessBlobPermissions.Read,
                    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
                });

                ICloudBlob destinationBlob = destinationContainer.GetBlockBlobReference(sourceBlob.Name);

                if (destinationBlob.Exists())
                {
                    Console.WriteLine(string.Format("Destination blob '{0}' already exists. Skipping.", destinationBlob.Uri));
                }
                else
                {
                    try
                    {
                        Console.WriteLine(string.Format("Copy blob '{0}' to '{1}'", sourceBlob.Uri, destinationBlob.Uri));
                        destinationBlob.StartCopyFromBlob(new Uri(sourceBlob.Uri.AbsoluteUri + signature));


                        while (true)
                        {
                            // The StartCopyFromBlob is an async operation, 
                            // so we want to check if the copy operation is completed before proceeding. 
                            // To do that, we call FetchAttributes on the blob and check the CopyStatus. 
                            destinationBlob.FetchAttributes();
                            if (destinationBlob.CopyState.Status != CopyStatus.Pending)
                            {
                                break;
                            }
                            //It's still not completed. So wait for some time.
                            System.Threading.Thread.Sleep(1000);
                        }

                        Console.WriteLine("Final blob copy status = " + destinationBlob.CopyState.Status);
                    }
                    catch (Exception ex)
                    {
                        Console.WriteLine(string.Format("Error copying blob '{0}': {1}", sourceBlob.Name, ex.Message));
                    }
                }
            }
		
		    /// <summary>
		    /// Sets a file with the .ism extension as a primary file.
		    /// </summary>
		    /// <param name="asset">The asset that contains the smooth streaming files.</param>
		    static private void SetISMFileAsPrimary(IAsset asset)
		    {
		        var ismAssetFiles = asset.AssetFiles.ToList().
		            Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray();
		
		        if (ismAssetFiles.Count() != 1)
		            throw new ArgumentException("The asset should have only one, .ism file");
		
		        ismAssetFiles.First().IsPrimary = true;
		        ismAssetFiles.First().Update();
		    }
		}
 

##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0128_2016-->