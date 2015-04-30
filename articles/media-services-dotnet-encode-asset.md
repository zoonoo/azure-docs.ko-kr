<properties 
	pageTitle="Azure 미디어 인코더를 사용하여 자산을 인코딩하는 방법" 
	description="Azure 미디어 인코더를 사용하여 미디어 서비스에서 미디어 콘텐츠를 인코딩하는 방법에 대해 알아봅니다. 코드 샘플은 C#으로 작성되었으며 Media Services SDK for .NET을 사용합니다." 
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


# Azure 미디어 인코더를 사용하여 자산을 인코딩하는 방법

이 문서는 [미디어 서비스 주문형 비디오 워크플로](media-services-video-on-demand-workflow.md) 시리즈의 일부입니다. 

## 개요

인터넷을 통해 디지털 비디오를 배달하려면 미디어를 압축해야 합니다. 디지털 비디오 파일은 크기가 상당히 크기 때문에 인터넷을 통해 전달하거나 고객의 장치에서 제대로 표시하지 못할 수 있습니다. 인코딩은 고객이 미디어를 볼 수 있도록 비디오 및 오디오를 압축하는 과정입니다.

인코딩 작업은 미디어 서비스에서 가장 일반적인 처리 작업 중 하나입니다. 인코딩 작업을 만들어 한 인코딩에서 다른 인코딩으로 미디어 파일을 변환합니다. 인코딩할 때는 미디어 서비스 기본 제공 미디어 인코더를 사용할 수 있습니다. 또한 미디어 서비스 파트너가 제공하는 인코더를 사용할 수도 있습니다. 타사 인코더는 Azure 마켓플레이스를 통해 사용할 수 있습니다. 인코더에 정의된 미리 설정 문자열을 사용하여 또는 미리 설정 구성 파일을 사용하여 인코딩 작업의 세부 정보를 지정할 수 있습니다. 사용할 수 있는 미리 설정 유형을 보려면 Azure Media Services에 대한 작업 미리 설정을 참조하세요. 타사 인코더를 사용하는 경우 [파일 유효성을 검사](https://msdn.microsoft.com/library/azure/dn750842.aspx)해야 합니다.

항상 mezzanine 파일을 적응 비트 전송률 MP4 집합으로 인코딩한 다음 [동적 패키징](https://msdn.microsoft.com/library/azure/jj889436.aspx)을 사용하여 원하는 형식으로 집합을 변환하는 것이 좋습니다.

출력 자산이 암호화된 저장소인 경우 자산 배달 정책을 구성해야 합니다. 자세한 내용은 [자산 배달 정책 구성](media-services-dotnet-configure-asset-delivery-policy.md)을 참조하세요.

## 작업을 단일 인코딩 작업으로 만들기 

Azure 미디어 인코더로 인코딩할 때 [여기](https://msdn.microsoft.com/library/azure/dn619389.aspx)에 지정된 작업 구성 기본 설정을 사용할 수 있습니다.

### .NET에 Media Services SDK 사용하기  

다음 **EncodeToAdaptiveBitrateMP4Set** 메서드는 인코딩 작업을 만들고 하나의 인코딩 태스크를 해당 작업에 추가합니다. 해당 태스크는 "Azure 미디어 인코더"를 사용하여 "H264 Adaptive Bitrate MP4 Set 720p"로 인코드합니다. 

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
    {
        var encodingPreset = "H264 Adaptive Bitrate MP4 Set 720p";

        IJob job = _context.Jobs.Create(String.Format("Encoding {0} into to {1}",
                                inputAsset.Name,
                                encodingPreset));

        var mediaProcessors = GetLatestMediaProcessorByName("Azure Media Encoder");

        ITask encodeTask = job.Tasks.AddNew("Encoding", mediaProcessors, encodingPreset, TaskOptions.None);
        
        encodeTask.InputAssets.Add(inputAsset);

        // Specify the storage-encrypted output asset.
        encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), 
            AssetCreationOptions.StorageEncrypted);


        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
        job.Submit();
        job.GetExecutionProgressTask(CancellationToken.None).Wait();

        return job.OutputMediaAssets[0];
    }

    private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
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
                break;
            default:
                break;
        }
    }

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
           ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

