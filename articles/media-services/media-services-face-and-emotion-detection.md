---
title: "Azure 미디어 분석으로 얼굴 및 감정 탐지 | Microsoft 문서"
description: "이 토픽에는 Azure Media Analytics로 얼굴 및 감정을 감지하는 방법을 보여 줍니다."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/12/2017
ms.author: milanga;juliako;
translationtype: Human Translation
ms.sourcegitcommit: bd3dfe9472e944a127230d3dc0d564d344c4fbec
ms.openlocfilehash: 2ca5813c12e1358afaa20b4a919de4ee8a355865


---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Azure 미디어 분석으로 얼굴 및 감정 검색
## <a name="overview"></a>개요
**Azure 미디어 얼굴 탐지기** MP(미디어 프로세서)를 사용하여 이동 추적, 계산이 가능해지며 표정을 통해 대상 그룹 참여 및 반응 판단도 가능합니다. 이 서비스는 두 가지 기능을 포함합니다. 

* **얼굴 검색**
  
    얼굴 검색은 동영상 내의 얼굴을 찾아 추적합니다. 여러 얼굴이 검색될 수 있으며 이후 JSON 파일로 반환되는 시간 및 위치 메타데이터를 사용하여 얼굴이 움직일 때마다 추적할 수 있습니다. 추적하는 동안 화면에서 사용자가 움직일 때, 가려지거나 프레임에서 잠시 벗어나는 경우에도 동일한 얼굴에 일관된 ID를 지정하려고 합니다.
  
  > [!NOTE]
  > 이 서비스는 안면 인식을 수행하지 않습니다. 너무 오래 프레임에서 벗어나있거나 가려지는 경우에는 다시 돌아왔을 때 새 ID가 지정됩니다.
  > 
  > 
* **감정 검색**
  
    감정 검색은 검색된 얼굴로부터 행복, 슬픔, 두려움, 분노 등의 여러 감정적 특성에 대한 분석을 반환하는 얼굴 탐지 미디어 프로세서의 선택적 구성 요소입니다. 

**Azure 미디어 얼굴 탐지기** MP는 현재 미리 보기 상태입니다.

이 토픽은 **Azure Media Face Detector** 에 대한 세부 정보 및 .NET용 Media Services SDK와 함께 사용하는 방법을 보여 줍니다.

## <a name="face-detector-input-files"></a>얼굴 탐지기 입력 파일
동영상 파일입니다. 현재 MP4, MOV 및 WMV 형식이 지원됩니다.

## <a name="face-detector-output-files"></a>얼굴 탐지기 출력 파일
얼굴 검색 및 추적 API는 한 동영상 내에서 최대 64명의 얼굴을 검색할 수 있는 고정밀도 얼굴 위치 검색 및 추적을 제공합니다. 정면이 최상의 결과를 제공하며 측면 또는 작은 얼굴(24x24 픽셀보다 작거나 같음)의 경우 비교적 정확도가 낮을 수 있습니다.

검색 및 추적된 얼굴은 개별적인 추적을 나타내는 얼굴 ID 번호뿐만 아니라 이미지 내에서 얼굴의 위치를 픽셀 단위로 나타내는 좌표(왼쪽, 위쪽, 너비 및 높이)와 함께 반환됩니다. 얼굴 ID 번호는 프레임 안에 정면 얼굴이 없거나 겹쳐진 상황에서 재설정될 가능성이 크므로 결과적으로 일부 사용자에게 여러 ID가 할당될 수 있습니다.

### <a name="a-idoutputelementsaelements-of-the-output-json-file"></a><a id="output_elements"></a>출력 JSON 파일의 요소
얼굴 검색 및 추적 작업의 경우 출력 결과는 지정된 파일 내에서 얼굴에 대한 메타데이터를 JSON 형식으로 포함합니다.

얼굴 검색 및 추적 JSON에는 다음 특성이 포함됩니다.

