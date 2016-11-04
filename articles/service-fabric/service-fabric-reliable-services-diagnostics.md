---
title: 상태 저장 Reliable Services 진단 | Microsoft Docs
description: 상태 저장 Reliable Services의 진단 기능
services: service-fabric
documentationcenter: .net
author: AlanWarwick
manager: timlt
editor: ''

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/17/2016
ms.author: alanwar

---
# 상태 저장 Reliable Services의 진단 기능
상태 저장 Reliable Services StatefulServiceBase 클래스는 서비스를 디버그하는 데 사용할 수 있는 [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 이벤트를 내보내고, 런타임이 작동하는 방법에 대한 고급 정보를 제공하고 문제 해결에 도움을 줍니다.

## EventSource 이벤트
상태 저장 Reliable Services StatefulServiceBase 클래스의 EventSource 이름은 "Microsoft-ServiceFabric-Services"입니다. 이 이벤트 원본의 이벤트는 서비스가 [Visual Studio에서 디버깅](service-fabric-debugging-your-application.md)될 때 [진단 이벤트](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) 창에 표시됩니다.

EventSource 이벤트를 수집하거나 보는 데 도움이 되는 도구 및 기술의 예로 [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Microsoft Azure 진단](../cloud-services/cloud-services-dotnet-diagnostics.md) 및 [Microsoft TraceEvent 라이브러리](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)가 있습니다.

## 이벤트
| 이벤트 이름 | 이벤트 ID | Level | 이벤트 설명 |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |정보 제공 |서비스 RunAsync 작업이 시작되면 내보내집니다. |
| StatefulRunAsyncCancellation |2 |정보 제공 |서비스 RunAsync 작업이 취소되면 내보내집니다. |
| StatefulRunAsyncCompletion |3 |정보 제공 |서비스 RunAsync 작업이 완료되면 내보내집니다. |
| StatefulRunAsyncSlowCancellation |4 |Warning |서비스 RunAsync 작업이 취소를 완료하는 데 너무 오래 걸리는 경우 내보내집니다. |
| StatefulRunAsyncFailure |5 |오류 |서비스 RunAsync 작업이 예외를 throw하면 내보내집니다. |

## 이벤트 해석
StatefulRunAsyncInvocation, StatefulRunAsyncCompletion 및 StatefulRunAsyncCancellation 이벤트는 서비스 작성자가 서비스 시작, 취소 또는 완료 타이밍과 서비스의 수명 주기를 이해하는 데 유용합니다. 이는 서비스 문제를 디버깅하거나 서비스 수명 주기를 이해할 때 유용할 수 있습니다.

StatefulRunAsyncSlowCancellation 및 StatefulRunAsyncFailure 이벤트는 서비스와 관련된 문제를 나타내므로 서비스 작성자는 이 이벤트에 세심한 주의를 기울여야 합니다.

서비스 RunAsync() 작업이 예외를 throw할 때마다 StatefulRunAsyncFailure가 내보내집니다. 일반적으로 throw된 예외는 서비스의 오류 또는 버그를 나타냅니다. 또한 예외로 인해 서비스가 실패하므로 다른 노드로 이동시켜야 합니다. 이 작업은 부담이 큰 작업이며 서비스가 이동하는 동안 들어오는 요청을 지연시킬 수 있습니다. 서비스 작성자는 예외의 원인을 확인하고 가능한 경우 이를 완화해야 합니다.

RunAsync 작업에 대한 취소 요청이 4초보다 오래 걸릴 경우 StatefulRunAsyncSlowCancellation이 내보내집니다. 서비스 취소 완료 시간이 너무 오래 걸리는 경우 서비스가 다른 노드에서 신속하게 다시 시작하는 기능에 영향을 줄 수 있습니다. 이 작업은 서비스의 전반적인 가용성에 영향을 줄 수 있습니다.

<!---HONumber=AcomDC_0518_2016-->