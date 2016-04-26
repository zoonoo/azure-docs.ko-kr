<properties
	pageTitle="Azure 미디어 검색으로 동작 검색"
	description="Azure 미디어 동작 탐지기 MP(미디어 프로세서)를 사용하여 길고 특별하지 않은 동영상 중에서 원하는 섹션을 효율적으로 식별합니다."
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/08/2016"   
	ms.author="milanga;juliako;"/>

# Azure 미디어 검색으로 동작 검색

##개요

**Azure 미디어 동작 탐지기**의 MP(미디어 프로세서)를 사용하여 길고 특별하지 않은 동영상 중에서 원하는 섹션을 효율적으로 식별합니다. 동작 검색은 고정된 카메라 장면에서 동작이 발생한 동영상 섹션을 식별하는 데 사용할 수 있습니다. 이벤트가 발생한 경계 영역 및 타임스탬프가 있는 메타데이터를 포함하는 JSON 파일을 생성합니다.

보안 동영상 피드를 대상으로 하는 이 기술은 동작을 관련 이벤트와 그림자 및 조명 변화와 같은 가양성으로 분류할 수 있습니다. 이를 통해 수많은 관련 없는 이벤트로 인해 스팸 처리되지 않고 카메라 피드로부터 보안 경고를 생성할 수 있으며, 아주 긴 감시 동영상으로부터 필요한 순간을 추출할 수 있습니다.

**Azure 미디어 동작 탐지기** MP는 현재 미리 보기 상태입니다.

이 항목은 **Azure 미디어 동작 탐지기**에 대한 세부 정보 및 .NET용 Media Services SDK와 함께 사용하는 방법을 보여 줍니다.

##동작 검색기 입력 파일

동영상 파일입니다. 현재 MP4, MOV 및 WMV 형식이 지원됩니다.

##동작 검색기 출력 파일

동작 검색 작업은 동작 경고 및 동영상 내에서의 해당 범주를 설명하는 출력 자산으로 JSON 파일을 반환합니다. 파일에는 동영상에서 검색된 동작 시간 및 기간에 대한 정보가 포함됩니다.

현재 동작 검색은 출력에서 ***유형 2***로 참조하는 일반 동작 범주만 지원합니다.

X 및 Y 좌표와 크기는 0.0과 1.0 사이의 정규화된 부동을 사용하여 나열됩니다. 동작이 검색된 영역에 대한 경계 상자를 가져오려면 여기에 동영상 높이 및 너비 해상도를 곱합니다.

각 출력은 조각으로 분할되며 동영상 내에서 데이터를 정의하는 간격으로 세분화됩니다. 조각 길이는 같지 않아도 되며 동작이 전혀 검색되지 않을 때에는 조각 길이가 길어질 수 있습니다.

동작 탐지기 API는 고정된 배경의 동영상에 움직이는 개체가 있는 경우 표시기를 제공합니다. 동작 탐지기는 조명 및 그림자 변화와 같은 가양성을 줄이기 위해 학습됩니다. 현재 알고리즘의 한계로 야간 투시 동영상, 반투명 개체 및 작은 개체가 있습니다.

###<a id="output_elements"></a>출력 JSON 파일의 요소

다음 표는 출력 JSON 파일의 요소에 대해 설명합니다.

요소|설명
---|---
버전|동영상 API의 버전을 나타냅니다.
시간 간격|동영상의 초당 "틱"입니다.
Offset|"틱" 단위의 타임스탬프에 대한 시간 오프셋입니다. 동영상 API 버전 1.0에서는 항상 0입니다. 향후 지원하는 시나리오에서는 이 값이 변경될 수 있습니다.
프레임 속도|동영상의 초당 프레임 수입니다.
너비, 높이|픽셀 단위의 동영상 너비와 높이를 참조합니다.
시작|"틱" 단위의 시작 타임스탬프입니다.
기간|"틱" 단위의 이벤트 길이입니다.
간격|"틱" 단위의 이벤트에 있는 각 항목의 간격입니다.
이벤트|각 이벤트 조각에는 해당 기간 내에 검색된 동작이 포함됩니다.
형식|현재 버전에서 이 값은 일반 동작에 대해 항상 '2'입니다. 이 레이블은 향후 버전에서 동영상 API에 동작 분류에 대한 유연성을 제공합니다.
RegionID|위에서 설명했듯이 이 버전에서 이 값은 항상 0입니다. 이 레이블은 향후 버전에서 동영상 API에 다양한 영역에서 동작을 찾는 유연성을 제공합니다.
영역|동작에 관심이 있는 동영상 영역을 참조합니다. 동영상 API의 현재 버전에서는 영역을 지정할 수 없는 대신 동영상의 전체 화면이 동작 검색 영역이 됩니다. <br/>-ID는 영역을 나타냅니다. 이 버전에는 ID 0 하나밖에 없습니다. <br/>-사각형은 동작에 관심이 있는 영역의 모양을 나타냅니다. 이 버전에서는 항상 사각형입니다. <br/>-영역은 X, Y, 너비 및 높이 치수를 가집니다. X 및 Y 좌표는 0.0 ~ 1.0의 정규화된 배율 단위로 영역의 왼쪽 상단 XY 좌표를 나타냅니다. 너비와 높이는 0.0 ~ 1.0의 정규화된 배율 단위로 영역의 크기를 나타냅니다. 현재 버전에서 X, Y, 너비 및 높이는 항상 0, 0 및 1, 1로 고정됩니다. <br/>-조각: 메타데이터는 조각이라고 하는 다른 세그먼트로 청크 분할됩니다. 각 조각에는 시작, 기간, 간격 번호 및 이벤트가 포함됩니다. 이벤트가 없는 조각은 해당 시작 시간 및 기간 동안에 검색된 동작이 없음을 의미합니다.
대괄호|각 대괄호는 이벤트에서 하나의 간격을 나타냅니다. 해당 간격에 대한 빈 대괄호는 검색된 동작이 없음을 의미합니다.
 

