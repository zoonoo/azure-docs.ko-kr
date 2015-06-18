<properties 
	pageTitle=".NET을 사용하여 작업 진행 상태를 확인하는 방법" 
	description="이벤트 처리기 코드를 사용하여 작업 진행률을 추적하고 상태 업데이트를 보내는 방법에 대해 알아봅니다. 코드 샘플은 C#으로 작성되었으며 Media Services SDK for .NET을 사용합니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="juliako"/>

# 방법: 작업 진행 상태 확인

이 문서는 [미디어 서비스 주문형 비디오 워크플로](media-services-video-on-demand-workflow.md) 시리즈의 일부입니다. 

작업을 실행할 때 작업 진행 상태를 추적하는 방법이 종종 필요합니다. [StateChanged 이벤트 처리기를 정의](#statechange_event_handler) 하여 진행률을 확인할 수 있습니다. 또는 [Azure 큐 저장소를 사용하여 Media Services 작업 알림을 모니터링합니다](#check_progress_with_queues). 두 메서드는 이 항목에 설명 되어있습니다. 

## <a id="statechange_event_handler"></a>작업 진행 상태를 모니터링 하는 StateChanged 이벤트 처리기를 정의합니다.

다음 코드 예제는 StateChanged 이벤트 처리기를 정의합니다. 이 이벤트 처리기는 작업 진행 상태를 추적하고 상태에 따라 업데이트된 상태를 제공합니다. 또한 다음 코드는 LogJobStop 메서드를 정의합니다. 이 도우미 메서드는 오류 세부 정보를 기록합니다.

	private static void StateChanged(object sender, JobStateChangedEventArgs e)
	{
	    Console.WriteLine("Job state changed event:");
	    Console.WriteLine("  Previous state: " + e.PreviousState);
	    Console.WriteLine("  Current state: " + e.CurrentState);
	
	    switch (e.CurrentState)
	    {
	        case JobState.Finished:
	            Console.WriteLine();
	            Console.WriteLine("********************");
	            Console.WriteLine("Job is finished.");
	            Console.WriteLine("Please wait while local tasks or downloads complete...");
	            Console.WriteLine("********************");
	            Console.WriteLine();
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
	
	private static void LogJobStop(string jobId)
	{
	    StringBuilder builder = new StringBuilder();
	    IJob job = GetJob(jobId);
	
	    builder.AppendLine("\nThe job stopped due to cancellation or an error.");
	    builder.AppendLine("***************************");
	    builder.AppendLine("Job ID: " + job.Id);
	    builder.AppendLine("Job Name: " + job.Name);
	    builder.AppendLine("Job State: " + job.State.ToString());
	    builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
	    builder.AppendLine("Media Services account name: " + _accountName);
	    builder.AppendLine("Media Services account location: " + _accountLocation);
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
	    // Write the output to a local file and to the console. The template 
	    // for an error output file is:  JobStop-{JobId}.txt
	    string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
	    WriteToFile(outputFile, builder.ToString());
	    Console.Write(builder.ToString());
	}
	
	private static string JobIdAsFileName(string jobID)
	{
	    return jobID.Replace(":", "_");
	}



## <a id="check_progress_with_queues"></a>Azure 큐 저장소를 사용하여 Media Services 작업 알림을 모니터링합니다.

Microsoft Azure 미디어 서비스에는 알림 메시지를 [Azure 큐 저장소](storage/storage-dotnet-how-to-use-queues.md#what-is) 에 제공하는 기능이 있습니다. (미디어 작업 처리 시) 이 항목에서는 큐 저장소에서 이 알림 메시지를 가져오는 방법을 보여줍니다.

세계 어디에서나 큐 저장소에 배달된 메시지에 액세스할 수 있습니다. Azure 큐 메시징 아키텍처는 안정적이고 확장성이 뛰어납니다. 다른 메서드를 사용하는 동안 큐 저장소를 폴링하는 것이 좋습니다. 

Media Services 알림 수신에 대한 일반적인 시나리오는 인코딩 작업 후 일부 추가 작업을 수행해야 하는 콘텐츠 관리 시스템을 개발하는 경우(예를 들어, 다음에는 워크플로에서 다음 단계를 트리거하거나 콘텐츠를 게시)입니다. 

### 고려 사항

Azure 저장소 큐를 사용하는 미디어 서비스 응용 프로그램을 개발할 때 다음 사항을 고려합니다.

- 큐 서비스는 선입 선출(FIFO) 순차적 전달을 보장하지 않습니다. 자세한 내용은 [Azure 큐 및 Azure 서비스 버스 큐 비교 및 대조](https://msdn.microsoft.com/library/azure/hh767287.aspx)를 참조하세요.
- Azure 저장소 큐는 푸시 서비스가 아닙니다. 큐를 폴링해야 합니다. 
- 개수에 관계 없이 큐를 사용할 수 있습니다. 자세한 내용은 [큐 서비스 REST API](https://msdn.microsoft.com/library/azure/dd179363.aspx)를 참조하세요.
- Azure 저장소 큐에는 일부 제한 사항이 있으며 다음 문서에서 설명합니다. [Azure 큐 및 Azure 서비스 버스 큐 비교 및 대조](https://msdn.microsoft.com/library/azure/hh767287.aspx).

### 코드 예제

이 섹션의 코드는 다음 작업을 수행합니다.

1. 알림 메시지 형식에 매핑되는 **EncodingJobMessage** 클래스를 정의합니다. 코드는 큐에서 수신한 메시지를 **EncodingJobMessage** 유형의 개체로 deserialize합니다.
1. app.config 파일에서 미디어 서비스 및 저장소 계정 정보를 로드합니다. 이 정보를 사용하여 **CloudMediaContext** 및 **CloudQueue** 개체를 만듭니다.
1. 인코딩 작업에 대한 알림 메시지를 받는 큐를 만듭니다.
1. 큐에 매핑되는 알림 끝점을 만듭니다.
1. 알림 끝점 작업에 연결하고 인코딩 작업을 제출합니다. 작업에 연결하는 여러 알림 끝점이 있을 수 있습니다.
1. 이 예에서 우리의 관심은 작업 처리의 최종 상태에 있으므로 **NotificationJobState.FinalStatesOnly**를 **AddNew** 메서드로 전달합니다. 
		
		job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
1. NotificationJobState.All을 전달하는 경우 모든 상태 변경 알림을 가져와야 합니다. 큐에 대기-> 예약 -> 처리-> 완료됩니다. 그러나 앞에서 설명한 대로 Azure 저장소 큐 서비스가 순차적 전달을 보장하지 않습니다. 주문 메시지에 타임스탬프 속성(아래 예제에서는 EncodingJobMessage 형식에서 정의됨)을 사용할 수 있습니다. 중복된 알림 메시지를 받을 수 있습니다. ETag 속성(EncodingJobMessage 형식에서 정의됨)을 사용하여 중복을 확인합니다. 또한 일부 상태 변경 알림을 건너뛸 수 있습니다. 
1. 10초마다 큐를 검사하여 작업이 완성된 상태가 될 때를 기다립니다. 처리된 후 메시지를 삭제합니다.
1. 큐와 알림 끝점을 삭제합니다.

>[AZURE.NOTE]작업 상태 모니터링 방법으로 다음 예제와 같이 알림 메시지 수신을 권장합니다.
>
>또는 **IJob.State** 속성을 사용하여 작업 상태를 확인할 수 있습니다.  작업 완료에 대한 알림 메시지를 **IJob**에 대한 상태가 **마침**으로 설정되기 전에 수신할 수 있습니다. **IJob.State** 속성은 약간의 지연 시간을 포함하여 정확한 상태를 반영합니다.

	
	using System;
	using System.Linq;
	using System.Configuration;
	using System.IO;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using System.Web;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;
	using System.Runtime.Serialization.Json;
	
	namespace JobNotification
	{
	    public class EncodingJobMessage
	    {
	        // MessageVersion is used for version control. 
	        public String MessageVersion { get; set; }
	    
	        // Type of the event. Valid values are 
	        // JobStateChange and NotificationEndpointRegistration.
	        public String EventType { get; set; }
	    
	        // ETag is used to help the customer detect if 
	        // the message is a duplicate of another message previously sent.
	        public String ETag { get; set; }
	    
	        // Time of occurrence of the event.
	        public String TimeStamp { get; set; }
	
	        // Collection of values specific to the event.
	
	        // For the JobStateChange event the values are:
	        //     JobId - Id of the Job that triggered the notification.
	        //     NewState- The new state of the Job. Valid values are:
	        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
	        //     OldState- The old state of the Job. Valid values are:
	        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
	
	        // For the NotificationEndpointRegistration event the values are:
	        //     NotificationEndpointId- Id of the NotificationEndpoint 
	        //          that triggered the notification.
	        //     State- The state of the Endpoint. 
	        //          Valid values are: Registered and Unregistered.
	
	        public IDictionary<string, object> Properties { get; set; }
	    }
	
	    class Program
	    {
	        private static CloudMediaContext _context = null;
	        private static CloudQueue _queue = null;
	        private static INotificationEndPoint _notificationEndPoint = null;
	
	        private static readonly string _singleInputMp4Path =
	            Path.GetFullPath(@"C:\supportFiles\multifile\BigBuckBunny.mp4");
	
	        static void Main(string[] args)
	        {
	            // Get the values from app.config file.
	            string mediaServicesAccountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
	            string mediaServicesAccountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	            string storageConnectionString = ConfigurationManager.AppSettings["StorageConnectionString"];
	
	
	            string endPointAddress = Guid.NewGuid().ToString();
	
	            // Create the context. 
	            _context = new CloudMediaContext(mediaServicesAccountName, mediaServicesAccountKey);
	
	            // Create the queue that will be receiving the notification messages.
	            _queue = CreateQueue(storageConnectionString, endPointAddress);
	
	            // Create the notification point that is mapped to the queue.
	            _notificationEndPoint = 
	                    _context.NotificationEndPoints.Create(
	                    Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);
	
	
	            if (_notificationEndPoint != null)
	            {
	                IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
	                WaitForJobToReachedFinishedState(job.Id);
	            }
	
	            // Clean up.
	            _queue.Delete();      
	            _notificationEndPoint.Delete();
	       }
	
	
	        static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
	        {
	            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);
	
	            // Create the queue client
	            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
	
	            // Retrieve a reference to a queue
	            CloudQueue queue = queueClient.GetQueueReference(endPointAddress);
	
	            // Create the queue if it doesn't already exist
	            queue.CreateIfNotExists();
	
	            return queue;
	        }
	 
	
	        // Upload a video file, and encode to Smooth Streaming format
	        public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
	        {
	            // Declare a new job.
	            IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");
	
	            //Create an encrypted asset and upload the mp4. 
	            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
	                inputMediaFilePath);
	
	            // Get a media processor reference, and pass to it the name of the 
	            // processor to use for the specific task.
	            IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
	
	            // Create a task with the conversion details, using a configuration file. 
	            ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
	                processor,
	                "H264 Smooth Streaming 720p",
	                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
	
	            // Specify the input asset to be encoded.
	            task.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job.
	            task.OutputAssets.AddNew("Output asset",
	                AssetCreationOptions.None);
	
	            // Add a notification point to the job. You can add multiple notification points.  
	            job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, 
	                _notificationEndPoint);
	
	            job.Submit();
	
	            return job;
	        }
	
	        public static void WaitForJobToReachedFinishedState(string jobId)
	        {
	            int expectedState = (int)JobState.Finished;
	            int timeOutInSeconds = 600;
	
	            bool jobReachedExpectedState = false;
	            DateTime startTime = DateTime.Now;
	            int jobState = -1;
	
	            while (!jobReachedExpectedState)
	            {
	                // Specify how often you want to get messages from the queue.
	                Thread.Sleep(TimeSpan.FromSeconds(10));
	
	                foreach (var message in _queue.GetMessages(10))
	                {
	                    using (Stream stream = new MemoryStream(message.AsBytes))
	                    {
	                        DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
	                        settings.UseSimpleDictionaryFormat = true;
	                        DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
	                        EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);
	
	                        Console.WriteLine();
	
	                        // Display the message information.
	                        Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
	                        Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
	                        Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
	                        Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
	                        foreach (var property in encodingJobMsg.Properties)
	                        {
	                            Console.WriteLine("    {0}: {1}", property.Key, property.Value);
	                        }
	
	                        // We are only interested in messages 
	                        // where EventType is "JobStateChange".
	                        if (encodingJobMsg.EventType == "JobStateChange")
	                        {
	                            string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
	                            if (JobId == jobId)
	                            {
	                                string oldJobStateStr = (String)encodingJobMsg.Properties.
	                                                            Where(j => j.Key == "OldState").FirstOrDefault().Value;
	                                string newJobStateStr = (String)encodingJobMsg.Properties.
	                                                            Where(j => j.Key == "NewState").FirstOrDefault().Value;
	
	                                JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
	                                JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);
	
	                                if (newJobState == (JobState)expectedState)
	                                {
	                                    Console.WriteLine("job with Id: {0} reached expected state: {1}", 
	                                        jobId, newJobState);
	                                    jobReachedExpectedState = true;
	                                    break;
	                                }
	                            }
	                        }
	                    }
	                    // Delete the message after we've read it.
	                    _queue.DeleteMessage(message);
	                }
	
	                // Wait until timeout
	                TimeSpan timeDiff = DateTime.Now - startTime;
	                bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
	                if (timedOut)
	                {
	                    Console.WriteLine(@"Timeout for checking job notification messages, 
	                                        latest found state ='{0}', wait time = {1} secs",
	                        jobState,
	                        timeDiff.TotalSeconds);
	
	                    throw new TimeoutException();
	                }
	            }
	        }
	   
	        static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
	        {
	            var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(), 
	                assetCreationOptions);
	
	            var fileName = Path.GetFileName(singleFilePath);
	
	            var assetFile = asset.AssetFiles.Create(fileName);
	
	            Console.WriteLine("Created assetFile {0}", assetFile.Name);
	            Console.WriteLine("Upload {0}", assetFile.Name);
	
	            assetFile.Upload(singleFilePath);
	            Console.WriteLine("Done uploading of {0}", assetFile.Name);
	
	            return asset;
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

위의 예제는 다음과 같이 출력됩니다. 값은 달라질 수 있습니다.
	
	Created assetFile BigBuckBunny.mp4
	Upload BigBuckBunny.mp4
	Done uploading of BigBuckBunny.mp4
	
	EventType: NotificationEndPointRegistration
	MessageVersion: 1.0
	ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
	TimeStamp: 2013-05-14T20:22:37
	    NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
	    State: Registered
	    Name: dde957b2-006e-41f2-9869-a978870ac620
	    Created: 2013-05-14T20:22:35
	
	EventType: JobStateChange
	MessageVersion: 1.0
	ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
	TimeStamp: 2013-05-14T20:24:40
	    JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
	    JobName: My MP4 to Smooth Streaming encoding job
	    NewState: Finished
	    OldState: Processing
	    AccountName: westeuropewamsaccount
	job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected 
	State: Finished
	


<!--HONumber=52-->