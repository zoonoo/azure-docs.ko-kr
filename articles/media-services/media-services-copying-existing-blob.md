---
title: "저장소 계정에서 Azure Media Services 자산으로 Blob 복사 | Microsoft Docs"
description: "이 토픽에서는 기존 Blob을 Media Services 자산에 복사하는 방법을 보여 줍니다. 이 예제에서는 Azure Media Services .NET SDK Extensions를 사용합니다."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 6a63823f-f3c9-424c-91b8-566f70bec346
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/31/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: b74fbba254f73c1df388b2b0ff9134cc7b8a31e8
ms.openlocfilehash: acc4780ea16f5a5ef0b87788b72595f29f6dcd89


---
# <a name="copying-existing-blobs-into-a-media-services-asset"></a>기존 Blob을 Media Services 자산으로 복사
이 토픽에서는 [Azure Media Services .NET SDK Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions/)를 사용하여 저장소 계정에서 새로운 AMS(Azure Media Services) 자산으로 Blob을 복사하는 방법을 보여줍니다.

확장 메서드는 다음에서 작동합니다.

- 일반 자산
- 라이브 보관 자산(FragBlob 형식)
- 다른 Media Services 계정에 속하는 원본 및 대상 자산(다른 데이터 센터 사이인 경우 포함) 하지만 이 경우 요금이 발생할 수 있습니다. 가격 책정에 대한 자세한 내용은 [데이터 전송](https://azure.microsoft.com/pricing/#header-11)을 참조하세요.

> [!NOTE]
> 미디어 서비스 API를 사용하지 않고 미디어 서비스에서 생성된 Blob 컨테이너의 콘텐츠를 변경하려고 하면 안 됩니다.
> 

이 토픽에서는 두 가지 코드 샘플을 보여줍니다.

1. 한 AMS 계정의 자산에 있는 Blob을 다른 AMS 계정의 새 자산으로 복사합니다.
2. 일부 저장소 계정에서 AMS 계정의 새 자산으로 Blob을 복사합니다.

## <a name="copy-blobs-between-two-ams-accounts"></a>두 AMS 계정 간에 Blob 복사  

### <a name="prerequisites"></a>필수 조건

두 개의 Media Services 계정이 필요합니다. [Media Services 계정을 만드는 방법](media-services-portal-create-account.md) 토픽을 참조하세요.

### <a name="download-sample"></a>샘플 다운로드
이 문서의 단계를 수행하거나 [여기](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/)에서 이 문서에 설명된 코드가 포함된 샘플을 다운로드할 수 있습니다.

### <a name="set-up-your-project"></a>프로젝트 설정

1. Visual Studio를 사용하여 C# 콘솔 응용 프로그램 프로젝트가 포함된 새 솔루션을 만듭니다. 
3. [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices.extensions)을 사용하여 Azure Media Services .NET SDK Extensions(windowsazure.mediaservices.extensions)를 설치한 후 추가합니다. 이 패키지를 설치하면 미디어 서비스 .NET SDK도 설치되고 다른 모든 필수 종속성이 추가됩니다.
4. 이 프로젝트에 필요한 다른 참조를 추가합니다: System.Configuration.
6. appSettings 섹션을 .config 파일에 추가하고 Media Services 계정, 대상 저장소 계정 및 원본 자산 ID에 따라 값을 업데이트합니다. 
   
        <appSettings>
              <add key="MediaServicesSourceAccountName" value="name"/>
              <add key="MediaServicesSourceAccountKey" value="key"/>
              <add key="MediaServicesDestAccountName" value="name"/>
              <add key="MediaServicesDestAccountKey" value="key"/>
              <add key="DestStorageAccountName" value="name"/>
              <add key="DestStorageAccountKey" value="key"/>
              <add key="SourceAssetID" value="nb:cid:UUID:assetID"/>       
        </appSettings>

### <a name="copy-blobs-from-an-asset-in-one-ams-account-into-an-asset-in-another-ams-account"></a>한 AMS 계정의 자산에 있는 Blob을 다른 AMS 계정의 자산으로 복사합니다.

다음 코드에서는 확장 **IAsset.Copy** 메서드를 사용하여 단일 확장을 통해 원본 자산의 모든 파일을 대상 자산으로 복사합니다. 비동기 지원에 따른 추가적인 오버헤드가 있습니다.

    using System;
    using System.Linq;
    using System.Configuration;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.Storage.Auth;
    
    namespace CopyExistingBlobsIntoAsset
    {
        class Program
        {
            static string _sourceAaccountName = ConfigurationManager.AppSettings["MediaServicesSourceAccountName"];
            static string _sourceAccountKey = ConfigurationManager.AppSettings["MediaServicesSourceAccountKey"];
            static string _destAccountName = ConfigurationManager.AppSettings["MediaServicesDestAccountName"];
            static string _destAccountKey = ConfigurationManager.AppSettings["MediaServicesDestAccountKey"];
            static string _destStorageAccountName = ConfigurationManager.AppSettings["DestStorageAccountName"];
            static string _destStorageAccountKey = ConfigurationManager.AppSettings["DestStorageAccountKey"];
            static string _sourceAssetID = ConfigurationManager.AppSettings["SourceAssetID"];
            
    
            private static CloudMediaContext _sourceContext = null;
            private static CloudMediaContext _destContext = null;
    
            static void Main(string[] args)
            {
                // Create the context for your source Media Services account.
    
                // Use the cached credentials to create CloudMediaContext.
                _sourceContext = new CloudMediaContext(new MediaServicesCredentials(
                    _sourceAaccountName,
                    _sourceAccountKey));

                // Create the context for your destination Media Services account.
                _destContext = new CloudMediaContext(new MediaServicesCredentials(
                    _destAccountName,
                    _destAccountKey));
    
                // Get the credentials of the default Storage account bound to your destination Media Services account.
                StorageCredentials destinationStorageCredentials =
                    new StorageCredentials(_destStorageAccountName, _destStorageAccountKey);
    
                // Get a reference to the source asset in the source context.
                IAsset sourceAsset = _sourceContext.Assets.Where(a => a.Id == _sourceAssetID).First();
    
                // Create an empty destination asset in the destination context.
                IAsset destinationAsset = _destContext.Assets.Create(sourceAsset.Name, AssetCreationOptions.None);
    
                // Copy the files in the source asset instance into the destination asset instance.
                // There is an additional overload with async support.
                sourceAsset.Copy(destinationAsset, destinationStorageCredentials);

                
            }
        }
    }


## <a name="copy-blobs-from-a-storage-account-into-an-ams-account"></a>저장소 계정의 Blob을 AMS 계정으로 복사 

### <a name="prerequisites"></a>필수 조건

- Blob를 복사할 저장소 계정 한 개가 필요합니다.
- Blob를 복사해 넣을 AMS 계정 한 개가 필요합니다.

### <a name="set-up-your-project"></a>프로젝트 설정

1. Visual Studio를 사용하여 C# 콘솔 응용 프로그램 프로젝트가 포함된 새 솔루션을 만듭니다. 
3. [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices.extensions)을 사용하여 Azure Media Services .NET SDK Extensions(windowsazure.mediaservices.extensions)를 설치한 후 추가합니다. 이 패키지를 설치하면 미디어 서비스 .NET SDK도 설치되고 다른 모든 필수 종속성이 추가됩니다.
4. 이 프로젝트에 필요한 다른 참조를 추가합니다: System.Configuration.
6. appSettings 섹션을 .config 파일에 추가하고 원본 저장소 및 대상 AMS 계정에 기반한 값을 업데이트합니다.
   
          <appSettings>
            <add key="MediaServicesAccountName" value="name" />
            <add key="MediaServicesAccountKey" value="key" />
            <add key="MediaServicesStorageAccountName" value="name" />
            <add key="MediaServicesStorageAccountKey" value="key" />
            <add key="SourceStorageAccountName" value="name" />
            <add key="SourceStorageAccountKey" value="key" />
          </appSettings>

### <a name="copy-blobs-from-some-storage-account-into-a-new-asset-in-a-ams-account"></a>일부 저장소 계정에서 AMS 계정의 새 자산으로 Blob 복사

다음 코드는 저장소 계정에서 Media Services 자산으로 Blob을 복사하는 데 사용됩니다. 

    using System;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    
    namespace CopyExistingBlobsIntoAsset
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
            private static readonly string _mediaServicesStorageAccountName =
                ConfigurationManager.AppSettings["MediaServicesStorageAccountName"];
            private static readonly string _mediaServicesStorageAccountKey =
                ConfigurationManager.AppSettings["MediaServicesStorageAccountKey"];
            private static readonly string _sourceStorageAccountName =
         ConfigurationManager.AppSettings["SourceStorageAccountName"];
            private static readonly string _sourceStorageAccountKey =
                ConfigurationManager.AppSettings["SourceStorageAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static CloudStorageAccount _sourceStorageAccount = null;
            private static CloudStorageAccount _destinationStorageAccount = null;
    
            static void Main(string[] args)
            {
    
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey));
    
                _sourceStorageAccount = 
                    new CloudStorageAccount(new StorageCredentials(_sourceStorageAccountName, 
                        _sourceStorageAccountKey), true);
    
                _destinationStorageAccount = 
                    new CloudStorageAccount(new StorageCredentials(_mediaServicesStorageAccountName, 
                        _mediaServicesStorageAccountKey), true);
                
                CloudBlobClient sourceCloudBlobClient = 
                    _sourceStorageAccount.CreateCloudBlobClient();
                CloudBlobContainer sourceContainer = 
                    sourceCloudBlobClient.GetContainerReference("NameOfBlobContainerYouWantToCopy");
    
                CreateAssetFromExistingBlobs(sourceContainer);
            }
            
            static public IAsset CreateAssetFromExistingBlobs(CloudBlobContainer sourceBlobContainer)
            {
                CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();
    
                // Create a new asset. 
                IAsset asset = _context.Assets.Create("NewAsset_" + Guid.NewGuid(), AssetCreationOptions.None);
    
                IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
                    TimeSpan.FromHours(24), AccessPermissions.Write);
    
                ILocator destinationLocator = 
                    _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    
                // Get the asset container URI and Blob copy from mediaContainer to assetContainer. 
                CloudBlobContainer destAssetContainer =
                    destBlobStorage.GetContainerReference((new Uri(destinationLocator.Path)).Segments[1]);
    
                if (destAssetContainer.CreateIfNotExists())
                {
                    destAssetContainer.SetPermissions(new BlobContainerPermissions
                    {
                        PublicAccess = BlobContainerPublicAccessType.Blob
                    });
                }
    
                var blobList = sourceBlobContainer.ListBlobs();
    
                foreach (var sourceBlob in blobList)
                {
                    var assetFile = asset.AssetFiles.Create((sourceBlob as ICloudBlob).Name);
    
                    ICloudBlob destinationBlob = destAssetContainer.GetBlockBlobReference(assetFile.Name);
    
                    // Call the CopyBlobHelpers.CopyBlobAsync extension method to copy blobs.
                    using (Task task = 
                        CopyBlobHelpers.CopyBlobAsync((CloudBlockBlob)sourceBlob, 
                            (CloudBlockBlob)destinationBlob, 
                            new BlobRequestOptions(), 
                            CancellationToken.None))
                    {
                        task.Wait();
                    }
    
                    assetFile.ContentFileSize = (sourceBlob as ICloudBlob).Properties.Length;
                    assetFile.Update();
                    Console.WriteLine("File {0} is of {1} size", assetFile.Name, assetFile.ContentFileSize);
                }
    
                asset.Update();
    
                destinationLocator.Delete();
                writePolicy.Delete();
    
                // Set the primary asset file.
                // If, for example, we copied a set of Smooth Streaming files, 
                // set the .ism file to be the primary file. 
                // If we, for example, copied an .mp4, then the mp4 would be the primary file. 
                var ismAssetFiles = asset.AssetFiles.ToList().
                    Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray();
    
                // The following code assigns the first .ism file as the primary file in the asset.
                // An asset should have one .ism file.  
                ismAssetFiles.First().IsPrimary = true;
                ismAssetFiles.First().Update();
    
                return asset;
            }
        }
    }
    
## <a name="next-steps"></a>다음 단계

이제 업로드된 자산을 인코딩할 수 있습니다. 자세한 내용은 [자산 인코딩](media-services-portal-encode.md)을 참조하세요.

또한 Azure Functions를 사용하여 구성된 컨테이너에 도착하는 파일에 따라 인코딩 작업을 트리거할 수도 있습니다. 자세한 내용은 [이 샘플](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ )을 참조하세요.

## <a name="media-services-learning-paths"></a>미디어 서비스 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Feb17_HO2-->


