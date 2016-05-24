<properties 
   pageTitle="이벤트 허브를 사용하여 실행 부하 과다 경로에서 Azure 진단 데이터 스트리밍"
   description="일반적인 시나리오에 대한 지침을 포함하여 이벤트 허브에 Azure 진단을 완벽하게 구성하는 방법을 보여 줍니다."
   services="event-hubs"
   documentationCenter="na"
   authors="tomarcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/08/2016"
   ms.author="tarcher" />

# 이벤트 허브를 사용하여 실행 부하 과다 경로에서 Azure 진단 데이터 스트리밍

## 개요
Azure 진단에서는 계산 VM에서 메트릭 및 로그를 수집하고 Azure 저장소로 전송하는 유연한 방법을 제공합니다. 2016년 3월(SDK 2.9)부터, 현재 Azure 이벤트 허브를 사용하여 데이터 소스를 완벽하게 사용자 지정하고 몇 초 만에 실행 부하 과다 경로 데이터를 전송할 수 있는 Azure 진단 싱크 기능이 지원됩니다.

지원되는 데이터 유형은 다음과 같습니다.

- ETW 이벤트
- 성능 카운터
- Windows 이벤트 로그 
- 응용 프로그램 로그
- Azure 진단 인프라 로그
 
이 문서에서는 이벤트 허브에 Azure 진단을 구성하는 방법을 완벽하게 보여 줍니다. 또한, 이벤트 허브에 싱크되는 로그 및 메트릭 사용자 지정, 각 환경에서 구성 변경 방법, 이벤트 허브 스트림 데이터를 볼 수 있는 수많은 방법 중 하나 예는 물론, 연결 문제 해결 방법 등의 일반적인 시나리오에 대한 지침이 제공됩니다.

## 필수 조건
Azure 진단에 싱크하는 이벤트 허브는 최소 Azure SDK 2.9의 WAD와 해당 Azure Tools for Visual Studio를 지원하는 모든 계산 유형(클라우드 서비스, VM, VMSS 및 서비스 패브릭)에서 지원됩니다.
  
