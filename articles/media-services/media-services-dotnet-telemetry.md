---
title: ".NET을 사용하여 Azure Media Services 원격 분석 구성 | Microsoft Docs"
description: "이 문서는 .NET SDK를 사용하여 Azure Media Services 원격 분석을 사용하는 방법을 보여 줍니다."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: f8f55e37-0714-49ea-bf4a-e6c1319bec44
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: d693bc0de2f8a03d67b346f3b2d4693284ae4d71
ms.openlocfilehash: da7d4f87fdcaca6517fa95152d42c138533772b9


---

# <a name="configuring-azure-media-services-telemetry-with-net"></a>.NET을 사용하여 Azure Media Services 원격 분석 구성

이 항목은 .NET SDK를 사용하여 Azure Media Services(AMS) 원격 분석을 구성할 때 수행할 수 있는 일반적인 단계를 설명합니다. 

>[!NOTE]
>AMS 원격 분석이 무엇인지, 어떻게 사용하는지에 대한 자세한 설명을 보려면 [개요](media-services-telemetry-overview.md) 항목을 참조하세요.

다음 방법 중 하나를 사용하여 원격 분석 데이터를 사용할 수 있습니다.

- Azure 테이블 저장소에서 직접 데이터를 읽습니다(예: 저장소 SDK 사용). 원격 분석 저장소 테이블에 대한 설명을 보려면 **이** 항목의 [원격 분석 정보 사용](https://msdn.microsoft.com/library/mt742089.aspx) 을 참조하세요.

또는

- 저장소 데이터를 읽기 위한 미디어 서비스 .NET SDK의 지원을 사용합니다. 이 항목에서는 지정된 AMS 계정에 대해 원격 분석을 사용하도록 설정하고 Azure 미디어 서비스 .NET SDK를 사용하여 메트릭을 쿼리하는 방법을 보여 줍니다.  

## <a name="configuring-telemetry-for-a-media-services-account"></a>미디어 서비스 계정에 대해 원격 분석 구성

원격 분석을 사용하도록 설정하려면 다음 단계가 필요합니다.

- 미디어 서비스 계정에 연결된 저장소 계정의 자격 증명을 가져옵니다. 
- **EndPointType**이 **AzureTable**로 설정되고 endPointAddress가 저장소 테이블을 가리키는 알림 끝점을 만듭니다.

        INotificationEndPoint notificationEndPoint = 
                      _context.NotificationEndPoints.Create("monitoring", 
                      NotificationEndPointType.AzureTable,
                      "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/");

- 모니터링할 서비스에 대한 모니터링 구성 설정을 만듭니다. 한 개 이하의 모니터링 구성 설정이 허용됩니다. 
  
        IMonitoringConfiguration monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
            new List<ComponentMonitoringSetting>()
            {
                new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
            });

## <a name="consuming-telemetry-information"></a>이

원격 분석 정보 사용에 대한 자세한 내용은 [이](media-services-telemetry-overview.md) 항목을 참조하세요.
 
## <a name="example"></a>예  
    
이 섹션에 표시된 예제에서는 App.config 파일에 다음 섹션이 포함되어 있다고 가정합니다.
    
    <appSettings>
      <add key="MediaServicesAccountName" value="ams_acct_name" />
      <add key="MediaServicesAccountKey" value="ams_acct_key" />
      <add key="MediaServicesAccountID" value="ams_acct_id" />
      <add key="StorageAccountName" value="storage_name" />
      <add key="StorageAccountKey" value="storage_key" />
    </appSettings>