### .NET Extensions에 Media Services SDK 사용하기

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
    {
        // 1. Prepare a job with a single task to transcode the specified mezzanine asset
        //    into a multi-bitrate asset.
        IJob job = _context.Jobs.CreateWithSingleTask(
            MediaProcessorNames.AzureMediaEncoder,
            MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
            asset,
            "Adaptive Bitrate MP4",
            AssetCreationOptions.None);

        Console.WriteLine("Submitting transcoding job...");

        // 2. Submit the job and wait until it is completed.
        job.Submit();
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    } 

## 연결된 작업으로 작업 만들기 

대부분의 응용 프로그램 시나리오에서는 개발자가 일련의 처리 작업을 만들려고 합니다. 미디어 서비스에서 일련의 연결된 작업을 만들 수 있습니다. 각 작업은 서로 다른 처리 단계를 수행하고 다양한 미디어 프로세서를 사용할 수 있습니다. 연결된 작업은 한 작업에서 다른 작업으로 자산을 전달하여 자산에 대한 선형 시퀀스 작업을 수행할 수 있습니다. 그러나 작업에서 수행된 작업은 시퀀스에 있을 필요가 없습니다. 연결된 작업을 만들면 연결된 **ITask** 개체는 단일 **IJob** 개체에 만들어집니다.

>[AZURE.NOTE] 현재 작업은 작업당 30개로 제한됩니다. 30개 이상의 작업을 연결해야 하는 경우 둘 이상의 작업을 만들어 작업을 연결합니다.

다음 **CreateChainedTaskEncodingJob** 메서드는 두 개의 연결된 태스크를 포함하는 하나의 작업을 만듭니다. 결과적으로 이 메서드는 두 출력 자산을 포함하는 작업을 반환합니다.

	
    public static IJob CreateChainedTaskEncodingJob(IAsset asset)
    {
        // Declare a new job.
        IJob job = _context.Jobs.Create("My task-chained encoding job");

        // Set up the first task to encode the input file.

        // Get a media processor reference
        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My encoding task",
            processor,
           "H264 Adaptive Bitrate MP4 Set 720p",
            TaskOptions.ProtectedConfiguration);

        // Specify the input asset to be encoded.
        task.InputAssets.Add(asset);

        // Specify the storage-encrypted output asset.
        task.OutputAssets.AddNew("My storage-encrypted output asset",
            AssetCreationOptions.StorageEncrypted);

        // Set up the second task to decrypt the encoded output file from 
        // the first task.

        // Get another media processor instance
        IMediaProcessor decryptProcessor = GetLatestMediaProcessorByName("Storage Decryption");

        // Declare the decryption task. 
        ITask decryptTask = job.Tasks.AddNew("My decryption task",
            decryptProcessor,
            string.Empty,
            TaskOptions.None);

        // Specify the input asset to be decrypted. This is the output 
        // asset from the first task. 
        decryptTask.InputAssets.Add(task.OutputAssets[0]);

        // Specify an output asset to contain the results of the job. 
        // This should have AssetCreationOptions.None. 
        decryptTask.OutputAssets.AddNew("My decrypted output asset",
            AssetCreationOptions.None);

        // Use the following event handler to check job progress. 
        job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(JobStateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        //return job that contains two output assets.
        return job;
    }


## 다음 단계

[Azure 마켓플레이스]: https://datamarket.azure.com/
[인코더 기본 설정]: http://msdn.microsoft.com/library/dn619392.aspx
[방법: 미디어 프로세서 인스턴스 가져오기]:http://go.microsoft.com/fwlink/?LinkId=301732
[방법: 암호화된 자산 업로드]::http://go.microsoft.com/fwlink/?LinkId=301733
[방법: 다운로드를 통해 자산 제공]:http://go.microsoft.com/fwlink/?LinkId=301734
[작업 진행 상태를 확인하는 방법]:http://go.microsoft.com/fwlink/?LinkId=301737
[Azure Media Packager의 작업 미리 설정]:http://msdn.microsoft.com/library/windowsazure/hh973635.aspx

<!--HONumber=52-->