##작업 구성(기본 설정)

**Azure 미디어 동작 탐지기**로 작업을 만들 때에는 구성 기본 설정을 지정해야 합니다. 현재 Azure 미디어 동작 탐지기 구성 기본 설정에서 옵션을 설정할 수 없습니다. 다음은 제공해야 하는 최소 구성 기본 설정입니다.

	{"version":"1.0"}

##샘플 동영상 및 동작 탐지기 출력

###실제 동작 예제

[실제 동작 예제](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fd54876c6-89a5-41de-b1f4-f45b6e10a94f%2FGarage.ism%2Fmanifest)

###JSON 출력

	 {
	 "version": "1.0",
	 "timescale": 60000,
	 "offset": 0,
	 "framerate": 30,
	 "width": 1920,
	 "height": 1080,
	 "regions": [
	   {
	     "id": 0,
	     "type": "rectangle",
	     "x": 0,
	     "y": 0,
	     "width": 1,
	     "height": 1
	   }
	 ],
	 "fragments": [
	   {
	     "start": 0,
	     "duration": 68510
	   },
	   {
	     "start": 68510,
	     "duration": 969999,
	     "interval": 969999,
	     "events": [
	       [
	         {
	           "type": 2,
	           "regionId": 0
	         }
	       ]
	     ]
	   },
	   {
	     "start": 1038509,
	     "duration": 41489
	   }
	 ]
	}

###가양성 예제

[가양성 예제(조명 변화):](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Ffdc6656b-1c10-4f3f-aa7c-07ba073c1615%2FLivingRoomLight.ism%2Fmanifest&tech=flash)

###JSON 출력

	{
	    "version": "1.0",
	    "timescale": 30000,
	    "offset": 0,
	    "framerate": 29.97,
	    "width": 1920,
	    "height": 1080,
	    "regions": [
	    {
	        "id": 0,
	        "type": "rectangle",
	        "x": 0,
	        "y": 0,
	        "width": 1,
	        "height": 1
	    }
	    ],
	    "fragments": [
	    {
	        "start": 0,
	        "duration": 320320
	    }
	    ]
	}


##제한 사항

- 지원되는 입력 동영상 형식에는 MP4, MOV 및 WMV가 있습니다.
- 동작 검색은 고정 배경 동영상에 최적화되어 있습니다. 알고리즘은 조명 변화, 그림자와 같은 거짓 경보를 줄이는 데 중점을 둡니다.
- 일부 동작은 기술적인 문제(예: 야간 투시 동영상, 반투명 개체 및 작은 개체)로 인해 검색되지 않을 수도 있습니다.


## 샘플 코드

다음 프로그램은 방법을 보여 줍니다.

1. 자산을 만들고 미디어 파일을 자산에 업로드합니다.
1. 다음 json 기본 설정을 포함하는 구성 파일을 기반으로 동영상 동작 검색 작업을 만듭니다. 
					
		{
		    "version": "1.0"
		}

1. 출력 JSON 파일을 다운로드합니다.
		 
        using System;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace VideoMotionDetection
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
		
		            // Run the VideoMotionDetection job.
		            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
		                                        @"C:\supportFiles\VideoMotionDetection\config.json");
		
		            // Download the job output asset.
		            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
		        }
		
		        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
		        {
		            // Create an asset and upload the input media file to storage.
		            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
		                "My Video Motion Detection Input Asset",
		                AssetCreationOptions.None);
		
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("My Video Motion Detection Job");
		
		            // Get a reference to Azure Media Motion Detector.
		            string MediaProcessorName = "Azure Media Motion Detector";
		
		            var processor = GetLatestMediaProcessorByName(MediaProcessorName);
		
		            // Read configuration from the specified file.
		            string configuration = File.ReadAllText(configurationFile);
		
		            // Create a task with the encoding details, using a string preset.
		            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
		                processor,
		                configuration,
		                TaskOptions.None);
		
		            // Specify the input asset.
		            task.InputAssets.Add(asset);
		
		            // Add an output asset to contain the results of the job.
		            task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);
		
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


##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##관련 링크

[Azure 미디어 서비스 분석 개요](media-services-analytics-overview.md)

<!---HONumber=AcomDC_0413_2016-->