다음 예제에서는 지정된 AMS 계정에 대해 원격 분석을 사용하도록 설정하고 Azure 미디어 서비스 .NET SDK를 사용하여 메트릭을 쿼리하는 방법을 보여 줍니다.  

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    
    namespace AMSMetrics
    {
        class Program
        {
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
            private static readonly string _mediaServicesAccountID =
                ConfigurationManager.AppSettings["MediaServicesAccountID"];
            private static readonly string _mediaServicesStorageAccountName =
                ConfigurationManager.AppSettings["StorageAccountName"];
            private static readonly string _mediaServicesStorageAccountKey =
                ConfigurationManager.AppSettings["StorageAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
            private static IStreamingEndpoint _streamingEndpoint = null;
            private static IChannel _channel = null;
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                _streamingEndpoint = _context.StreamingEndpoints.FirstOrDefault();
                _channel = _context.Channels.FirstOrDefault();
    
                var monitoringConfigurations = _context.MonitoringConfigurations;
                IMonitoringConfiguration monitoringConfiguration = null;
    
                // No more than one monitoring configuration settings is allowed.
                if (monitoringConfigurations.ToArray().Length != 0)
                {
                    monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
                }
                else
                {
                    INotificationEndPoint notificationEndPoint =
                                  _context.NotificationEndPoints.Create("monitoring",
                                  NotificationEndPointType.AzureTable, GetTableEndPoint());
    
                    monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
                        new List<ComponentMonitoringSetting>()
                        {
                            new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                            new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
    
                        });
                }
    
                //Print metrics for a Streaming Endpoint.
                PrintStreamingEndpointMetrics();
    
                Console.ReadLine();
            }
    
            private static string GetTableEndPoint()
            {
                return "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/";
            }
    
            private static void PrintStreamingEndpointMetrics()
            {
                Console.WriteLine(string.Format("Telemetry for streaming endpoint '{0}'", _streamingEndpoint.Name));
    
                var end = DateTime.UtcNow;
                var start = DateTime.UtcNow.AddHours(-5);
    
                // Get some streaming endpoint metrics.
                var res = _context.StreamingEndPointRequestLogs.GetStreamingEndPointMetrics(
                        GetTableEndPoint(),
                        _mediaServicesStorageAccountKey,
                        _mediaServicesAccountID,
                        _streamingEndpoint.Id,
                        start,
                        end);
    
    
                Console.Title = "Streaming endpoint metrics:";
    
                foreach (var log in res)
                {
                    Console.WriteLine("AccountId: {0}", log.AccountId);
                    Console.WriteLine("BytesSent: {0}", log.BytesSent);
                    Console.WriteLine("EndToEndLatency: {0}", log.EndToEndLatency);
                    Console.WriteLine("HostName: {0}", log.HostName);
                    Console.WriteLine("ObservedTime: {0}", log.ObservedTime);
                    Console.WriteLine("PartitionKey: {0}", log.PartitionKey);
                    Console.WriteLine("RequestCount: {0}", log.RequestCount);
                    Console.WriteLine("ResultCode: {0}", log.ResultCode);
                    Console.WriteLine("RowKey: {0}", log.RowKey);
                    Console.WriteLine("ServerLatency: {0}", log.ServerLatency);
                    Console.WriteLine("StatusCode: {0}", log.StatusCode);
                    Console.WriteLine("StreamingEndpointId: {0}", log.StreamingEndpointId);
                    Console.WriteLine();
                }
    
                Console.WriteLine();
            }
    
            private static void PrintChannelMetrics()
            {
                if (_channel == null)
                {
                    Console.WriteLine("There are no channels in this AMS account");
                    return;
                }
    
                Console.WriteLine(string.Format("Telemetry for channel '{0}'", _channel.Name));
    
                var end = DateTime.UtcNow;
                var start = DateTime.UtcNow.AddHours(-5);
    
                // Get some channel metrics.
                var channelMetrics = _context.ChannelMetrics.GetChannelMetrics(
                    GetTableEndPoint(),
                    _mediaServicesStorageAccountKey,
                    _mediaServicesAccountID,
                   _channel.Id,
                    start,
                    end);
    
                // Print the channel metrics.
                Console.WriteLine("Channel metrics:");
    
                foreach (var channelHeartbeat in channelMetrics.OrderBy(x => x.ObservedTime))
                {
                    Console.WriteLine(
                        "    Observed time: {0}, Last timestamp: {1}, Incoming bitrate: {2}",
                        channelHeartbeat.ObservedTime,
                        channelHeartbeat.LastTimestamp,
                        channelHeartbeat.IncomingBitrate);
                }
    
                Console.WriteLine();
            }
        }
    }


## <a name="next-steps"></a>다음 단계

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


<!--HONumber=Nov16_HO5-->