| 요소 | 설명 |
| --- | --- |
| 버전 |동영상 API의 버전을 나타냅니다. |
| 시간 간격 |동영상의 초당 "틱"입니다. |
| Offset |타임스탬프의 시간 오프셋입니다. 동영상 API 버전 1.0에서는 항상 0입니다. 향후 지원하는 시나리오에서는 이 값이 변경될 수 있습니다. |
| 프레임 속도 |동영상의 초당 프레임 수입니다. |
| 조각 |메타데이터는 조각이라고 하는 다른 세그먼트로 청크 분할됩니다. 각 조각에는 시작, 기간, 간격 번호 및 이벤트가 포함됩니다. |
| 시작 |‘틱’ 단위의 첫 이벤트 시작 시간입니다. |
| 기간 |"틱" 단위의 조각 길이입니다. |
| 간격 |"틱" 단위의 조각 내 각 이벤트 항목 간격입니다. |
| 이벤트 |각 이벤트는 해당 기간 내에 검색 및 추적한 얼굴을 포함합니다. 이벤트 배열입니다. 외부 배열은 하나의 시간 간격을 나타냅니다. 내부 배열은 해당 특정 시점에 발생한 0개 이상의 이벤트로 구성됩니다. 빈 대괄호 []는 검색된 얼굴이 없음을 의미합니다. |
| ID |추적 중인 얼굴의 ID입니다. 이 번호는 얼굴이 검색되지 않는 경우 실수로 변경될 수 있습니다. 지정된 사용자는 동영상 전체에서 동일한 ID를 가져야 하지만 검색 알고리즘의 한계(폐색 등)로 인해 보장될 수 없습니다. |
| X, Y |0.0 ~ 1.0의 정규화된 배율 단위의 얼굴 경계 상자에 대한 왼쪽 상단 X, Y 좌표입니다. <br/>-X 및 Y 좌표는 항상 가로 방향에 비례하므로 세로 동영상(또는 iOS의 경우 거꾸로 된 동영상)을 가지고 있을 경우 좌표를 이에 따라 바꿔야 합니다. |
| 너비, 높이 |0.0 ~ 1.0의 정규화된 배율 단위의 얼굴 경계 상자에 대한 너비 및 높이입니다. |
| facesDetected |이는 JSON 결과의 끝에서 발견되며 알고리즘이 동영상에서 검색한 얼굴 수를 요약합니다. 얼굴이 검색되지 않는 경우(예: 얼굴이 화면 밖으로 나가거나 멀어짐) ID가 재설정될 수 있으므로 이 수는 동영상 내에 있는 실제 얼굴 수와 항상 같지 않을 수도 있습니다. |

얼굴 탐지기는 조각화 기술(메타데이터가 시간 기반 청크로 나뉠 수 있으며 필요한 것만 다운로드할 수 있음) 및 분할 기술(이벤트가 너무 커질 경우 분할)을 사용합니다. 몇 가지 간단한 계산으로 데이터를 변환할 수 있습니다. 예를 들어 이벤트가 6300(틱)에서 시작하고 날짜 표시줄이 2997(틱/초), 프레임 속도가 29.97(프레임/초)인 경우 다음과 같습니다.

* 시작/날짜 표시줄 = 2.1초
* 초 x 프레임 속도 = 63개 프레임

## <a name="face-detection-input-and-output-example"></a>얼굴 검색 입력 및 출력 예제
### <a name="input-video"></a>입력 동영상
[입력 동영상](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>작업 구성(기본 설정)
**Azure 미디어 얼굴 탐지기**로 작업을 만들 때에는 구성 기본 설정을 지정해야 합니다. 다음은 얼굴 검색에 대한 구성 기본 설정입니다.

    {
      "version":"1.0"
      "options":{
          "TrackingMode": "Faster"
      }
    }

#### <a name="attribute-descriptions"></a>특성 설명
| 특성 이름 | 설명 |
| --- | --- |
| Mode |빠르게: 처리 속도는 더 빠르지만 정확도가 떨어집니다(기본값). <br/>품질: 추적 정확도는 더 높지만 시간이 더 오래 걸립니다. |


### <a name="json-output"></a>JSON 출력
다음 JSON 출력 예는 잘린 상태입니다.

    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],

        . . . 

## <a name="emotion-detection-input-and-output-example"></a>감정 검색 입력 및 출력 예제
### <a name="input-video"></a>입력 동영상
[입력 동영상](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>작업 구성(기본 설정)
**Azure 미디어 얼굴 탐지기**로 작업을 만들 때에는 구성 기본 설정을 지정해야 합니다. 다음 구성 기본 설정은 감정 검색을 기반으로 JSON을 만들도록 지정합니다.

    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }


