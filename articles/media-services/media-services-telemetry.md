<properties pageTitle=".NET 포함 Azure 미디어 서비스 원격 분석 | Microsoft Azure" 
	description="이 문서에서는 Azure 미디어 서비스 원격 분석을 사용하는 방법을 보여 줍니다." 
	services="" 
	documentationCenter=""
	authors="juliako" />

# .NET 포함 Azure 미디어 서비스 원격 분석

## 개요

미디어 서비스 고객은 미디어 서비스 원격 분석/모니터링을 통해 서비스에 대한 메트릭 데이터에 액세스할 수 있습니다. 현재 버전에서는 "Channel" 및 "StreamingEndpoint" 엔터티에 대한 원격 분석 데이터를 지원합니다. 구성 요소 세분성 수준에서 원격 분석을 구성할 수 있습니다. "Normal" 및 "Verbose"라는 두 가지 세부 수준이 있습니다. 현재 버전은 "Normal"만 지원합니다.

원격 분석은 고객이 제공한 Azure 저장소 계정에 기록됩니다(저장소 계정은 미디어 서비스 계정에 연결되어야 함). 원격 분석은 지정된 저장소 계정의 Azure 저장소 테이블에 기록됩니다. 원격 분석 시스템은 00:00 UTC 기반으로 각 날마다 별도의 테이블을 만듭니다. 예를 들어 "TelemetryMetrics20160321"에서 "20160321"은 테이블이 생성된 날짜입니다. 각 날짜에 대한 별도의 테이블이 있습니다.

원격 분석 시스템에서는 데이터 보존 및 이전 레코드의 자동 삭제를 둘다 제공하지 않습니다. 따라서 이전 레코드를 관리하고 삭제해야 합니다. 각 날짜에 대해 별도의 테이블을 보유하므로 이전 레코드를 쉽게 삭제할 수 있습니다. 이전 테이블을 삭제하기만 하면 됩니다.

이 항목에서는 지정된 AMS 서비스에 대해 원격 분석을 사용하도록 설정하고 .NET을 사용하여 메트릭을 쿼리하는 방법을 보여 줍니다.

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


## StreamingEndpoint 로그

###사용 가능한 메트릭

다음 StreamingEndPoint 메트릭을 쿼리할 수 있습니다.

- **PartitionKey**는 레코드의 파티션 키를 가져옵니다.
- **RowKey**는 레코드의 행 키를 가져옵니다.
- **AccountId**는 미디어 서비스 계정 ID를 가져옵니다.
- **AccountId**는 미디어 서비스 스트리밍 끝점 ID를 가져옵니다.
- **ObservedTime**은 메트릭의 관찰된 시간을 가져옵니다.
- **HostName**은 스트리밍 끝점 호스트 이름을 가져옵니다.
- **StatusCode**는 상태 코드를 가져옵니다.
- **ResultCode**는 결과 코드를 가져옵니다.
- **RequestCount**는 요청 수를 가져옵니다.
- **BytesSent**는 전송된 바이트를 가져옵니다.
- **ServerLatency**는 서버 대기 시간을 가져옵니다.
- **EndToEndLatency**는 종단 간 요청 시간을 가져옵니다.

###스트리밍 끝점 쿼리 결과 예

![스트리밍 끝점 쿼리](media/media-services-telemetry/media-services-telemetry01.png)


## 라이브 채널 하트비트

###사용 가능한 메트릭

다음 라이브 채널 메트릭을 쿼리할 수 있습니다.

- **PartitionKey**는 레코드의 파티션 키를 가져옵니다.
- **RowKey**는 레코드의 행 키를 가져옵니다.
- **AccountId**는 미디어 서비스 계정 ID를 가져옵니다.
- **ChannelId**는 미디어 서비스 채널 ID를 가져옵니다.
- **ObservedTime**은 메트릭의 관찰된 시간을 가져옵니다.
- **CustomAttributes**는 사용자 지정 특성을 가져옵니다.
- **TrackType**은 트랙 유형을 가져옵니다.
- **TrackName**은 트랙 이름을 가져옵니다.
- **Bitrate**는 비트 전송률을 가져옵니다.
- **IncomingBitrate**는 들어오는 비트 전송률을 가져옵니다.
- **OverlapCount**는 겹침 수를 가져옵니다.
- **DiscontinuityCount**는 불연속성 수를 가져옵니다.
- **LastTimestamp**는 마지막 타임스탬프를 가져옵니다.
 
###라이브 채널 쿼리 결과 예

![스트리밍 끝점 쿼리](media/media-services-telemetry/media-services-telemetry01.png)

## StreamingEndpoint 메트릭 예
		
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
	
	            INotificationEndPoint notificationEndPoint = 
	                          _context.NotificationEndPoints.Create("monitoring", NotificationEndPointType.AzureTable, GetTableEndPoint());
	
	            var monitoringConfigurations = _context.MonitoringConfigurations;
	            IMonitoringConfiguration monitoringConfiguration = null;
	
	            // No more than one monitoring configuration settings is allowed.
	            if (monitoringConfigurations.ToArray().Length != 0)
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
	            }
	            else
	            {
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

<!---HONumber=AcomDC_0720_2016-->