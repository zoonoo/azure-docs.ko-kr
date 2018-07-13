---
title: Azure Content Moderator - 비디오 조정 | Microsoft Docs
description: 비디오 조정을 사용하여 가능한 성인 및 외설 콘텐츠를 검색합니다.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/02/2018
ms.author: sajagtap
ms.openlocfilehash: ef58f5990d4a0a19ab2b8c61b42ab2a0754dc6fa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377694"
---
# <a name="video-moderation"></a>비디오 조정

오늘날, 온라인 뷰어는 대중적인 국가별 소셜 미디어 웹 사이트에서 수십억 개의 비디오 뷰를 생성하며 점점 증가 추세에 있습니다. 잠재적인 성인 및 외설 콘텐츠를 예측하는 기계 학습 기반 서비스를 적용하여 조정 노력을 줄일 수 있습니다.

## <a name="sign-up-for-the-content-moderator-media-processor-public-preview"></a>Content Moderator 미디어 프로세서(공개 미리 보기)에 등록

### <a name="create-a-free-azure-account"></a>평가판 Azure 계정 만들기

Azure 계정이 아직 없는 경우 [여기에서 시작](https://azure.microsoft.com/free/)하여 무료 Azure 계정을 만듭니다.

### <a name="create-an-azure-media-services-account"></a>Azure Media Services 계정 만들기

Content Moderator의 비디오 기능은 AMS(Azure Media Services)에서 무료로 공개 미리 보기 **미디어 프로세서**로 사용할 수 있습니다.

Azure 구독에서 [Azure Media Services 계정을 만듭니다](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account).

### <a name="get-azure-active-directory-credentials"></a>Azure Active Directory 자격 증명 가져오기

   1. [Azure Media Services 포털 문서](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad)를 읽고 Azure Portal을 사용하여 Azure AD 인증 자격 증명을 가져오는 방법을 알아봅니다.
   1. [Azure Media Services .NET 문서](https://docs.microsoft.com/azure/media-services/media-services-dotnet-get-started-with-aad)를 읽고 .NET SDK에서 Azure Active Directory 자격 증명을 사용하는 방법을 알아봅니다.

   > [!NOTE]
   > 이 빠른 시작의 샘플 코드는 이러한 두 문서에 설명된 **서비스 주체 인증** 방법을 사용합니다.

AMS 자격 증명을 가져온 후에는 두 가지 방법으로 Content Moderator 미디어 프로세서를 사용해볼 수 있습니다.

## <a name="use-azure-media-services-explorer"></a>Azure Media Services 탐색기 사용

대화형 [AMS(Azure Media Services) 탐색기](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/)를 사용하여 AMS 계정을 찾아보고, 비디오를 업로드하고, Content Moderator 미디어 프로세서로 검사를 수행합니다. GitHub에서 [다운로드한 후 설치](https://github.com/Azure/Azure-Media-Services-Explorer/releases)하고 [소스 코드를 찾아보면서](http://github.com/Azure/Azure-Media-Services-Explorer) AMS SDK를 사용에 대해 자세히 알아봅니다.

![Content Moderator를 사용하는 Azure Media Services 탐색기](images/ams-explorer-content-moderator.PNG)

## <a name="net-quickstart-with-visual-studio-and-c"></a>Visual Studio 및 C#으로 .NET 빠른 시작

1. 솔루션에 새 **콘솔 앱(.NET Framework)** 프로젝트를 추가합니다.

   샘플 코드에서 프로젝트의 이름을 **VideoModeration**으로 지정합니다.

1. 이 프로젝트를 솔루션의 단일 시작 프로젝트로 선택합니다.

### <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

[NuGet](https://www.nuget.org/)에서 사용할 수 있는 다음 NuGet 패키지를 설치합니다.

- windowsazure.mediaservices
- windowsazure.mediaservices.extensions

### <a name="update-the-programs-using-statements"></a>프로그램의 using 문 업데이트

프로그램의 using 문을 수정합니다.

    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.IO;
    using System.Threading;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using System.Collections.Generic;


### <a name="initialize-application-specific-settings"></a>응용 프로그램 관련 설정 초기화

Program.cs의 **Program** 클래스에 다음 정적 필드를 추가합니다.

    // declare constants and globals
    private static CloudMediaContext _context = null;
    private static CloudStorageAccount _StorageAccount = null;

    // Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
    // a list of Blobs to be processed.
    static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
    static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
    static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

    private static StorageCredentials _StorageCredentials = null;

    // Azure Media Services authentication. See the quickstart for how to get these.
    private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
    private const string CLIENT_ID = "YOUR CLIENT ID"
    private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

    // REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
    private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

    // Content Moderator Media Processor Nam
    private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

    // Input and Output files in the current directory of the executable
    private const string INPUT_FILE = "VIDEO FILE NAME";
    private const string OUTPUT_FOLDER = "";

### <a name="create-a-preset-file-json"></a>미리 설정된 파일(json) 만들기

버전 번호를 사용하여 현재 디렉터리에 JSON 파일을 만듭니다.

    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";
    //Example file content:
    //        {
    //             "version": "2.0"
    //        }
    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

### <a name="add-the-following-code-to-the-main-method"></a>main 메서드에 다음 코드 추가

main 메서드는 먼저 Azure 미디어 컨텍스트를 만들고, 비디오가 Blob 저장소에 있는 경우에는 Azure Storage 컨텍스트를 만듭니다.
코드의 나머지 부분은 Azure Storage 컨테이너 내의 로컬 폴더, Blob 또는 여러 Blob에서 비디오를 검색합니다.
다른 코드 줄을 주석으로 처리하여 모든 옵션을 시도할 수 있습니다.

    // Create Azure Media Context
    CreateMediaContext();

    // Create Storage Context
    CreateStorageContext();

    // Use a file as the input.
    // IAsset asset = CreateAssetfromFile();
    
    // -- OR ---
    
    // Or a blob as the input
    // IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

    // Then submit the asset to Content Moderator
    // RunContentModeratorJob(asset);

    //-- OR ----

    // Just run the content moderator on all blobs in a list (from a Blob Container)
    RunContentModeratorJobOnBlobs();

### <a name="add-the-code-to-create-an-azure-media-context"></a>Azure 미디어 컨텍스트를 만드는 코드 추가

    /// <summary>
    /// Creates a media context from azure credentials
    /// </summary>
    static void CreateMediaContext()
    {
        // Get Azure AD credentials
        var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
            new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
            AzureEnvironments.AzureCloudEnvironment);

        // Initialize an Azure AD token
        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        // Create a media context
        _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
    }

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Azure Storage 컨텍스트를 만드는 코드 추가
저장소 자격 증명에서 만든 저장소 컨텍스트를 사용하여 Blob 저장소에 액세스합니다.

    /// <summary>
    /// Creates a storage context from the AMS associated storage name and key
    /// </summary>
    static void CreateStorageContext()
    {
        // Get a reference to the storage account associated with a Media Services account. 
        if (_StorageCredentials == null)
        {
            _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
        }
        _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
    }

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>로컬 파일 및 Blob에서 Azure 미디어 자산을 만드는 코드 추가
Content Moderator 미디어 프로세서는 Azure Media Services 플랫폼 내의 **자산**에 대해 작업을 실행합니다.
이러한 메서드는 로컬 파일 또는 연결된 Blob에서 자산을 만듭니다.

    /// <summary>
    /// Creates an Azure Media Services Asset from the video file
    /// </summary>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromFile()
    {
        return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
    }

    /// <summary>
    /// Creates an Azure Media Services asset from your blog storage
    /// </summary>
    /// <param name="Blob"></param>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
    {
        // Create asset from the FIRST blob in the list and return it
        return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
    }

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>컨테이너 내에서 비디오 컬렉션(blob으로)을 검색하는 코드 추가

    /// <summary>
    /// Runs the Content Moderator Job on all Blobs in a given container name
    /// </summary>
    static void RunContentModeratorJobOnBlobs()
    {
        // Get the reference to the list of Blobs. See the following method.
        var blobList = GetBlobsList();

        // Iterate over the Blob list items or work on specific ones as needed
        foreach (var sourceBlob in blobList)
        {
            // Create an Asset
            IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                               _StorageCredentials, AssetCreationOptions.None);
            asset.Update();

            // Submit to Content Moderator
            RunContentModeratorJob(asset);
        }
    }

    /// <summary>
    /// Get all blobs in your container
    /// </summary>
    /// <returns></returns>
    static IEnumerable<IListBlobItem> GetBlobsList()
    {
        // Get a reference to the Container within the Storage Account
        // that has the files (blobs) for moderation
        CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
        CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

        // Get the reference to the list of Blobs 
        var blobList = MediaBlobContainer.ListBlobs();
        return blobList;
    }

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Content Moderator 작업을 실행하기 위한 메서드 추가

    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
                    Thread.Sleep(10000);
             }
             while (jobQuery.State != JobState.Finished &&
             jobQuery.State != JobState.Error &&
             jobQuery.State != JobState.Canceled);
        });
        progressPrintTask.Start();

        Task progressJobTask = job.GetExecutionProgressTask(
        CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            ErrorDetail error = job.Tasks.First().ErrorDetails.First();
            Console.WriteLine(string.Format("Error: {0}. {1}",
            error.Code,
            error.Message));
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

### <a name="add-a-couple-of-helper-functions"></a>몇 가지 도우미 함수 추가

이러한 메서드는 Azure Media Services 자산에서 Content Moderator 출력 파일(JSON)을 다운로드하고 프로그램이 콘솔에 실행 중 상태를 로깅할 수 있도록 조정 작업의 상태를 추적하는 데 도움을 줍니다.

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    // event handler for Job State
    static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);
        switch (e.CurrentState)
        {
            case JobState.Finished:
                Console.WriteLine();
                Console.WriteLine("Job finished.");
                break;
            case JobState.Canceling:
            case JobState.Queued:
            case JobState.Scheduled:
            case JobState.Processing:
                Console.WriteLine("Please wait...\n");
                break;
            case JobState.Canceled:
                Console.WriteLine("Job is canceled.\n");
                break;
            case JobState.Error:
                Console.WriteLine("Job failed.\n");
                break;
            default:
                break;
        }
    }

