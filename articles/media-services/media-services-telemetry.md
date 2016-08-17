<properties pageTitle=".NET 포함 Azure 미디어 서비스 원격 분석 | Microsoft Azure" 
	description="이 문서에서는 Azure 미디어 서비스 원격 분석을 사용하는 방법을 보여 줍니다." 
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
	ms.date="07/27/2016"   
	ms.author="juliako"/>

# .NET 포함 Azure 미디어 서비스 원격 분석
 
## 개요

미디어 서비스 고객은 미디어 서비스 원격 분석/모니터링을 통해 서비스에 대한 메트릭 데이터에 액세스할 수 있습니다. 현재 버전에서는 "Channel" 및 "StreamingEndpoint" 엔터티에 대한 원격 분석 데이터를 지원합니다. 구성 요소 세분성 수준에서 원격 분석을 구성할 수 있습니다. "Normal" 및 "Verbose"라는 두 가지 세부 수준이 있습니다. 현재 버전은 "Normal"만 지원합니다.

원격 분석은 고객이 제공한 Azure 저장소 계정의 저장소 테이블에 기록됩니다(저장소 계정은 미디어 서비스 계정에 연결되어야 함). 원격 분석 시스템은 00:00 UTC 기반으로 각 날마다 별도의 테이블을 만듭니다. 예를 들어 "TelemetryMetrics20160321"에서 "20160321"은 테이블이 생성된 날짜입니다. 각 날짜에 대한 별도의 테이블이 있습니다.

원격 분석 시스템은 데이터 보존을 관리하지 않습니다. 저장소 테이블을 삭제하여 이전 원격 분석 데이터를 제거할 수 있습니다.

다음 방법 중 하나를 사용하여 원격 분석 데이터를 사용할 수 있습니다.

