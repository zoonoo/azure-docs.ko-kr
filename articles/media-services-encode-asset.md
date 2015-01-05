<properties urlDisplayName="How to Encode an Asset" pageTitle="미디어 서비스용 자산을 인코딩하는 방법 - Azure" metaKeywords="" description="Learn how to use the Azure Media Encoder to encode media content on Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Encode an Asset" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />


#방법: 자산 인코드
이 문서는 Azure 미디어 서비스 프로그래밍을 소개하는 시리즈 중 하나입니다. 이전 항목은 [방법: 미디어 프로세서 가져오기](../media-services-get-media-processor/)입니다.

서버의 미디어 콘텐츠의 경우 Azure Media Encoder를 사용하여 다수의 미디어 인코딩 및 형식으로 콘텐츠를 인코드할 수 있습니다. 미디어 서비스 파트너가 제공하는 인코더, 즉 [Azure 마켓플레이스][]를 통해 사용할 수 있는 타사 인코더를 사용할 수도 있습니다. [인코더 미리 설정][] 문자열이나 구성 파일을 사용하여 인코딩 작업의 세부 정보를 지정할 수 있습니다. 

##MP4 가변 품질 세트로 인코드
Mezzanine 파일을 MP4 가변 품질 세트로 인코드하고 동적 패키징을 사용하여 콘텐츠를 배달하는 것이 좋습니다. 자세한 내용은 [Media Services SDK for .NET을 사용하여 인코딩 작업 만들기](http://msdn.microsoft.com/ko-kr/library/azure/dn282273.aspx), [동적 패키징](http://msdn.microsoft.com/ko-kr/library/azure/jj889436.aspx) 및 [콘텐츠 배달](http://msdn.microsoft.com/ko-kr/library/azure/hh973618.aspx)을 참조하세요.

##MP4로 인코드
다음 메서드는 단일 자산을 업로드하고 H264 인코딩을 사용하여 720p 해상도에서 단일 MP4를 만드는 "H264 Broadband 720p" 기본 설정을 사용하여 자산을 MP4로 인코드하는 작업을 만듭니다.
<pre><code>
	static IJob CreateEncodingJob(string inputMediaFilePath, string outputFolder)
	{
    	//Create an encrypted asset and upload to storage.
		IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
			inputMediaFilePath);

		// Declare a new job.

    	IJob job = _context.Jobs.Create("My encoding job");
	
		// Get a reference to the Azure Media Encoder
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
    
		// Create a task with the encoding details, using a string preset.
    	ITask task = job.Tasks.AddNew("My encoding task",
        	processor,
	        "H264 Broadband 720p",
        	_protectedConfig);
    
		// Specify the input asset to be encoded.
    	task.InputAssets.Add(asset);
    
		// Add an output asset to contain the results of the job. 
    	// This output is specified as AssetCreationOptions.None, which 
    	// means the output asset is in the clear (unencrypted). 
    	task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
    
		// Use the following event handler to check job progress.  
    	job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);
    
		// Launch the job.
    	job.Submit();
    
		// Optionally log job details. This displays basic job details
    	// to the console and saves them to a JobDetails-JobId.txt file 
    	// in your output folder.
    	LogJobDetails(job.Id);
    
		// Check job execution and wait for job to finish. 
    	Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    	progressJobTask.Wait();
    
		// If job state is Error, the event handling 
    	// method for job progress should log errors.  Here we check 
    	// for error state and exit if needed.
    	if (job.State == JobState.Error)
    	{
	        Console.WriteLine("\nExiting method due to job error.");
        	return job;
    	}
    
		// Perform other tasks. For example, access the assets that are the output of a job, 
    	// either by creating URLs to the asset on the server, or by downloading. 
    	return job;
	}

	private static void StateChanged(object sender, JobStateChangedEventArgs e)
	{
		Console.WriteLine("Job state changed event:");
	    Console.WriteLine("  Previous state: " + e.PreviousState);
	    Console.WriteLine("  Current state: " + e.CurrentState);
	    switch (e.CurrentState)
	    {
        	case JobState.Finished:
           	Console.WriteLine();
           	Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
</code></pre>
<h2>부드러운 스트리밍으로 인코드</h2>
비디오를 부드러운 스트리밍으로 인코드하려는 경우 두 가지 옵션이 있습니다.
<ul>
<li> 곧바로 부드러운 스트리밍으로 인코드 </li>
<li> MP4로 인코드한 후 부드러운 스트리밍으로 변환</li>
</ul>

곧바로 부드러운 스트리밍으로 인코드하려면 위에 나온 코드를 사용하고 부드러운 스트리밍 인코더 기본 설정 중 하나를 사용합니다. 인코더 미리 설정 전체 목록은 [Azure Media Encoder용 작업 미리 설정 문자열](http://msdn.microsoft.com/ko-kr/library/jj129582.aspx)을 참조하세요. 

MP4를 부드러운 스트리밍으로 변환하려면 Azure Media Packager를 사용합니다. Azure Media Packager에서는 문자열 기본 설정이 지원되지 않으므로, XML에서 구성 옵션을 지정해야 합니다. MP4를 부드러운 스트리밍으로 변환하는 데 필요한 XML은 [Azure Media Packager의 작업 미리 설정][]에서 찾을 수 있습니다. XML을 복사하여 프로젝트에서 MediaPackager_MP4ToSmooth.xml이라는 파일에 붙여넣습니다. 다음 코드는 MP4 자산을 부드러운 스트리밍으로 변환하는 방법을 보여 줍니다. 다음 메서드는 기존 자산을 변환합니다. 
<pre><code>
private static IJob ConvertMP4toSmooth(IAsset assetToConvert, string configFilePath)
 {
	// Declare a new job to contain the tasks
    IJob job = _context.Jobs.Create("Convert to Smooth Streaming job");
    // Set up the first Task to convert from MP4 to Smooth Streaming. 
    // Read in task configuration XML
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));
    // Get a media packager reference
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");
    // Create a task with the conversion details, using the configuration data
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
           processor,
           configMp4ToSmooth,
           TaskOptions.None);
    // Specify the input asset to be converted.
    task.InputAssets.Add(assetToConvert);
    // Add an output asset to contain the results of the job.
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
    // Use the following event handler to check job progress. 
	// The StateChange method is the same as the one in the previous sample
    job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);
    // Launch the job.
    job.Submit();
    // Check job execution and wait for job to finish. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();
    // Get a refreshed job reference after waiting on a thread.
    job = GetJob(job.Id);
    // Check for errors
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
    }
    return job;
}
</code></pre>

