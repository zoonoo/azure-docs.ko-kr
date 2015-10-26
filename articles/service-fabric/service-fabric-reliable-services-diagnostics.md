<properties
   pageTitle="상태 저장 신뢰할 수 있는 서비스 진단"
   description="상태 저장 신뢰할 수 있는 서비스의 진단 기능"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="alanwar"/>

# 상태 저장 신뢰할 수 있는 서비스의 진단 기능
상태 저장 신뢰할 수 있는 서비스 StatefulServiceBase 클래스는 서비스 디버깅에 사용할 수 있는 [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 이벤트를 내보내고, 런타임이 작동하는 방법에 대한 고급 정보를 제공하고 문제 해결에 도움을 줍니다.

## EventSource 이벤트
상태 저장 신뢰할 수 있는 서비스 StatefulServiceBase 클래스의 EventSource 이름은 "Microsoft-ServiceFabric-Services"입니다. 이 이벤트 원본의 이벤트는 서비스가 [Visual Studio에서 디버깅](service-fabric-debugging-your-application.md)될 때 [진단 이벤트](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) 창에 표시됩니다.

또한 패브릭 서비스는 이러한 이벤트를 [Application Insights](http://azure.microsoft.com/services/application-insights/)로 전달하는 옵션도 제공합니다. 이에 자세한 내용은 [서비스 패브릭을 위한 Application Insights 설정](service-fabric-diagnostics-application-insights-setup.md)을 참조하세요.

EventSource 이벤트를 수집하거나 보는 데 도움이 되는 다른 도구 및 기술의 예에는 [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Microsoft Azure 진단](../cloud-services-dotnet-diagnostics.md) 및 [Microsoft TraceEvent 라이브러리](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)가 있습니다.

## 이벤트

|이벤트 이름|이벤트 ID|Level|이벤트 설명|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|정보 제공|서비스 RunAsync 작업이 시작되면 내보내집니다.|
|StatefulRunAsyncCancellation|2|정보 제공|서비스 RunAsync 작업이 취소되면 내보내집니다.|
|StatefulRunAsyncCompletion|3|정보 제공|서비스 RunAsync 작업이 완료되면 내보내집니다.|
|StatefulRunAsyncSlowCancellation|4|Warning|서비스 RunAsync 작업이 취소를 완료하는 데 너무 오래 걸리는 경우 내보내집니다.|
|StatefulRunAsyncFailure|5|오류|서비스 RunAsync 작업이 예외를 throw하면 내보내집니다.|

## 이벤트 해석

StatefulRunAsyncInvocation, StatefulRunAsyncCompletion 및 StatefulRunAsyncCancellation 이벤트는 서비스 작성자가 서비스 시작, 취소 및 완료 타이밍과 서비스의 수명 주기를 이해하는 데 유용합니다. 이는 서비스 문제를 디버깅하거나 서비스 수명 주기를 이해할 때 유용할 수 있습니다.

서비스 작성자는 서비스와 관련된 문제를 나타낼 때 StatefulRunAsyncSlowCancellation 및 StatefulRunAsyncFailure 이벤트에 세심한 주의를 기울여야 합니다.

서비스 RunAsync() 작업이 예외를 throw할 때마다 StatefulRunAsyncFailure가 내보내집니다. 일반적으로 throw된 예외는 서비스의 오류 또는 버그를 나타냅니다. 또한 예외는 서비스에 오류를 발생시켜 다른 노드로 이동하므로 비용이 많이 드는 작업이 되고 서비스를 이동하는 동안 수신 요청이 지연될 수 있습니다. 서비스 작성자는 예외의 원인을 확인하고 가능한 경우 이를 완화해야 합니다.

RunAsync 작업에 대한 취소 요청이 4초보다 오래 걸릴 경우 StatefulRunAsyncSlowCancellation이 내보내집니다. 서비스 취소 완료 시간이 너무 오래 걸리는 경우 서비스가 다른 노드에서 신속하게 다시 시작하는 기능에 영향을 미치고 서비스의 전반적인 가용성에 영향을 줄 수 있습니다.

<!---HONumber=Oct15_HO3-->