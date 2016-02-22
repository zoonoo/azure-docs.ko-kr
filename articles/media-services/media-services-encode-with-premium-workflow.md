<properties 
	pageTitle="미디어 인코더 Premium 워크플로를 사용한 고급 인코딩" 
	description="미디어 인코더 Premium 워크플로를 사용하여 인코딩하는 방법에 대해 알아봅니다. 코드 샘플은 C#으로 작성되었으며 Media Services SDK for .NET을 사용합니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2016" 
	ms.author="juliako"/>

#미디어 인코더 Premium 워크플로를 사용한 고급 인코딩

>[AZURE.NOTE]프리미엄 인코더 관련 질문은 mepd@microsoft.com으로 문의하세요.
>
>중국에서는 이 항목에서 설명하는 미디어 인코더 Premium 워크플로 미디어 프로세서를 사용할 수 없습니다.

##개요

Microsoft Azure 미디어 서비스는 **미디어 인코더 Premium 워크플로** 미디어 프로세서를 도입 중입니다. 이 프로세서는 프리미엄 주문형 워크플로에 고급 인코딩 기능을 제공합니다.

다음 항목에서는 **미디어 인코더 Premium 워크플로**와 관련된 세부 정보를 간력하게 설명합니다.

- [미디어 인코더 Premium 워크플로에서 지원하는 형식](media-services-premium-workflow-encoder-formats.md) – **미디어 인코더 Premium 워크플로**에서 지원하는 파일 형식 및 코덱에 대해 설명합니다.

