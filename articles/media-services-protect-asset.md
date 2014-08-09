<properties linkid="develop-media-services-how-to-guides-encrypt-assets" urlDisplayName="Encrypt Assets in Media Services" pageTitle="How to Encrypt Assets in Media Services - Azure" metaKeywords="" description="Learn how to use Microsoft PlayReady Protection to encrypt an asset in Media Services. Code samples are written in C# and use the Media Services SDK for .NET. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Protect an Asset with PlayReady Protection" authors="migree" solutions="" manager="" editor="" />

방법: PlayReady Protection을 사용하여 자산 보호
===============================================

이 문서는 Azure 미디어 서비스 프로그래밍을 소개하는 시리즈 중 하나입니다. 이전 항목은 [방법: 작업 진행 상태 확인](http://go.microsoft.com/fwlink/?LinkID=301737&clcid=0x409)입니다.

Azure 미디어 서비스에서 Microsoft PlayReady Protection과 통합되어 자산을 암호화하는 작업을 제출할 수 있습니다. 이 섹션의 코드는 입력 폴더의 여러 스트리밍 파일을 사용하고 작업을 만든 후 PlayReady Protection을 사용하여 암호화합니다.

다음 예제에서는 PlayReady Protection을 제공하는 간단한 작업을 만드는 방법을 보여 줍니다.

1.  구성 데이터를 검색합니다. [Azure 미디어 암호기에 대한 작업 기본 설정](http://msdn.microsoft.com/ko-kr/library/hh973610.aspx) 항목에서 예제 구성 파일을 가져올 수 있습니다.
2.  MP4 입력 파일을 업로드합니다.
3.  MP4 파일을 부드러운 스트리밍 자산으로 변환합니다.
4.  PlayReady를 사용하여 자산을 암호화합니다.
5.  작업을 제출합니다.

다음 코드 예제는 단계를 구현하는 방법을 보여 줍니다.

``` {}
private static IJob CreatePlayReadyProtectionJob(string inputMediaFilePath, string primaryFilePath, string configFilePath)
{
    // Create a storage-encrypted asset and upload the MP4. 
    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, inputMediaFilePath);

    // Declare a new job to contain the tasks.
    IJob job = _context.Jobs.Create("My PlayReady Protection job");

    // Set up the first task. 

    // Read the task configuration data into a string. 
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));

    // Get a media processor instance
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");

    // Create a task with the conversion details, using the configuration data 
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
        processor,
        configMp4ToSmooth,
        _clearConfig);

    // Specify the input asset to be converted.
    task.InputAssets.Add(asset);

    // Add an output asset to contain the results of the job. We do not need 
    // to persist the output asset to storage, so set the shouldPersistOutputOnCompletion
    // parameter to false. 
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
    IAsset smoothOutputAsset = task.OutputAssets[0];

    // Set up the encryption task. 

    // Read the configuration data into a string. 
    string configPlayReady = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaEncryptor_PlayReadyProtection.xml"));

    // Get a media processor instance
    IMediaProcessor playreadyProcessor = GetLatestMediaProcessorByName("Azure Media Encryptor");

    // Create a second task, specifying a task name, the media processor, and configuration
    ITask playreadyTask = job.Tasks.AddNew("My PlayReady Task",
        playreadyProcessor,
        configPlayReady,
        _protectedConfig);

    // Add the input asset, which is the smooth streaming output asset from the first task. 
    playreadyTask.InputAssets.Add(smoothOutputAsset);

    // Add an output asset to contain the results of the job. Persist the output by setting 
    // the shouldPersistOutputOnCompletion param to true.
    playreadyTask.OutputAssets.AddNew("PlayReady protected output asset",
        AssetCreationOptions.None);

    // Use the following event handler to check job progress. 
    job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(StateChanged);

    // Launch the job.
    job.Submit();

    // Optionally log job details. This displays basic job details
    // to the console and saves them to a JobDetails-{JobId}.txt file 
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
```

PlayReady Protection에 대한 자세한 내용은 다음을 참조하십시오.

-   [Microsoft PlayReady를 사용하여 자산 보호](http://msdn.microsoft.com/ko-kr/library/dn189154.aspx)
-   [Microsoft PlayReady](http://www.microsoft.com/PlayReady/)

다음 단계
---------

이제 미디어 서비스를 사용하여 자산을 보호하는 방법을 배웠으므로 [자산 관리 방법](http://go.microsoft.com/fwlink/?LinkID=301943&clcid=0x409) 항목으로 이동하십시오.