### <a name="run-the-program-and-review-the-output"></a>프로그램 실행 및 출력 검토

Content Moderation 작업이 완료되면 JSON 응답을 분석합니다. 응답은 다음 요소로 구성됩니다.

- 비디오 정보 요약
- **Shots**를 "**fragments**"로 지정
- **Key frames**를 **Adult** 및 **Racy** 점수를 기준으로 하는 **reviewRecommended" (= true or false)"** 플래그를 사용하여 "**events**"로 지정
- **start**, **duration**, **totalDuration** 및 **timestamp**는 "ticks"로 지정합니다. **timescale**로 나누어 초 수를 가져옵니다.
 
> [!NOTE]

> - `adultScore`는 잠재적으로 특정 상황에서 성적으로 노골적이거나 성인용으로 간주될 수 있는 콘텐츠의 존재 가능성 및 예측 점수를 나타냅니다.
> - `racyScore`는 잠재적으로 특정 상황에서 성적으로 외설적이거나 도발적인 것으로 간주될 수 있는 콘텐츠의 존재 가능성 및 예측 점수를 나타냅니다.
> - `adultScore` 및 `racyScore`의 범위는 0부터 1 사이입니다. 점수가 높을수록 모델이 예측하는 범주에 해당할 가능성이 높아집니다. 이 미리 보기는 수동으로 코딩된 결과가 아닌 통계 모델을 사용합니다. 고유한 콘텐츠로 테스트하여 각 범주가 요구 사항과 얼마나 일치하는지 확인하는 것이 좋습니다.
> - `reviewRecommended`는 내부 점수 임계값에 따라 true 또는 false입니다. 고객은 이 값을 사용할지 또는 콘텐츠 정책에 따라 사용자 지정 임계값을 결정할지를 평가해야 합니다.
>

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }

## <a name="next-steps"></a>다음 단계

조정 출력에서 [비디오 검토](video-reviews-quickstart-dotnet.md)를 생성하는 방법을 알아봅니다.

비디오 검토에 [기록 조정](video-transcript-moderation-review-tutorial-dotnet.md)을 추가합니다.

[완전한 비디오 및 기록 조정 솔루션](video-transcript-moderation-review-tutorial-dotnet.md)을 빌드하는 방법에 대한 자세한 자습서를 살펴봅니다.

이 빠른 시작과 기타 .NET용 Content Moderator 빠른 시작을 위한 [Visual Studio 솔루션을 다운로드](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)합니다.