- [인코더 비교](media-services-encode-asset.md#compare_encoders) 섹션에서는 **미디어 인코더 Premium 워크플로**, **미디어 인코더 표준** 및 **Azure 미디어 인코더**의 인코딩 기능을 비교합니다.

이 항목에서는 .NET을 사용하여 **미디어 인코더 Premium 워크플로**로 인코딩하는 방법을 보여 줍니다.

**미디어 인코더 Premium 워크플로**의 인코딩 태스크에는 워크플로 파일이라는 별도의 구성 파일이 필요합니다. 이러한 파일은 확장명이 .workflow이고 [Workflow Designer](media-services-workflow-designer.md) 도구를 사용하여 작성됩니다.

>[AZURE.NOTE]프리미엄 인코더 관련 질문은 mepd@microsoft.com으로 문의하세요.

##인코딩

**미디어 인코더 Premium 워크플로**의 인코딩 태스크에는 워크플로 파일이라는 별도의 구성 파일이 필요합니다. 이러한 파일은 확장명이 .workflow이고 [Workflow Designer](media-services-workflow-designer.md) 도구를 사용하여 작성됩니다.


[여기](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)에서 기본 워크플로 파일을 가져올 수도 있습니다. 폴더에는 이러한 파일에 대한 설명도 포함되어 있습니다.

워크플로 파일은 자산으로 미디어 서비스 계정에 업로드되고 이 자산은 인코딩 태스크에 전달되어야 합니다.

다음 예제에서는 **미디어 인코더 Premium 워크플로**를 사용하여 인코딩하는 방법을 보여 줍니다.

다음 단계를 수행합니다.
 
1. 자산을 만들고 워크플로 파일을 업로드합니다. 
2. 자산을 만들고 소스 미디어 파일을 업로드합니다.
3. "미디어 인코더 Premium 워크플로" 미디어 프로세서를 가져옵니다.
4. 작업 및 태스크를 만듭니다. 

	대부분의 경우 태스크에 대한 구성 문자열은 비어 있습니다(다음 예제 참조). 인코딩 태스크에 XML 문자열을 제공하는 경우 런타임 속성을 동적으로 설정해야 하는 몇 가지 고급 시나리오가 있습니다. 이러한 시나리오의 예로는 오버레이 만들기, 순차 또는 병렬 미디어 연결, 자막 작성 등이 있습니다.
5. 태스크에 입력 자산 두 개를 추가합니다.
	
	a. 첫 번째 – 워크플로 자산입니다.

	b. 두 번째 – 비디오 자산입니다.
	
	**참고**: 워크플로 자산은 미디어 자산보다 먼저 태스크에 추가되어야 합니다. 이 태스크에 대한 구성 문자열은 비어 있어야 합니다.

6. 인코딩 작업을 제출합니다.

다음은 전체 예제입니다. 미디어 서비스 .NET 개발을 설정하는 방법에 대한 자세한 내용은 [.NET을 사용한 미디어 서비스 개발](media-services-dotnet-how-to-use.md)을 참조하세요


 	using System; 
	using System.Linq;
	using System.Configuration;
	using System.IO;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.MediaServices.Client;
	
	namespace MediaEncoderPremiumWorkflowSample
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
	
	        private static readonly string _supportFiles =
	            Path.GetFullPath(@"../..\Media");
	
	        private static readonly string _workflowFilePath =
	            Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");
	        
	        private static readonly string _singleMP4InputFilePath =
	            Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");
	
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
	            var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
	            IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset); 
	
	        }
	
	        static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
	        {
	            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
	            var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
	
	            var fileName = Path.GetFileName(singleFilePath);
	
	            var assetFile = asset.AssetFiles.Create(fileName);
	
	            Console.WriteLine("Created assetFile {0}", assetFile.Name);
	
	            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
	                                                                AccessPermissions.Write | AccessPermissions.List);
	
	            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);
	
	            Console.WriteLine("Upload {0}", assetFile.Name);
	
	            assetFile.Upload(singleFilePath);
	            Console.WriteLine("Done uploading {0}", assetFile.Name);
	
	            locator.Delete();
	            accessPolicy.Delete();
	
	            return asset;
	        }
	
	        static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
	        {
	            // Declare a new job.
	            IJob job = _context.Jobs.Create("Premium Workflow encoding job");
	            // Get a media processor reference, and pass to it the name of the 
	            // processor to use for the specific task.
	            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");
	
	            // Create a task with the encoding details, using a string preset.
	            ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
	                processor,
	                "",
	                TaskOptions.None);
	
	            // Specify the input asset to be encoded.
	            task.InputAssets.Add(workflow);
	            task.InputAssets.Add(video); // we add one asset
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.None, which 
	            // means the output asset is not encrypted. 
	            task.OutputAssets.AddNew("Output asset",
	                AssetCreationOptions.None);
	
	            // Use the following event handler to check job progress.  
	            job.StateChanged += new
	                    EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	            // Launch the job.
	            job.Submit();
	
	            // Check job execution and wait for job to finish. 
	            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	            progressJobTask.Wait();
	
	            // If job state is Error the event handling 
	            // method for job progress should log errors.  Here we check 
	            // for error state and exit if needed.
	            if (job.State == JobState.Error)
	            {
	                throw new Exception("\nExiting method due to job error.");
	            }
	
	            return job.OutputMediaAssets[0];
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
	                    LogJobStop(job.Id);
	                    break;
	                default:
	                    break;
	            }
	        }
	
	        static private void LogJobStop(string jobId)
	        {
	            StringBuilder builder = new StringBuilder();
	            IJob job = _context.Jobs.Where(j => j.Id == jobId).FirstOrDefault();
	
	            builder.AppendLine("\nThe job stopped due to cancellation or an error.");
	            builder.AppendLine("***************************");
	            builder.AppendLine("Job ID: " + job.Id);
	            builder.AppendLine("Job Name: " + job.Name);
	            builder.AppendLine("Job State: " + job.State.ToString());
	            builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
	            builder.AppendLine("Media Services account name: " + _mediaServicesAccountName);
	            // Log job errors if they exist.  
	            if (job.State == JobState.Error)
	            {
	                builder.Append("Error Details: \n");
	                foreach (ITask task in job.Tasks)
	                {
	                    foreach (ErrorDetail detail in task.ErrorDetails)
	                    {
	                        builder.AppendLine("  Task Id: " + task.Id);
	                        builder.AppendLine("    Error Code: " + detail.Code);
	                        builder.AppendLine("    Error Message: " + detail.Message + "\n");
	                    }
	                }
	            }
	            builder.AppendLine("***************************\n");
	
	            Console.Write(builder.ToString());
	        }
	
	
	        static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	        {
	            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	            if (processor == null)
	                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	            return processor;
	        }
	    }
	}



##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->