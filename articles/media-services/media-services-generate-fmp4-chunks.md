---
title: "fMP4 청크를 생성하는 Azure Media Services 인코딩 작업 만들기 | Microsoft Docs"
description: "이 항목에서는 fMP4 청크를 생성하는 인코딩 작업을 만드는 방법을 보여 줍니다. 이 작업을 Media Encoder Standard 또는 Media Encoder Premium 워크플로 인코더와 함께 사용하면 출력 자산에는 ISO MP4 파일 대신 fMP4 청크가 포함됩니다."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: b7029ac5-eadd-4a2f-8111-1fc460828981
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2970606fb9a88b23d958b9e20a981e2ecbe72943
ms.openlocfilehash: 9b373d3174592d26b74896eda712ff7cae1f787e
ms.lasthandoff: 02/28/2017


---
#  <a name="create-an-encoding-task-that-generates-fmp4-chunks"></a>fMP4 청크를 생성하는 인코딩 작업 만들기

## <a name="overview"></a>개요

이 항목에서는 ISO MP4 파일 대신 조각화된 MP4(fMP4) 청크를 생성하는 인코딩 작업을 만드는 방법을 보여 줍니다. fMP4 청크를 생성하려면 다음 코드 조각처럼 **Media Encoder Standard** 또는 **Media Encoder Premium 워크플로** 인코더를 사용하여 인코딩 작업을 만들고 **AssetFormatOption.AdaptiveStreaming**을 지정합니다.  
    
    task.OutputAssets.AddNew(@"Output Asset containing fMP4 chunks", 
            options: AssetCreationOptions.None, 
            formatOption: AssetFormatOption.AdaptiveStreaming);


## <a id="encoding_with_dotnet"></a>미디어 서비스 .NET SDK를 사용하여 인코딩

다음 코드 예제에서는 미디어 서비스 .NET SDK를 사용하여 다음 작업을 수행합니다.

- 인코딩 작업을 만듭니다.
- **Media Encoder Standard** 인코더에 대한 참조를 가져옵니다.
- 작업에 인코딩 작업(task)을 추가하고 **적응 스트리밍** 사전 설정을 사용하도록 지정합니다. 
- fMP4 청크 및 .ism 파일을 포함할 출력 자산을 만듭니다.
- 작업 진행 상태를 확인할 이벤트 처리기를 추가합니다.
- 작업을 제출합니다.

        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;

        namespace AdaptiveStreaming
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
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Get an uploaded asset.
                var asset = _context.Assets.FirstOrDefault();

                // Encode and generate the output using the "Adaptive Streaming" preset.
                EncodeToAdaptiveBitrateMP4Set(asset);

                Console.ReadLine();
            }
            static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("Media Encoder Standard Job");

                // Get a media processor reference, and pass to it the name of the 
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                // Create a task
                ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                processor,
                "Adaptive Streaming",
                TaskOptions.None);

                // Specify the input asset to be encoded.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job. 

                // This output is specified as AssetCreationOptions.None, which 
                // means the output asset is not encrypted. 
                // It is also specified to use AssetFormatOption.AdaptiveStreaming, 
                // which means the output asset will contain fMP4 chunks.

                task.OutputAssets.AddNew(@"Output Asset containing fMP4 chunks",
                    options: AssetCreationOptions.None,
                    formatOption: AssetFormatOption.AdaptiveStreaming);

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

            }

        }

## <a name="media-services-learning-paths"></a>미디어 서비스 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>참고 항목
[미디어 서비스 인코딩 개요](media-services-encode-asset.md)


