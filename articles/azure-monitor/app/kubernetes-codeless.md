---
title: Application Insights를 사용하여 AKS(Azure Kubernetes Service)에서 애플리케이션 모니터링 - Azure Monitor | Microsoft Docs
description: Azure Monitor는 Kubernetes에서 실행되는 애플리케이션과 원활하게 통합되며, 앱 문제를 즉시 찾을 수 있습니다.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 76f9f922697ef6be6c959ea7f9bafd0872dba6ff
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773766"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Kubernetes에 대한 제로 계측 애플리케이션 모니터링 - Azure Monitor Application Insights

> [!IMPORTANT]
>  현재 코드를 계측하지 않고 Kubernetes에서 실행되는 Java 앱에 대한 모니터링을 사용하도록 설정할 수 있습니다. [Java 독립 실행형 에이전트](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)를 사용합니다. 애플리케이션 모니터링을 원활하게 활성화하는 솔루션은 다른 언어에서도 작동하지만 SDK를 사용하여 AKS에서 실행되는 앱을 모니터링합니다. [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core), [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net), [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs), [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript) 및 [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

## <a name="application-monitoring-without-instrumenting-the-code"></a>코드를 계측하지 않고 애플리케이션 모니터링
현재 Java만 사용하면 코드를 계측하지 않고도 애플리케이션 모니터링을 사용하도록 설정할 수 있습니다. 다른 언어로 애플리케이션을 모니터링하려면 SDK를 사용합니다. 

## <a name="java"></a>Java
활성화되면 Java 에이전트는 가장 널리 사용되는 라이브러리 및 프레임워크에서 다양한 요청, 종속성, 로그 및 메트릭을 자동으로 수집합니다.

[자세한 지침](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)에 따라 Kubernetes 앱 및 기타 환경에서 실행되는 Java 앱을 모니터링합니다. 

## <a name="other-languages"></a>기타 언어

다른 언어로 된 애플리케이션의 경우 현재 SKD를 사용하는 것이 좋습니다.
* [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
* [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
* [Node.JS](https://docs.microsoft.com/azure/azure-monitor/app/nodejs) 
* [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
* [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

## <a name="next-steps"></a>다음 단계

* [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 및 [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)에 대해 자세히 알아보기
* [분산 추적](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)의 개요를 가져오고 비즈니스에 대해 [애플리케이션 맵](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net)이 수행할 수 있는 작업을 확인합니다.