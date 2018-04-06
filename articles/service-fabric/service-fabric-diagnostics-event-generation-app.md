---
title: Azure Service Fabric 응용 프로그램 수준 모니터링 | Microsoft Docs
description: Azure Service Fabric 클러스터를 모니터링 및 진단하는 데 사용되는 응용 프로그램 및 서비스 수준 이벤트와 로그에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur
ms.openlocfilehash: 258aac722aa1c94ecf2cbf0524a3e4b53b8a788c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="application-and-service-level-logging"></a>응용 프로그램 및 서비스 수준 로깅

코드를 계측하는 것은 다른 측면 대부분의 서비스 모니터링에 대한 기반이 됩니다. 계측은 무언가가 잘못되었다는 것을 알 수 있는 유일한 방법이며, 수정해야 할 것을 진단합니다. 기술적으로 디버거를 프로덕션 서비스에 연결할 수도 있지만 일반적인 방법은 아닙니다. 따라서 자세한 계측 데이터를 갖는 것이 중요합니다.

일부 제품은 자동으로 코드를 계측합니다. 이러한 솔루션이 잘 작동할 수 있지만 수동 계측이 거의 항상 필요합니다. 결국 응용 프로그램을 과학 수사 방식으로 디버그하는 데 필요한 정보가 충분히 있어야 합니다. 이 문서에서는 코드를 계측하는 여러 가지 방법, 그리고 다른 방법 대신 특정 방법을 선택해야 하는 경우에 대해 설명합니다.

## <a name="eventsource"></a>EventSource

Visual Studio의 템플릿에서 Service Fabric 솔루션을 만들면 **EventSource** 파생 클래스(**ServiceEventSource** 또는 **ActorEventSource**)가 생성됩니다. 응용 프로그램 또는 서비스에 대한 이벤트를 추가할 수 있는 템플릿을 만듭니다. **EventSource** 이름은 **고유해야** 하며 MyCompany-&lt;solution&gt;-&lt;project&gt; 기본 템플릿 문자열에서 이름을 변경해야 합니다. 동일한 이름을 사용하는 **EventSource** 정의가 여러 개 있으면 런타임에서 문제가 발생합니다. 정의된 각 이벤트에는 고유 식별자가 있어야 합니다. 식별자가 고유하지 않으면 런타임 오류가 발생합니다. 일부 조직에서는 별도의 개발 팀 간에 충돌을 피하기 위해 식별자에 대한 값의 범위가 미리 할당됩니다. 자세한 내용은 [Vance의 블로그](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) 또는 [MSDN 설명서](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx)를 참조하세요.

## <a name="aspnet-core-logging"></a>ASP.NET Core 로깅

코드를 계측하는 방법을 신중하게 계획해야 합니다. 올바른 계측 계획을 사용하면 잠재적인 코드 기반의 불안정을 피한 다음 코드를 다시 계측할 수 있습니다. 위험을 줄이기 위해 Microsoft ASP.NET Core의 일부인 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)과 같은 계측 라이브러리를 선택할 수 있습니다. ASP.NET Core에는 기존 코드에 미치는 영향을 최소화하면서 선택한 공급자와 함께 사용할 수 있는 [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) 인터페이스가 있습니다. Windows 및 Linux의 ASP.NET Core 및 전체 .NET Framework의 코드를 사용하여 계측 코드를 표준화할 수 있습니다.

## <a name="choosing-a-logging-provider"></a>로깅 공급자 선택

응용 프로그램이 높은 성능에 의존하는 경우 일반적으로 **EventSource**를 사용하는 것이 적합합니다. **EventSource**는 *일반적으로* ASP.NET Core 로깅이나 사용 가능한 타사 솔루션보다 더 적은 리소스를 사용하는 한편 더 효율적으로 수행합니다.  이는 대부분의 서비스에서 문제가 되지 않지만, 성능 지향적인 서비스인 경우 **EventSource**를 사용하는 것이 더 나은 선택일 수 있습니다. 그러나 구조적 로깅과 동일한 이점을 얻으려면 엔지니어링 팀에서 **EventSource**에 많이 투자해야 합니다. 가능하다면 신속하게 몇 가지 로깅 옵션의 프로토타입을 제작하여 요구 사항에 가장 부합하는 것을 선택합니다.

## <a name="next-steps"></a>다음 단계

응용 프로그램 및 서비스를 계측할 로깅 공급자를 선택한 후에는 분석 플랫폼으로 보낼 수 있도록 로그 및 이벤트를 집계해야 합니다. 권장 옵션을 더 잘 이해하려면 [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) 및 [WAD](service-fabric-diagnostics-event-aggregation-wad.md)를 참조하세요.