#### <a name="attribute-descriptions"></a>특성 설명
| 특성 이름 | 설명 |
| --- | --- |
| Mode |얼굴: 얼굴만 감지합니다.<br/>PerFaceEmotion: 각 얼굴 감지에 대해 독립적으로 감정을 반환합니다.<br/>AggregateEmotion: 프레임의 모든 얼굴에 대한 평균 감정 값을 반환합니다. |
| AggregateEmotionWindowMs |AggregateEmotion 모드가 선택된 경우에 사용합니다. 각 집계 결과를 생성하는 데 사용되는 동영상의 길이를 밀리초 단위로 지정합니다. |
| AggregateEmotionIntervalMs |AggregateEmotion 모드가 선택된 경우에 사용합니다. 집계 결과 생성 빈도를 지정합니다. |

#### <a name="aggregate-defaults"></a>집계 기본값
집계 창 및 간격 설정에는 아래 값이 권장됩니다. AggregateEmotionWindowMs는 AggregateEmotionIntervalMs보다 길어야 합니다.

| 기본값 | 최소값 | 최대값 |
| --- | --- | --- | --- |
| AggregateEmotionWindowMs |0.5 |2 |
| AggregateEmotionIntervalMs |0.5 |1 |

### <a name="json-output"></a>JSON 출력
감정 집계에 대한 JSON 출력(잘림)입니다.

    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,


## <a name="limitations"></a>제한 사항
* 지원되는 입력 동영상 형식에는 MP4, MOV 및 WMV가 있습니다.
* 검색 가능한 얼굴 크기 범위는 24x24 픽셀에서 2048x2048 픽셀입니다. 이 범위를 벗어난 얼굴은 검색되지 않습니다.
* 각 동영상에서 반환되는 최대 얼굴 수는 64입니다.
* 일부 얼굴은 기술적인 문제(예: 매우 큰 얼굴 각도(머리 포즈) 및 큰 폐색)로 인해 검색되지 않을 수 있습니다. 정면 및 정면에 가까운 얼굴이 최상의 결과를 생성합니다.

## <a name="sample-code"></a>샘플 코드
다음 프로그램은 방법을 보여 줍니다.

1. 자산을 만들고 미디어 파일을 자산에 업로드합니다.
2. 다음 json 기본 설정을 포함하는 구성 파일을 기반으로 얼굴 감지 작업을 만듭니다. 
   
        {
            "version": "1.0"
        }
3. 출력 JSON 파일을 다운로드합니다. 
   
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
   
        namespace FaceDetection
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
   
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
   
                static void Main(string[] args)
                {
   
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
   
                    // Run the FaceDetection job.
                    var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                                @"C:\supportFiles\FaceDetection\config.json");
   
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
                }
   
                static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Detection Input Asset",
                        AssetCreationOptions.None);
   
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Detection Job");
   
                    // Get a reference to Azure Media Face Detector.
                    string MediaProcessorName = "Azure Media Face Detector";
   
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
   
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
   
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Detection Task",
                        processor,
                        configuration,
                        TaskOptions.None);
   
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
   
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Detectoion Output Asset", AssetCreationOptions.None);
   
                    // Use the following event handler to check job progress.  
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
   
                    // Launch the job.
                    job.Submit();
   
                    // Check job execution and wait for job to finish.
                    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
   
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
                        return null;
                    }
   
                    return job.OutputMediaAssets[0];
                }
   
                static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
                {
                    IAsset asset = _context.Assets.Create(assetName, options);
   
                    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                    assetFile.Upload(filePath);
   
                    return asset;
                }
   
                static void DownloadAsset(IAsset asset, string outputDirectory)
                {
                    foreach (IAssetFile file in asset.AssetFiles)
                    {
                        file.Download(Path.Combine(outputDirectory, file.Name));
                    }
                }
   
                static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors
                        .Where(p => p.Name == mediaProcessorName)
                        .ToList()
                        .OrderBy(p => new Version(p.Version))
                        .LastOrDefault();
   
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor",
                                                                   mediaProcessorName));
   
                    return processor;
                }
   
                static private void StateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
   
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished.");
                            Console.WriteLine();
                            break;
                        case JobState.Canceling:
                        case JobState.Queued:
                        case JobState.Scheduled:
                        case JobState.Processing:
                            Console.WriteLine("Please wait...\n");
                            break;
                        case JobState.Canceled:
                        case JobState.Error:
                            // Cast sender as a job.
                            IJob job = (IJob)sender;
                            // Display or log error details as needed.
                            // LogJobStop(job.Id);
                            break;
                        default:
                            break;
                    }
                }
   
            }
        }

## <a name="media-services-learning-paths"></a>미디어 서비스 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>관련 링크
[Azure 미디어 서비스 분석 개요](media-services-analytics-overview.md)

[Azure 미디어 분석 데모](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)




<!--HONumber=Jan17_HO2-->


