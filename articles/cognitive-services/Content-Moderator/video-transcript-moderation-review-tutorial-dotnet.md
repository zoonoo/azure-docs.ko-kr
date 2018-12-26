---
title: '자습서: .NET에서 비디오 및 대본 조정 - Content Moderator'
titlesuffix: Azure Cognitive Services
description: .NET에서 비디오 및 대본을 조정하기 위해 Content Moderator를 사용하는 방법입니다.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 1/27/2018
ms.author: sajagtap
ms.openlocfilehash: d156c481e3c16105ad85cbc793d93306a310d5ef
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567114"
---
# <a name="tutorial-video-and-transcript-moderation"></a>자습서: 비디오 및 대본 조정

Content Moderator의 비디오 API를 사용하면 사용자 검토 도구에서 비디오를 조정하고 비디오 검토를 만들 수 있습니다. 

이 자세한 자습서는 컴퓨터 지원 조정 및 사람이 개입된(human-in-the-loop) 검토 생성을 통해 완벽한 비디오 및 대본 조정 솔루션을 빌드하는 방법을 이해하는 데 도움이 됩니다.

이 자습서에 대해서는 [C# 콘솔 응용 프로그램](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp)을 다운로드합니다. 콘솔 응용 프로그램은 SDK 및 관련 패키지를 사용하여 다음 작업을 수행합니다.

- 신속한 처리를 위해 입력 비디오 압축
- 비디오를 조정하여 인사이트가 있는 촬영 및 프레임 가져오기
- 프레임 타임스탬프를 사용하여 썸네일(이미지) 만들기
- 타임스탬프 및 썸네일을 제출하여 비디오 검토 만들기
- Media Indexer API를 사용하여 비디오 음성을 텍스트(대본)로 변환
- 텍스트 조정 서비스를 사용하여 대본 조정
- 비디오 검토에 조정된 대본 추가

## <a name="sample-program-outputs"></a>샘플 프로그램 출력

계속하기 전에 프로그램에서 다음 샘플 출력을 살펴보겠습니다.

- [콘솔 출력](#program-output)
- [비디오 검토](#video-review-default-view)
- [대본 보기](#video-review-transcript-view)

## <a name="prerequisites"></a>필수 조건

1. [Content Moderator 검토 도구](https://contentmoderator.cognitive.microsoft.com/) 웹 사이트에 등록하고 C# 콘솔 응용 프로그램이 코드 내에서 할당하는 [사용자 지정 태그를 만듭니다](Review-Tool-User-Guide/tags.md). 다음 스크린샷에서는 사용자 지정 태그를 보여 줍니다.

  ![비디오 조정 사용자 지정 태그](images/video-tutorial-custom-tags.png)

1. 샘플 응용 프로그램을 실행하려면 Azure 계정 및 Azure Media Services 계정이 필요합니다. 또한 Content Moderator 비공개 미리 보기에 액세스해야 합니다. 마지막으로 Azure Active Directory 인증 자격 증명이 필요합니다. 이 정보를 얻는 방법에 대한 자세한 내용은 [비디오 조정 API 빠른 시작](video-moderation-api.md)을 참조합니다.

1. 파일 `App.config`를 편집하고 Active Directory 테넌트 이름, 서비스 엔드포인트 및 `#####`에서 표시한 구독 키를 추가합니다. 다음 정보가 필요합니다.

|키|설명|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|AMS(Azure Media Services) API에 대한 엔드포인트|
|`ClientSecret`|Azure Media Services에 대한 구독 키|
|`ClientId`|Azure Media Services에 대한 클라이언트 ID|
|`AzureAdTenantName`|소속 조직을 나타내는 Active Directory 테넌트 이름|
|`ContentModeratorReviewApiSubscriptionKey`|Content Moderator 검토 API에 대한 구독 키|
|`ContentModeratorApiEndpoint`|Content Moderator API에 대한 엔드포인트|
|`ContentModeratorTeamId`|Content Moderator 팀 ID|

## <a name="getting-started"></a>시작

`Program.cs`의 클래스 `Program`은 비디오 조정 응용 프로그램에 대한 기본 진입점입니다.

### <a name="methods-of-class-program"></a>클래스 메서드 프로그램

|방법|설명|
|-|-|
|`Main`|명령줄을 구문 분석하고 사용자 입력을 수집하고 처리를 시작합니다.|
|`ProcessVideo`|비디오 검토를 압축하고 업로드하고 조정하고 생성합니다.|
|`CreateVideoStreamingRequest`|스트림을 만들어 비디오 업로드|
|`GetUserInputs`|명령줄 옵션이 없을 때 사용된 사용자 입력 수집|
|`Initialize`|조정 프로세스에 필요한 개체 초기화|

### <a name="the-main-method"></a>기본 메서드

`Main()`은 실행이 시작되는 위치이므로 비디오 중재 프로세스를 이해하기 시작하는 장소입니다.

    static void Main(string[] args)
    {
        if (args.Length == 0)
        {
            string videoPath = string.Empty;
            GetUserInputs(out videoPath);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(Path.GetDirectoryName(videoPath), "log.txt");
            try
            {
                ProcessVideo(videoPath).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
        else
        {
            DirectoryInfo directoryInfo = new DirectoryInfo(args[0]);
            if (args.Length == 2)
                bool.TryParse(args[1], out generateVtt);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(args[0], "log.txt");
            var files = directoryInfo.GetFiles("*.mp4", SearchOption.AllDirectories);
            foreach (var file in files)
            {
                try
                {
                    ProcessVideo(file.FullName).Wait();
                }
                catch (Exception ex)
                {
                    Console.WriteLine(ex.Message);
                }
            }
        }
    }

`Main()`은 다음 명령줄 인수를 처리합니다.

- 조정을 위해 제출될 MPEG-4 비디오 파일을 포함하는 디렉터리 경로입니다. 이 디렉터리 및 하위 디렉터리의 모든 `*.mp4` 파일이 조정을 위해 제출됩니다.
- 필요에 따라 텍스트 대본이 오디오 조정을 위해 생성되어야 하는지 여부를 나타내는 부울(true/false) 플래그입니다.

명령줄 인수가 없는 경우 `Main()`은 `GetUserInputs()`을 호출합니다. 이 메서드는 사용자가 단일 비디오 파일의 경로를 입력하고 텍스트 대본을 생성해야 할지 여부를 지정하게 합니다.

> [!NOTE]
> 콘솔 응용 프로그램은 [Azure Media Indexer API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2)를 사용하여 업로드된 비디오의 오디오 트랙에서 대본을 생성합니다. 결과는 WebVTT 형식으로 제공됩니다. 이 형식에 대한 자세한 내용은 [Web Video Text Tracks 형식](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API)을 참조하세요.

### <a name="initialize-and-processvideo-methods"></a>초기화 및 ProcessVideo 메서드

대화형 사용자 입력 또는 명령줄에서 프로그램 옵션이 제공되는지 여부에 관계 없이 `Main()`은 `Initialize()`를 호출하여 다음 인스턴스를 만듭니다.

|클래스|설명|
|-|-|
|`AMSComponent`|조정을 위해 제출하기 전에 비디오 파일을 압축합니다.|
|`AMSconfigurations`|`App.config`에 있는 응용 프로그램의 구성 데이터에 대한 인터페이스입니다.|
|`VideoModerator`| AMS SDK를 사용하여 업로드, 인코딩, 암호화 및 조정|
|`VideoReviewApi`|Content Moderator 서비스에서 비디오 검토 관리|

이러한 클래스(간단한 `AMSConfigurations` 제외)에 대해서는 이 자습서의 이후 섹션에서 자세히 설명합니다.

마지막으로 비디오 파일은 각각에 대해 `ProcessVideo()`를 호출하여 한 번에 하나씩 처리됩니다.

    private static async Task ProcessVideo(string videoPath)
    {
        Stopwatch sw = new Stopwatch();
        sw.Start();
        Console.ForegroundColor = ConsoleColor.White;
        Console.WriteLine("\nVideo compression process started...");

        var compressedVideoPath = amsComponent.CompressVideo(videoPath);
        if (string.IsNullOrWhiteSpace(compressedVideoPath))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Compression failed.");
        }

        Console.WriteLine("\nVideo compression process completed...");

        UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
        UploadAssetResult uploadResult = new UploadAssetResult();

        if (generateVtt)
        {
            uploadResult.GenerateVTT = generateVtt;
        }
        Console.WriteLine("\nVideo moderation process started...");

        if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Review process failed.");
        }

        Console.WriteLine("\nVideo moderation process completed...");
        Console.WriteLine("\nVideo review process started...");
        string reviewId = await videoReviewApi.CreateVideoReviewInContentModerator(uploadResult);
        Console.WriteLine("\nVideo review successfully completed...");
        sw.Stop();
        Console.WriteLine("\nTotal Elapsed Time: {0}", sw.Elapsed);
        using (var stw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            stw.WriteLine("Video File Name: " + Path.GetFileName(videoPath));
            stw.WriteLine($"ReviewId: {reviewId}");
            stw.WriteLine("Total Elapsed Time: {0}", sw.Elapsed);
        }
    }


`ProcessVideo()` 메서드는 상당히 간단합니다. 순서대로 다음 작업을 수행합니다.

- 비디오 압축
- Azure Media Services 자산에 비디오 업로드
- AMS 작업을 만들어 비디오 조정
- Content Moderator에서 비디오 검토 만들기

다음 섹션에서는 `ProcessVideo()`에서 호출한 일부 개별 프로세스를 좀 더 자세히 살펴봅니다. 

## <a name="compressing-the-video"></a>비디오 압축

네트워크 트래픽을 최소화하려면 응용 프로그램은 비디오 파일을 H.264(MPEG-4 AVC) 형식으로 변환하고 최대 너비 640픽셀로 크기를 조정합니다. 높은 효율성(압축률) 때문에 H.264 코덱이 권장됩니다. 압축은 Visual Studio 솔루션의 `Lib` 폴더에 포함된 무료 `ffmpeg` 명령줄 도구를 사용하여 이루어집니다. 입력 파일은 가장 일반적으로 사용되는 비디오 파일 형식 및 코덱을 포함하여 `ffmpeg`에서 지원하는 모든 형식이 될 수 있습니다.

> [!NOTE]
> 명령줄 옵션을 사용하여 프로그램을 시작할 경우 조정을 위해 제출될 비디오 파일을 포함하는 디렉터리를 지정합니다. `.mp4` 파일 이름 확장명를 가진 이 디렉터리의 모든 파일이 처리됩니다. 다른 파일 이름 확장명을 처리하려면 `Program.cs`의 `Main()` 메서드를 업데이트하여 원하는 확장명을 포함시킵니다.

단일 비디오 파일을 압축하는 코드는 `AMSComponent.cs`의 `AmsComponent` 클래스입니다. 이 기능을 담당하는 메서드는 여기에 표시된 `CompressVideo()`입니다.

    public string CompressVideo(string videoPath)
    {
        string ffmpegBlobUrl;
        if (!ValidatePreRequisites())
        {
            Console.WriteLine("Configurations check failed. Please cross check the configurations!");
            throw new Exception();
        }

        if (File.Exists(_configObj.FfmpegExecutablePath))
        {
            ffmpegBlobUrl = this._configObj.FfmpegExecutablePath;
        }
        else
        {
            Console.WriteLine("ffmpeg.exe is missing. Please check the Lib folder");
            throw new Exception();
        }

        string videoFilePathCom = videoPath.Split('.')[0] + "_c.mp4";
        ProcessStartInfo processStartInfo = new ProcessStartInfo();
        processStartInfo.WindowStyle = ProcessWindowStyle.Hidden;
        processStartInfo.FileName = ffmpegBlobUrl;
        processStartInfo.Arguments = "-i \"" + videoPath + "\" -vcodec libx264 -n -crf 32 -preset veryfast -vf scale=640:-1 -c:a aac -aq 1 -ac 2 -threads 0 \"" + videoFilePathCom + "\"";
        var process = Process.Start(processStartInfo);
        process.WaitForExit();
        process.Close();
        return videoFilePathCom;
    }

코드는 다음 단계를 수행합니다.

- `App.config`의 구성이 필요한 모든 데이터를 포함하는지 확인
- `ffmpeg` 이진이 존재하는지 확인
- 파일의 기본 이름(예: `Example.mp4` -> `E>xample_c.mp4`)에 `_c.mp4`를 추가하여 출력 파일 이름 빌드
- 명령줄 문자열을 빌드하여 변환 수행
- 명령줄을 사용하는 `ffmpeg` 프로세스 시작
- 처리될 비디오 대기

> [!NOTE]
> 이미 비디오가 H.264를 사용하여 압축되고 적절한 크기를 갖고 있음을 아는 경우 압축하지 않으려면 `CompressVideo()`를 다시 생성합니다.

메서드는 압축된 출력 파일의 파일 이름을 반환합니다.

## <a name="uploading-and-moderating-the-video"></a>비디오 업로드 및 조정

비디오는 Content Moderation 서비스에서 처리하기 전에 Azure Media Services에 저장되어야 합니다. `Program.cs`의 `Program` 클래스에는 비디오를 업로드하는 데 사용된 스트리밍 요청을 나타내는 개체를 반환하는 짧은 메서드 `CreateVideoStreamingRequest()`가 있습니다.

    private static UploadVideoStreamRequest CreateVideoStreamingRequest(string compressedVideoFilePath)
    {
        return
            new UploadVideoStreamRequest
            {
                VideoStream = File.ReadAllBytes(compressedVideoFilePath),
                VideoName = Path.GetFileName(compressedVideoFilePath),
                EncodingRequest = new EncodingRequest()
                {
                    EncodingBitrate = AmsEncoding.AdaptiveStreaming
                },
                VideoFilePath = compressedVideoFilePath
            };
    }

결과 `UploadVideoStreamRequest` 개체는 `UploadVideoStreamRequest.cs`(및 부모인 `UploadVideoRequest.cs`의 `UploadVideoRequest`)에서 정의됩니다. 이러한 클래스는 여기에 표시되지 않고, 짧으며, 압축된 비디오 데이터 및 이에 대한 정보를 보유하기 위해서만 사용합니다. 다른 데이터 전용 클래스 `UploadAssetResult`(`UploadAssetResult.cs`)는 업로드 프로세스의 결과를 보유하는 데 사용됩니다. 이제 `ProcessVideo()`에서 이러한 줄을 이해할 수 있습니다.

    UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
    UploadAssetResult uploadResult = new UploadAssetResult();

    if (generateVtt)
    {
        uploadResult.GenerateVTT = generateVtt;
    }
    Console.WriteLine("\nVideo moderation process started...");

    if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
    {
        Console.ForegroundColor = ConsoleColor.Red;
        Console.WriteLine("Video Review process failed.");
    }

이러한 줄은 다음 작업을 수행합니다.

- `UploadVideoStreamRequest`를 만들어 압축된 비디오 업로드
- 사용자가 텍스트 대본을 요청한 경우 요청의 `GenerateVTT` 플래그 설정
- 업로드를 수행하고 결과를 받으려면 `CreateAzureMediaServicesJobToModerateVideo()` 호출

## <a name="deep-dive-into-video-moderation"></a>비디오 조정에 대한 심층 분석

메서드 `CreateAzureMediaServicesJobToModerateVideo()`는 Azure Media Services와 상호 작용하는 대량의 코드를 포함하는 `VideoModerator.cs`에 있습니다. 메서드의 소스 코드는 다음 추출에 표시됩니다.

    public bool CreateAzureMediaServicesJobToModerateVideo(UploadVideoStreamRequest uploadVideoRequest, UploadAssetResult uploadResult)
    {
        asset = CreateAsset(uploadVideoRequest);
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        // Encoding the asset , Moderating the asset, Generating transcript in parallel
        IAsset encodedAsset = null;
        //Creates the job for the tasks.
        IJob job = this._mediaContext.Jobs.Create("AMS Review Job");

        //Adding encoding task to job.
        ConfigureEncodeAssetTask(uploadVideoRequest.EncodingRequest, job);

        ConfigureContentModerationTask(job);

        //adding transcript task to job.
        if (uploadResult.GenerateVTT)
        {
            ConfigureTranscriptTask(job);
        }

        Stopwatch timer = new Stopwatch();
        timer.Start();
        //submit and execute job.
        job.Submit();
        job.GetExecutionProgressTask(new CancellationTokenSource().Token).Wait();
        timer.Stop();
        using (var sw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            sw.WriteLine("AMS Job Elapsed Time: {0}", timer.Elapsed);
        }

        if (job.State == JobState.Error)
        {
            throw new Exception("Video moderation has failed due to AMS Job error.");
        }

        UploadAssetResult result = uploadResult;
        encodedAsset = job.OutputMediaAssets[0];
        result.ModeratedJson = GetCmDetail(job.OutputMediaAssets[1]);
        // Check for valid Moderated JSON
        var jsonModerateObject = JsonConvert.DeserializeObject<VideoModerationResult>(result.ModeratedJson);

        if (jsonModerateObject == null)
        {
            return false;
        }
        if (uploadResult.GenerateVTT)
        {
            GenerateTranscript(job.OutputMediaAssets.Last());
        }

        uploadResult.StreamingUrlDetails = PublishAsset(encodedAsset);
        string downloadUrl = GenerateDownloadUrl(asset, uploadVideoRequest.VideoName);
        uploadResult.StreamingUrlDetails.DownloadUri = downloadUrl;
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        uploadResult.VideoFilePath = uploadVideoRequest.VideoFilePath;
        return true;
    }

이 코드는 다음 작업을 수행합니다.

- 수행될 처리에 대한 AMS 작업 만들기
- 비디오 파일 인코딩, 조정 및 텍스트 대본 생성에 대한 작업 추가
- 작업 제출, 파일 업로드 및 처리 시작
- 조정 결과, 텍스트 대본(요청할 경우) 및 기타 정보 검색

## <a name="sample-video-moderation-response"></a>샘플 비디오 조정 응답

비디오 조정 작업의 결과([비디오 조정 빠른 시작](video-moderation-api.md) 참조)는 조정 결과를 포함하는 JSON 데이터 구조입니다. 이러한 결과는 비디오 내의 조각(샷)에 대한 분석 결과를 포함하며 각 비디오는 검토를 위해 플래그가 지정된 키 프레임이 있는 이벤트(클립)을 포함합니다. 각 키 프레임은 성인 또는 선정적인 콘텐츠를 포함할 가능성에 따라 점수가 매겨집니다. 다음 예제에서는 JSON 응답을 보여 줍니다.

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

비디오에서 오디오의 전사는 `GenerateVTT` 플래그가 설정되는 경우 생성됩니다.

> [!NOTE]
> 콘솔 응용 프로그램은 [Azure Media Indexer API](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2)를 사용하여 업로드된 비디오의 오디오 트랙에서 대본을 생성합니다. 결과는 WebVTT 형식으로 제공됩니다. 이 형식에 대한 자세한 내용은 [Web Video Text Tracks 형식](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API)을 참조하세요.


## <a name="creating-the-human-in-the-loop-review"></a>사람이 개입된(human-in-the-loop) 검토 만들기

조정 프로세스는 비디오에서 오디오 트랙의 대본과 함께 키 프레임 목록을 반환합니다. 다음 단계는 사람 조정자를 위해 Content Moderator 검토 도구에서 검토를 만드는 것입니다. `Program.cs`의 `ProcessVideo()` 메서드로 돌아가 `CreateVideoReviewInContentModerator()` 메서드에 대한 호출을 봅니다. 이 메서드는 `VideoReviewAPI.cs`에 있는 `videoReviewApi` 클래스에 있으며, 여기에 표시됩니다.

    public async Task<string> CreateVideoReviewInContentModerator(UploadAssetResult uploadAssetResult)
    {
    
        string reviewId = string.Empty;
        List<ProcessedFrameDetails> frameEntityList = framegenerator.CreateVideoFrames(uploadAssetResult);
        string path = uploadAssetResult.GenerateVTT == true ? this._amsConfig.FfmpegFramesOutputPath + Path.GetFileNameWithoutExtension (uploadAssetResult.VideoName) + "_aud_SpReco.vtt" : "";
        TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult();
        
    if (File.Exists(path))
        {
            screenTextResult = await GenerateTextScreenProfanity(reviewId, path, frameEntityList);
            uploadAssetResult.Category1TextScore = screenTextResult.Category1Score;
            uploadAssetResult.Category2TextScore = screenTextResult.Category2Score;
            uploadAssetResult.Category3TextScore = screenTextResult.Category3Score;
            uploadAssetResult.Category1TextTag = screenTextResult.Category1Tag;
            uploadAssetResult.Category2TextTag = screenTextResult.Category2Tag;
            uploadAssetResult.Category3TextTag = screenTextResult.Category3Tag;
        }
        
        var reviewVideoRequestJson = CreateReviewRequestObject(uploadAssetResult, frameEntityList);
        if (string.IsNullOrWhiteSpace(reviewVideoRequestJson))
        {
            throw new Exception("Video review process failed in CreateVideoReviewInContentModerator");
        }
        
        reviewId = JsonConvert.DeserializeObject<List<string>>(ExecuteCreateReviewApi(reviewVideoRequestJson).Result).FirstOrDefault();
        frameEntityList = framegenerator.GenerateFrameImages(frameEntityList, uploadAssetResult, reviewId);
        await CreateAndPublishReviewInContentModerator(uploadAssetResult, frameEntityList, reviewId, path, screenTextResult);
        return reviewId;
    
    }

`CreateVideoReviewInContentModerator()`는 다음 작업을 수행하려면 다른 여러 메서드를 호출합니다.

> [!NOTE]
> 콘솔 응용 프로그램은 썸네일을 생성하기 위한 [FFmpeg](https://ffmpeg.org/) 라이브러리를 사용합니다. 이러한 썸네일(이미지)은 [비디오 조정 출력](#sample-video-moderation-response)에서 프레임 타임스탬프에 해당합니다.

|Task|메서드|파일|
|-|-|-|
|비디오에서 키 프레임 추출 및 키 프레임의 썸네일 이미지 만들기|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|성인 또는 선정적인 오디오를 찾으려면 사용 가능한 경우 텍스트 대본 검사|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|휴먼 검사를 위한 비디오 검토 요청 준비 및 제출|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>비디오 검토 기본 보기

다음 화면에서는 이전 단계의 결과를 보여줍니다.

![비디오 검토 기본 보기](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>대본 생성

지금까지 이 자습서에 제공된 코드는 시각적 콘텐츠에 중점을 뒀습니다. 음성 콘텐츠 검토는 언급했듯이 오디오에서 생성된 대본을 사용하는 별도의 선택적 프로세스입니다. 이제 텍스트 대본이 검토 프로세스에서 생성되고 사용되는 방법에 대해 살펴볼 때가 됐습니다. 대본 생성 작업은 [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content) 서비스의 몫입니다.

이 응용 프로그램은 다음 작업을 수행합니다.

|Task|메서드|파일|
|-|-|-|
|텍스트 대본이 생성되는지 여부를 결정|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|생성된다면 조정의 일부로 전사 작업 제출|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|대화의 로컬 복사본 가져오기|`GenerateTranscript()`|`VideoModerator.cs`|
|부적절한 오디오를 포함하는 비디오의 플래그 프레임|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|검토에 결과 추가|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>작업 구성

전사 작업 제출로 바로 들어가겠습니다. `CreateAzureMediaServicesJobToModerateVideo()`(이미 설명된)는 `ConfigureTranscriptTask()`를 호출합니다.

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

솔루션의 `Lib` 폴더의 파일 `MediaIndexerConfig.json`에서 대본 작업에 대한 구성을 읽습니다. 전사 프로세스의 출력 및 구성 파일에 대해 AMS 자산을 생성합니다. AMS 작업이 실행될 때 이 작업은 비디오 파일의 오디오 트랙에서 텍스트 대본을 만듭니다.

> [!NOTE]
> 샘플 응용 프로그램은 미국 영어로 된 음성만 인식합니다.

### <a name="transcript-generation"></a>대본 생성

대본은 AMS 자산으로 게시됩니다. 대본에서 불쾌한 콘텐츠를 검사하려면 응용 프로그램은 Azure Media Services에서 자산을 다운로드합니다. `CreateAzureMediaServicesJobToModerateVideo()`는 여기에 표시된 `GenerateTranscript()`를 호출하여 파일을 검색합니다.

    public bool GenerateTranscript(IAsset asset)
    {
        try
        {
            var outputFolder = this._amsConfigurations.FfmpegFramesOutputPath;
            IAsset outputAsset = asset;
            IAccessPolicy policy = null;
            ILocator locator = null;
            policy = _mediaContext.AccessPolicies.Create("My 30 days readonly policy", TimeSpan.FromDays(360), AccessPermissions.Read);
            locator = _mediaContext.Locators.CreateLocator(LocatorType.Sas, outputAsset, policy, DateTime.UtcNow.AddMinutes(-5));
            DownloadAssetToLocal(outputAsset, outputFolder);
            locator.Delete();
            return true;
        }
        catch
        {   //TODO:  Logging
            Console.WriteLine("Exception occured while generating index for video.");
            throw;
        }
    }

필요한 일부 AMS 설치 후 실제 다운로드는 AMS 자산을 로컬 파일로 복사하는 제네릭 함수인 `DownloadAssetToLocal()`을 호출하여 수행됩니다.

## <a name="transcript-moderation"></a>대본 조정

대본을 가까이에 두면 이를 검토에서 검사하고 사용합니다. 검토 만들기는 작업을 수행하려면 `GenerateTextScreenProfanity()`를 호출하는 `CreateVideoReviewInContentModerator()`의 권한입니다. 결국 이 메서드는 대부분의 기능을 포함하는 `TextScreen()`을 호출합니다. 

`TextScreen()`은 다음 작업을 수행합니다.

- 타임스탬프 및 캡션에 대한 대본 구문 분석
- 텍스트 조정에 대해 각 캡션 제출
- 불쾌한 음성 콘텐츠가 있을 수 있는 모든 프레임에 플래그 지정

이러한 작업 각각에 대해 보다 자세히 살펴보겠습니다.

### <a name="initialize-the-code"></a>코드 초기화

먼저, 모든 변수 및 컬렉션을 초기화합니다.

    private async Task<TranscriptScreenTextResult> TextScreen(string filepath, List<ProcessedFrameDetails> frameEntityList)
    {
        List<TranscriptProfanity> profanityList = new List<TranscriptProfanity>();
        string responseContent = string.Empty;
        HttpResponseMessage response;
        bool category1Tag = false;
        bool category2Tag = false;
        bool category3Tag = false;
        double category1Score = 0;
        double category2Score = 0;
        double category3Score = 0;
        List<string> vttLines = File.ReadAllLines(filepath).Where(line => !line.Contains("NOTE Confidence:") && line.Length > 0).ToList();
        StringBuilder sb = new StringBuilder();
        List<CaptionScreentextResult> csrList = new List<CaptionScreentextResult>();
        CaptionScreentextResult captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };

        // Code from the next sections in the tutorial
    

### <a name="parse-the-transcript-for-captions"></a>캡션에 대한 대본 구문 분석

다음으로, 캡션 및 타임스탬프에 대해 VTT 형식의 대본을 구문 분석합니다. 검토 도구는 비디오 검토 화면의 대본 탭에 이러한 캡션을 표시합니다. 타임스탬프는 해당 비디오 프레임과 캡션을 동기화하는 데 사용됩니다.

        // Code from the previous section(s) in the tutorial

        //
        // Parse the transcript
        //
        foreach (var line in vttLines.Skip(1))
        {
                if (line.Contains("-->"))
                {
                    if (sb.Length > 0)
                    {
                        captionScreentextResult.Captions.Add(sb.ToString());
                        sb.Clear();
                    }
                    if (captionScreentextResult.Captions.Count > 0)
                    {
                        csrList.Add(captionScreentextResult);
                        captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };
                    }
                    string[] times = line.Split(new string[] { "-->" }, StringSplitOptions.RemoveEmptyEntries);
                    string startTimeString = times[0].Trim();
                    string endTimeString = times[1].Trim();
                    int startTime = (int)TimeSpan.ParseExact(startTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    int endTime = (int)TimeSpan.ParseExact(endTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    captionScreentextResult.StartTime = startTime;
                    captionScreentextResult.EndTime = endTime;
                }
                else
                {
                    sb.Append(line);
                }
                if (sb.Length + line.Length > 1024)
                {
                    captionScreentextResult.Captions.Add(sb.ToString());
                    sb.Clear();
                }
            }
            if (sb.Length > 0)
            {
                captionScreentextResult.Captions.Add(sb.ToString());
            }
            if (captionScreentextResult.Captions.Count > 0)
            {
                csrList.Add(captionScreentextResult);
            }

            // Code from the following section in the quickstart

### <a name="moderate-captions-with-the-text-moderation-service"></a>텍스트 조정 서비스를 사용하여 캡션 조정

다음으로, Content Moderator의 텍스트 API를 사용하여 구문 분석된 텍스트 캡션을 검사합니다.

> [!NOTE]
> Content Moderator 서비스 키에는 RPS(초당 요청 수) 속도 제한이 있습니다. 제한을 초과하는 경우 SDK는 429 오류 코드로 예외를 throw합니다. 
>
> 체험판 계층 키에는 하나의 RPS 속도 제한이 있습니다.

    //
    // Moderate the captions or cues
    //
    int waitTime = 1000;
    foreach (var csr in csrList)
    {
                bool captionAdultTextTag = false;
                bool captionRacyTextTag = false;
                bool captionOffensiveTextTag = false;
                bool retry = true;

                foreach (var caption in csr.Captions)
                {
                    while (retry)
                    {
                        try
                        {
                            System.Threading.Thread.Sleep(waitTime);
                            var lang = await CMClient.TextModeration.DetectLanguageAsync("text/plain", caption);
                            var oRes = await CMClient.TextModeration.ScreenTextWithHttpMessagesAsync(lang.DetectedLanguageProperty, "text/plain", caption, null, null, null, true);
                            response = oRes.Response;
                            responseContent = await response.Content.ReadAsStringAsync();
                            retry = false;
                        }
                        catch (Exception e)
                        {
                            if (e.Message.Contains("429"))
                            {
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                                waitTime = (int)(waitTime * 1.5);
                                Console.WriteLine($"wait time: {waitTime}");
                            }
                            else
                            {
                                retry = false;
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                            }
                        }
                    }
                    var jsonTextScreen = JsonConvert.DeserializeObject<TextScreen>(responseContent);
                    if (jsonTextScreen != null)
                    {
                        TranscriptProfanity transcriptProfanity = new TranscriptProfanity();
                        transcriptProfanity.TimeStamp = "";
                        List<Terms> transcriptTerm = new List<Terms>();
                        if (jsonTextScreen.Terms != null)
                        {
                            foreach (var term in jsonTextScreen.Terms)
                            {
                                var profanityobject = new Terms
                                {
                                    Term = term.Term,
                                    Index = term.Index
                                };
                                transcriptTerm.Add(profanityobject);
                            }
                            transcriptProfanity.Terms = transcriptTerm;
                            profanityList.Add(transcriptProfanity);
                        }
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) captionAdultTextTag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) captionRacyTextTag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) captionOffensiveTextTag = true;
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) category1Tag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) category2Tag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) category3Tag = true;
                        category1Score = jsonTextScreen.Classification.Category1.Score > category1Score ? jsonTextScreen.Classification.Category1.Score : category1Score;
                        category2Score = jsonTextScreen.Classification.Category2.Score > category2Score ? jsonTextScreen.Classification.Category2.Score : category2Score;
                        category3Score = jsonTextScreen.Classification.Category3.Score > category3Score ? jsonTextScreen.Classification.Category3.Score : category3Score;
                    }
                    foreach (var frame in frameEntityList.Where(x => x.TimeStamp >= csr.StartTime && x.TimeStamp <= csr.EndTime))
                    {
                        frame.IsAdultTextContent = captionAdultTextTag;
                        frame.IsRacyTextContent = captionRacyTextTag;
                        frame.IsOffensiveTextContent = captionOffensiveTextTag;
                    }
                }
            }
            TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult()
            {
                TranscriptProfanity = profanityList,
                Category1Tag = category1Tag,
                Category2Tag = category2Tag,
                Category3Tag = category3Tag,
                Category1Score = category1Score,
                Category2Score = category2Score,
                Category3Score = category3Score
            };
            return screenTextResult;
    }

### <a name="breaking-down-the-text-moderation-step"></a>텍스트 조정 단계 분석

`TextScreen()`은 중요한 메서드이므로 이를 분석해보겠습니다.

1. 먼저, 메서드에서는 대본 파일을 한 줄씩 읽습니다. 신뢰도 점수에 따라 `NOTE`를 포함한 줄 및 빈 줄은 무시합니다. 파일의 *큐*에서 타임스탬프 및 텍스트 항목을 추출합니다. 큐는 오디오 트랙의 텍스트를 나타내고 시작 및 종료 시간을 포함합니다. 큐는 문자열 `-->`가 있는 타임스탬프 줄에서 시작하며, 텍스트의 하나 이상의 줄이 이어집니다.

1. `CaptionScreentextResult`(`TranscriptProfanity.cs`에 정의된)의 인스턴스는 각 큐에서 구문 분석된 정보를 보유하는 데 사용됩니다.  새 타임스탬프 줄이 검색되거나 최대 텍스트 길이 1024자에 도달하면 새 `CaptionScreentextResult`가 `csrList`에 추가됩니다. 

1. 다음으로, 메서드는 각 큐를 텍스트 조정 API에 제출하고, `Microsoft.Azure.CognitiveServices.ContentModerator` 어셈블리에서 정의되는 `ContentModeratorClient.TextModeration.DetectLanguageAsync()` 및 `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()` 둘 다 호출합니다. 속도가 제한되는 것을 방지하려면 메서드는 1초 동안 일시 중지한 후 각 큐를 제출합니다.

1. 텍스트 조정 서비스에서 결과를 받은 후 메서드는 그 결과를 분석하여 신뢰도 임계값을 충족하는지 여부를 확인합니다. 이러한 값은 `App.config`에 `OffensiveTextThreshold`, `RacyTextThreshold` 및 `AdultTextThreshold`로 설정됩니다. 마지막으로, 불쾌한 용어 자체도 저장됩니다. 큐의 시간 범위 내에 있는 모든 프레임은 불쾌하고 선정적 및/또는 성인 텍스트를 포함하는 것으로 플래그가 지정됩니다.

1. `TextScreen()`은 전체적으로 비디오에서 텍스트 조정 결과를 포함하는 `TranscriptScreenTextResult` 인스턴스를 반환합니다. 이 개체는 불쾌한 용어 목록과 함께 다양한 유형의 불쾌한 콘텐츠에 대한 플래그 및 점수를 포함합니다. 호출자 `CreateVideoReviewInContentModerator()`는 `UploadScreenTextResult()`를 호출하여 인간 검토자에게 사용할 수 있도록 이 정보를 검토에 연결합니다.
 
## <a name="video-review-transcript-view"></a>비디오 검토 대본 보기

다음 화면은 대본 생성 및 조정 단계의 결과를 보여줍니다.

![비디오 조정 대본 보기](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>프로그램 출력

프로그램에서 다음 명령줄 출력은 완료될 때 다양한 작업을 보여줍니다. 또한, 조정 결과(JSON 형식) 및 음성 대본은 원본 비디오 파일과 같은 디렉터리에서 사용할 수 있습니다.

    Microsoft.ContentModerator.AMSComponentClient
    Enter the fully qualified local path for Uploading the video :
    "Your File Name.MP4"
    Generate Video Transcript? [y/n] : y
    
    Video compression process started...
    Video compression process completed...
    
    Video moderation process started...
    Video moderation process completed...
    
    Video review process started...
    Video Frames Creation inprogress...
    Frames(83) created successfully.
    Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
    Video review successfully completed...
    
    Total Elapsed Time: 00:05:56.8420355


## <a name="next-steps"></a>다음 단계

이 자습서에 필요한 라이브러리, 샘플 파일 및 [Visual Studio 솔루션을 다운로드](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp)하고 통합에 대해 시작합니다.
