---

title: ".NET SDK를 사용하여 스트리밍 끝점을 관리합니다. | Microsoft Docs"
description: "이 항목에서는 Azure 포털을 사용하여 스트리밍 끝점을 관리하는 방법을 설명합니다."
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: erikre
editor: 
ms.assetid: 0da34a97-f36c-48d0-8ea2-ec12584a2215
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 4f61f7c0fd50065d341ae1ce182a033395857579
ms.openlocfilehash: 68011ef634b1f3bdeb7c219a46e1307698a12f7e


---


# <a name="manage-streaming-endpoints-with-net-sdk"></a>.NET SDK를 사용하여 스트리밍 끝점 관리

>[!NOTE]
>[개요](media-services-streaming-endpoints-overview.md) 항목을 살펴보세요. 또한 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)도 살펴보세요.

이 항목의 코드는 Azure Media Services .NET SDK를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

- 기본 스트리밍 끝점을 살펴봅니다.
- 새 스트리밍 끝점을 만들거나 추가합니다.

    다른 CDN 및 직접 액세스를 사용하려는 경우 여러 스트리밍 끝점을 배치하는 것이 좋습니다.

    > [!NOTE]
    > 스트리밍 끝점이 실행 중인 상태일 때만 요금이 청구됩니다.
    
- 스트리밍 끝점을 업데이트합니다.
    
    Update() 함수를 호출해야 합니다.

- 스트리밍 끝점을 삭제합니다.

    >[!NOTE]
    >기본 스트리밍 끝점은 삭제할 수 없습니다.

스트리밍 끝점의 크기를 조정하는 방법에 대한 자세한 내용은 [이 항목](media-services-portal-scale-streaming-endpoints.md) 을 참조하세요.


###<a name="set-up-the-visual-studio-project"></a>Visual Studio 프로젝트 설정

1. Visual Studio 2015를 사용하여 새 C# 콘솔 응용 프로그램을 만듭니다.  
2. 솔루션을 빌드하십시오.
3. **NuGet**을 사용하여 [최신 Azure Media Services .NET SDK 패키지](https://www.nuget.org/packages/windowsazure.mediaservices/)를 설치합니다.   
4. .config 파일에 appSettings 섹션을 추가하고 Media Services 이름 및 키 값을 업데이트합니다. 
    
        <appSettings>
          <add key="MediaServicesAccountName" value="Media-Services-Account-Name"/>
          <add key="MediaServicesAccountKey" value="Media-Services-Account-Key"/>
        </appSettings>

###<a name="add-code-that-manages-streaming-endpoints"></a>스트리밍 끝점을 관리하는 코드 추가
    
Program.cs의 코드를 다음 코드로 바꿉니다.

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.Live;
    
    namespace AMSStreamingEndpoint
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
    
                var defaultStreamingEndpoint = _context.StreamingEndpoints.Where(s=>s.Name.Contains("default")).FirstOrDefault();
                ExamineStreamingEndpoint(defaultStreamingEndpoint);
    
                IStreamingEndpoint newStreamingEndpoint = AddStreamingEndpoint();
                UpdateStreamingEndpoint(newStreamingEndpoint);
                DeleteStreamingEndpoint(newStreamingEndpoint);
            }
    
            static public void ExamineStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                Console.WriteLine(streamingEndpoint.Name);
                Console.WriteLine(streamingEndpoint.StreamingEndpointVersion);
                Console.WriteLine(streamingEndpoint.FreeTrialEndTime);
                Console.WriteLine(streamingEndpoint.ScaleUnits);
                Console.WriteLine(streamingEndpoint.CdnProvider);
                Console.WriteLine(streamingEndpoint.CdnProfile);
                Console.WriteLine(streamingEndpoint.CdnEnabled);
            }
    
            static public IStreamingEndpoint AddStreamingEndpoint()
            {
                var name = "StreamingEndpoint" + DateTime.UtcNow.ToString("hhmmss");
                var option = new StreamingEndpointCreationOptions(name, 1)
                {
                    StreamingEndpointVersion = new Version("2.0"),
                    CdnEnabled = true,
                    CdnProfile = "CdnProfile",
                    CdnProvider = CdnProviderType.PremiumVerizon
                };
    
                var streamingEndpoint = _context.StreamingEndpoints.Create(option);
    
                return streamingEndpoint;
            }
    
            static public void UpdateStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                if(streamingEndpoint.StreamingEndpointVersion == "1.0")
                    streamingEndpoint.StreamingEndpointVersion = "2.0";
    
                streamingEndpoint.CdnEnabled = false;
                streamingEndpoint.Update();
            }
    
            static public void DeleteStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                streamingEndpoint.Delete();
            }
        }
    }
    

## <a name="next-steps"></a>다음 단계
미디어 서비스 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