자산 처리에 대한 자세한 내용은 다음을 참조하세요.
<ul>
<li><a href="http://msdn.microsoft.com/ko-kr/library/jj129580.aspx">Media Services SDK for .NET을 사용하여 자산 처리(영문)</a></li>
<li><a href="http://msdn.microsoft.com/ko-kr/library/jj129574.aspx">Media Services REST API를 사용하여 자산 처리(영문)</a></li>
</ul>

##다음 단계
자산을 인코드하는 작업을 만드는 방법을 알아보았습니다. 이제 [미디어 서비스 작업 진행 상태를 확인하는 방법](../media-services-check-job-progress/) 항목으로 이동하세요.

[Azure 마켓플레이스]: https://datamarket.azure.com/
[인코더 미리 설정]: http://msdn.microsoft.com/ko-kr/library/dn619392.aspx
[방법: 미디어 프로세서 인스턴스 가져오기]:http://go.microsoft.com/fwlink/?LinkId=301732
[방법: 암호화된 자산 업로드]:http://go.microsoft.com/fwlink/?LinkId=301733
[방법: 다운로드를 통해 자산 제공]:http://go.microsoft.com/fwlink/?LinkId=301734
[작업 진행 상태를 확인하는 방법]:http://go.microsoft.com/fwlink/?LinkId=301737
[Azure Media Packager의 작업 미리 설정]:http://msdn.microsoft.com/ko-kr/library/windowsazure/hh973635.aspx

<!--HONumber=35.1-->