- Azure 진단 확장 1.6(기본적으로 [Azure SDK for .NET 2.9 이상](https://azure.microsoft.com/downloads/) 대상)
- [Visual Studio 2013 이상](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- *.wadcfgx* 파일을 사용하여 응용 프로그램에서 Azure 진단 구성을 성공적으로 구성한 이전의 예에서 다음 방법 중 하나를 사용하고 있습니다.
	- Visual Studio: [Azure 클라우드 서비스 및 가상 컴퓨터에서 진단 구성](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
	- Windows PowerShell: [PowerShell을 사용하여 Azure 클라우드 서비스에서 진단 사용](../cloud-services/cloud-services-diagnostics-powershell.md)
- [이벤트 허브 시작](./event-hubs-csharp-ephcs-getstarted.md) 문서에 따라 프로비저닝된 이벤트 허브 네임스페이스

## 이벤트 허브 싱크에 Azure 진단 연결
Azure 진단은 항상 기본적으로 Azure 저장소 계정에 로그 및 메트릭을 싱크합니다. 응용 프로그램 새로운 **싱크** 섹션을 *.wadcfgx* 파일에서 **PublicConfig** 섹션의 **WadCfg** 요소에 추가하여 이벤트 허브에 추가로 싱크할 수 있습니다. Visual Studio에서 *.wadcfgx* 파일은 *클라우드 서비스 프로젝트 > 역할 > (역할 이름) > diagnostics.wadcfgx* 파일에 저장됩니다.
  
	  <SinksConfig>
	    <Sink name="HotPath">
	      <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
	    </Sink>
	  </SinksConfig>

이 예제에서는 이벤트 허브 URL이 이벤트 허브의 정규화된 네임스페이스로 설정되어 있습니다(ServiceBus 네임스페이스 + “/” + 이벤트 허브 이름).

이벤트 허브 URL은 이벤트 허브 대시보드의 [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)에 표시됩니다.

**싱크** 이름은 같은 값이 구성 파일 전체에서 일관되게 사용되고 있다면 유효한 문자열로 설정할 수 있습니다.

**참고:** "applicationInsights"와 같이 이 섹션에 구성되어 있는 추가 싱크가 있을 수 있습니다. 각 싱크가 **PrivateConfig** 섹션에서도 선언되어 있는 경우 Azure 진단을 통해 하나 이상의 싱크를 정의할 수 있습니다.

또한, 이벤트 허브 싱크도 *.wadcfgx* 구성 파일의 **PrivateConfig** 섹션에서 선언되고 정의되어야 합니다.

	  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <StorageAccount name="" key="" endpoint="" />
	    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="9B3SwghJOGEUvXigc6zHPInl2iYxrgsKHZoy4nm9CUI=" />
	  </PrivateConfig>

**SharedAccessKeyName**은 **ServiceBus/EventHub** 네임스페이스에서 정의되었던 SAS 키와 정책과 일치해야 합니다. 이 작업은 [클래식 Azure 포털](https://manage.windowsazure.com)에서 이벤트 허브 대시보드로 이동한 후 **구성** 탭을 클릭하고 *보내기* 권한이 있는 명명된 정책(예: “SendRule”)을 설정하여 수행할 수 있습니다. 또한, **StorageAccount**도 **PrivateConfig**에서 선언되어 있습니다. 특히 작동 중인 경우에는 여기서 값을 변경할 필요가 없습니다. 이 예제에서는 다운스트림 자산이 값을 설정할 것이라는 표시로 값을 비워둡니다(예: *ServiceConfiguration.Cloud.cscfg* 환경 구성 파일은 환경에 적절한 이름과 키를 설정합니다).

>[AZURE.WARNING] 이벤트 허브 SAS 키는 *.wadcfgx* 파일에 저장되어 있습니다. 소스 코드 컨트롤 또는 빌드 서버에서 자산으로 체크인되는 경우가 있으므로 적절하게 보호해야 합니다. 악의적인 사용자가 이벤트 허브에 쓰기만 하고 수신하거나 관리하지 않을 수 있으므로 여기서 *보내기 전용* 권한으로 SAS 키를 사용하는 것이 좋습니다.

## 이벤트 허브와 싱크하기 위해 Azure 진단 로그 및 메트릭 구성
앞에서 설명한 대로 모든 기본 및 사용자 지정 진단 데이터(예: 메트릭 및 로그)는 구성된 간격으로 Azure 저장소로 자동 싱크됩니다. 이벤트 허브(및 추가 싱크)를 통해 계층 구조에서 이벤트 허브와 싱크할 루트 또는 리프 노드를 지정할 수 있는 옵션이 있습니다. 이러한 옵션에는 ETW 이벤트, 성능 카운터, Windows 이벤트 로그 및 응용 프로그램 로그가 포함되어 있습니다.

얼마나 많은 데이터 요소를 실제로 이벤트 허브로 전송해야 하는지 고려하는 것이 중요합니다. 일반적으로 개발자들은 이 옵션을 신속하게 사용하고 해석해야 하는 대기 시간이 짧은 실행 부하 과다 경로 데이터에 사용합니다(예: 경고 시스템 또는 AutoScale 규칙 모니터링을 통해). 또한, 대체 분석 또는 검색 스토어(예: 스트림 분석, ElasticSearch, 사용자 지정 모니터링 시스템 또는 즐겨찾는 타사 모니터링 시스템)을 구성하는 데 사용할 수도 있습니다.

다음은 몇 가지 구성 예입니다.

        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>

다음 예제에서는 싱크가 계층 구조에서 부모 **PerformanceCounters** 모드에 적용되며, 이것은 모든 자식 **PerformanceCounters**가 이벤트 허브로 전송된다는 것을 의미합니다.

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
        </PerformanceCounters>

위에 나열된 예제에서는 싱크가 단 세 개의 카운터(대기 중인 요청, 거부된 요청 및 % 프로세서 시간)에만 적용되었습니다.

다음 예제에서는 개발자가 전송될 데이터 양을 조절하고 이 서비스의 상태에 사용되는 중요 메트릭으로 제한하는 방법을 보여 줍니다.

        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />

이 예제에서 싱크는 로그에 적용되며 오류 슈준 추적으로만 필터링됩니다.
 
## 클라우드 서비스 응용 프로그램과 진단 구성 배포 및 업데이트

이벤트 허브 싱크 구성과 함께 아날로그 응용 프로그램을 배포하는 가장 쉬운 방법은 Visual Studio를 사용하는 것입니다. 보고 적절하게 편집하려면 Visual Studio에서 *클라우드 서비스 프로젝트 -> 역할 -> (역할 이름) -> diagnostics.wadcfgx* 파일에 저장된 *.wadcfgx* 파일을 열고, 완료되면 저장합니다.

이때 Visual Studio Team System의 모든 배포 및 배포 업데이트 작업 또는 MSBUILD 기반 명령 또는 스크립트(/t:게시 대상 사용)는 패키징 프로세스에서 *.wadcfgx*를 포함하며 VM에 상주하는 적절한 WAD 에이전트 확장을 사용하여 Azure에 배포되었는지 확인합니다.
  
응용 프로그램 및 Azure 진단 구성을 성공적으로 배포하면 이벤트 허브의 대시보드에 즉시 작업을 확인하게 됩니다. 이것은 사용자가 선택한 분석 도구 또는 수신기 클라이언트에서 실행 부하 과다 경로 데이터를 볼 준비가 되었다는 것을 나타냅니다.
 
다음 그림에서 이벤트 허브 대시보드는 해당 응용 프로그램이 업데이트된 *.wadcfgx*로 배포되었으며 싱크가 올바르게 구성된 오후 11시 후부터 시작하여 진단 데이터가 이벤트 허브로 정상 전송되었음을 보여 줍니다.

![][0]
  
>[AZURE.NOTE] Azure 진단 구성 파일(.wadcfgx)을 업데이트할 경우 이전 Visual Studio 게시 또는 Windows PowerShell 스크립트를 사용하여 구성과 함께 전체 응용 프로그램에 업데이트를 푸시하는 것이 좋습니다.

## 실행 부하 과다 경로 데이터 보기

앞에서 설명한 대로 이벤트 허브 데이터를 수신하고 처리하는 많은 사용 사례가 있습니다.
  
한 가지 간단한 접근 방법은 이벤트 허브를 수신하고 출력 스트림을 인쇄하기 위한 작은 테스트 콘솔 응용 프로그램을 만드는 것입니다. 다음 코드([이벤트 허브 시작](./event-hubs-csharp-ephcs-getstarted.md) 문서에서 더 자세히 설명되어 있음)는 콘솔 응용 프로그램에 배치될 수 있습니다.

콘솔 응용 프로그램에는 [EventProcessor Nuget 패키지](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/)가 포함되어 있어야 합니다.

아래 **기본** 함수에서 꺾쇠 괄호로 묶인 값을 리소스에 사용할 값으로 바꾸십시오.

	//Console application code for EventHub test client
	using System;
	using System.Collections.Generic;
	using System.Diagnostics;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.ServiceBus.Messaging;
	
	namespace EventHubListener
	{
	    class SimpleEventProcessor : IEventProcessor
	    {
	        Stopwatch checkpointStopWatch;
	
	        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	        {
	            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
	            if (reason == CloseReason.Shutdown)
	            {
	                await context.CheckpointAsync();
	            }
	        }
	
	        Task IEventProcessor.OpenAsync(PartitionContext context)
	        {
	            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
	            this.checkpointStopWatch = new Stopwatch();
	            this.checkpointStopWatch.Start();
	            return Task.FromResult<object>(null);
	        }
	
	        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
	        {
	            foreach (EventData eventData in messages)
	            {
	                string data = Encoding.UTF8.GetString(eventData.GetBytes());
	
	                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
	                    context.Lease.PartitionId, data));
	
	                foreach (var x in eventData.Properties)
	                {
	                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
	                }
	            }
	
	            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
	            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
	            {
	                await context.CheckpointAsync();
	                this.checkpointStopWatch.Restart();
	            }
	        }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            string eventHubConnectionString = "Endpoint= <Your Connection String>”
	            string eventHubName = "<EventHub Name>";
	            string storageAccountName = "<Storage Account Name>";
	            string storageAccountKey = "<Storage Account Key>”;
	            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
	
	            string eventProcessorHostName = Guid.NewGuid().ToString();
	            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
	            Console.WriteLine("Registering EventProcessor...");
	            var options = new EventProcessorOptions();
	            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
	            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
	
	            Console.WriteLine("Receiving. Press enter key to stop worker.");
	            Console.ReadLine();
	            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
	        }
	    }
	}

## 이벤트 허브 싱크 문제 해결

- 이벤트 허브가 들어오거나 나가는 이벤트 활동을 예상대로 표시하지 않습니다.

	이벤트 허브는 성공적으로 프로비저닝되었는지 확인하십시오. *.wadcfgx*의 **PrivateConfig** 섹션에 있는 모든 연결 정보는 포털에서 보이는 리소스의 값과 일치해야 합니다. 포털에서 SAS 정책(예에서는 “SendRule”)이 정의되어 있고 보내기 권한이 부여되어 있는지 확인하십시오.

- 업데이트를 수행한 후, 이벤트 허브에서 더 이상 들어오거나 나가는 이벤트 활동을 표시하지 않습니다.

	첫째, 이벤트 허브 및 구성 정보가 모두 위와 같이 일치합니다. 몇 가지 문제는 배포 업데이트에서 **PrivateConfig**에서 다시 설정되면서 발생합니다. 권장되는 해결 방법은 프로젝트에서 *.wadcfgx*에 모든 변경 사항을 적용한 다음 전체 응용 프로그램 업데이트를 푸시하는 것입니다. 가능하지 않으면 진단 업데이트가 SAS 키를 포함해 전체 **PrivateConfig**를 푸시하시기 바랍니다.

- 위의 방법을 시도했지만, 이벤트 허브가 여전히 작동하지 않습니다.

	Azure 진단에 대한 로그 및 오류가 포함된 Azure 저장소 테이블(**WADDiagnosticInfrastructureLogsTable**)에서 찾아 보십시오. 한 가지 옵션은 [Azure 저장소 탐색기](http://www.storageexplorer.com) 등의 도구를 사용하여 이 저장소 계정에 연결하고 이 테이블을 본 후 지난 24시간 이내의 타임스탬프에 대한 쿼리를 추가하는 것입니다. Excel에서는 어떤 오류가 보고되었는지 확인하기 위해 "EventHubs" 등의 전화 카드 문자열을 쉽게 검색할 수 있으므로, 이 도구를 사용하여 CSV를 내보낸 후 Microsoft Excel 등의 응용 프로그램에서 열 수 있습니다.

## 다음 단계
• [이벤트 허브에 대해 자세히 알아보기](https://azure.microsoft.com/services/event-hubs/)

## 부록: Azure 진단 구성 파일(.wadcfgx) 예제 완료
	<?xml version="1.0" encoding="utf-8"?>
	<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <WadCfg>
	      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
	        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
	        <Directories scheduledTransferPeriod="PT1M">
	          <IISLogs containerName="wad-iis-logfiles" />
	          <FailedRequestLogs containerName="wad-failedrequestlogs" />
	        </Directories>
	        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
	          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
	        </PerformanceCounters>
	        <WindowsEventLog scheduledTransferPeriod="PT1M">
	          <DataSource name="Application!*" />
	        </WindowsEventLog>
	        <CrashDumps>
	          <CrashDumpConfiguration processName="WaIISHost.exe" />
	          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
	          <CrashDumpConfiguration processName="w3wp.exe" />
	        </CrashDumps>
	        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
	      </DiagnosticMonitorConfiguration>
	      <SinksConfig>
	        <Sink name="HotPath">
	          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
	        </Sink>
	        <Sink name="applicationInsights">
	          <ApplicationInsights />
	          <Channels>
	            <Channel logLevel="Error" name="errors" />
	          </Channels>
	        </Sink>
	      </SinksConfig>
	    </WadCfg>
	    <StorageAccount />
	  </PublicConfig>
	  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <StorageAccount name="" key="" endpoint="" />
	    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
	  </PrivateConfig>
	  <IsEnabled>true</IsEnabled>
	</DiagnosticsConfiguration>

이 예제에 대한 보조 *ServiceConfiguration.Cloud.cscfg*는 다음과 같습니다.

	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
	  <Role name="MyFixIt.WorkerRole">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
	    </ConfigurationSettings>
	  </Role>
	</ServiceConfiguration>

<!-- Images. -->
[0]: ./media/event-hubs-streaming-azure-diags-data/dashboard.png

<!---HONumber=AcomDC_0511_2016-->