- Azure 테이블 저장소에서 직접 데이터를 읽습니다(예: 저장소 SDK 사용). 원격 분석 저장소 테이블에 대한 설명을 보려면 [이](https://msdn.microsoft.com/library/mt742089.aspx) 항목의 **원격 분석 정보 사용**을 참조하세요.

또는

- 저장소 데이터를 읽기 위한 미디어 서비스 .NET SDK의 지원을 사용합니다. 이 항목에서는 지정된 AMS 계정에 대해 원격 분석을 사용하도록 설정하고 Azure 미디어 서비스 .NET SDK를 사용하여 메트릭을 쿼리하는 방법을 보여 줍니다.

## 미디어 서비스 계정에 대해 원격 분석 구성

원격 분석을 사용하도록 설정하려면 다음 단계가 필요합니다.

- 미디어 서비스 계정에 연결된 저장소 계정의 자격 증명을 가져옵니다.
- **EndPointType**이 **AzureTable**로 설정되고 endPontAddress가 저장소 테이블을 가리키는 알림 끝점을 만듭니다.

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

## 원격 분석 정보 사용

원격 분석은 미디어 서비스 계정에 대해 원격 분석을 구성할 때 지정된 저장소 계정의 Azure Storage 테이블에 기록됩니다. 원격 분석 시스템은 00:00 UTC 기반으로 각 날마다 별도의 테이블을 만듭니다. 예를 들어 "TelemetryMetrics20160321"에서 "20160321"은 테이블이 생성된 날짜입니다. 각 날짜에 대한 별도의 테이블이 있습니다.

원격 분석 테이블에서 다음 메트릭 정보를 쿼리할 수 있습니다. 이 항목의 뒷부분에 나오는 예제에서는 미디어 서비스 .NET SDK를 사용하여 메트릭을 쿼리하는 방법을 보여 줍니다.

### StreamingEndpoint 로그

다음 StreamingEndPoint 메트릭을 쿼리할 수 있습니다.

속성|설명|샘플 값
---|---|---
**PartitionKey**|레코드의 파티션 키를 가져옵니다.|60b71b0f6a0e4d869eb0645c16d708e1\_6efed125eef44fb5b61916edc80e6e23
**RowKey**|레코드의 행 키를 가져옵니다.|00959\_00000
**AccountId**|미디어 서비스 계정 ID를 가져옵니다.|6efed125-eef4-4fb5-b619-16edc80e6e23
**StreamingEndpointId**|미디어 서비스 스트리밍 끝점 ID를 가져옵니다.|d17ec9e4-a5d4-033d-0c36-def70229f06f
**ObservedTime**|메트릭의 관찰된 시간을 가져옵니다.|1/20/16 23:44:01
**HostName**|스트리밍 끝점 호스트 이름을 가져옵니다.|builddemoserver.origin.mediaservices.windows.net
**StatusCode**|상태 코드를 가져옵니다.|200
**ResultCode**|결과 코드를 가져옵니다.|S\_OK
**RequestCount**|결과 수를 가져옵니다.|3
**BytesSent**|전송된 바이트를 가져옵니다.|2987358
**ServerLatency**|서버 대기 시간(저장소 포함)을 가져옵니다.|129
**EndToEndLatency**|종단 간 요청 시간을 가져옵니다.|250


### 라이브 채널 하트비트

다음 라이브 채널 메트릭을 쿼리할 수 있습니다.

속성|설명|샘플 값
---|---|---
**PartitionKey**|레코드의 파티션 키를 가져옵니다.|60b71b0f6a0e4d869eb0645c16d708e1\_0625cc45918e4f98acfc9a33e8066628
**RowKey**|레코드의 행 키를 가져옵니다.|13872\_00005
**AccountId**|미디어 서비스 계정 ID를 가져옵니다.|6efed125-eef4-4fb5-b619-16edc80e6e23
**ChannelId**|미디어 서비스 채널 ID를 가져옵니다.|
**ObservedTime**|메트릭의 관찰된 시간을 가져옵니다.|1/21/2016 20:08:49
**CustomAttributes**|사용자 지정 특성을 가져옵니다.|
**TrackType**|추적 형식을 가져옵니다.|video
**TrackName**|추적 이름을 가져옵니다.|video
**Bitrate**|비트 전송률을 가져옵니다.|785000
**IncomingBitrate**|들어오는 비트 전송률을 가져옵니다.|784548
**OverlapCount**|겹침 수를 가져옵니다.|0
**DiscontinuityCount**|불연속성 수를 가져옵니다.|0
**LastTimestamp**|마지막 타임스탬프를 가져옵니다.|1800488800
 
## 예  
	
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
	        // Read values from the App.config file.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	        private static readonly string _mediaServicesAccountID =
	            ConfigurationManager.AppSettings["MediaServicesAccountID"];
	        private static readonly string _streamingEndpointID =
	            ConfigurationManager.AppSettings["StreamingEndpointID"];
	        private static readonly string _mediaServicesStorageAccountName =
	            ConfigurationManager.AppSettings["StorageAccountName"];
	        private static readonly string _mediaServicesStorageAccountKey =
	            ConfigurationManager.AppSettings["StorageAccountKey"];
	
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
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some streaming endpoint metrics.
	            var res = _context.StreamingEndPointRequestLogs.GetStreamingEndPointMetrics(
	                    GetTableEndPoint(),
	                    _mediaServicesStorageAccountKey,
	                    _mediaServicesAccountID,
	                    _streamingEndpointID,
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
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some channel metrics.
	            var channelMetrics = _context.ChannelMetrics.GetChannelMetrics(
	                GetTableEndPoint(),
	                _mediaServicesStorageAccountKey,
	                _mediaServicesAccountName,
	                _context.Channels.FirstOrDefault().Id,
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


##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##다음 단계
 
AMS에서 제공하는 훌륭한 기능에 대해 자세히 알아보려면 Azure 미디어 서비스 학습 경로를 참조하세요.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

<!---HONumber=AcomDC_0803_2016-->