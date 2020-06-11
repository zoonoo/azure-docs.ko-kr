---
title: Application Insights를 사용하여 Azure Functions에서 실행되는 애플리케이션 모니터링 - Azure Monitor | Microsoft Docs
description: Azure Monitor는 Azure Functions에서 실행되는 애플리케이션과 원활하게 통합되며, 성능을 모니터링하고 앱 관련 문제를 즉시 확인할 수 있습니다.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/20/2020
ms.openlocfilehash: a936c77abb9aed5886fae8b2ec4a10bb076b7cb5
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83776299"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>Azure Monitor Application Insights를 사용하여 Azure Functions 모니터링

[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)는 함수를 모니터링할 수 있도록 Azure Application Insights와 기본적으로 통합됩니다. 

Application Insights는 로그, 성능 및 오류 데이터를 수집하고 성능 이상을 자동으로 감지합니다. Application Insights에는 문제를 진단하고 함수가 어떻게 사용되는지 이해할 수 있는 강력한 분석 도구가 있습니다. 애플리케이션 데이터에 대한 가시성이 있으면 지속적으로 성능 및 유용성을 향상시킬 수 있습니다. 로컬 함수 앱 프로젝트를 개발할 때도 Application Insights를 사용할 수 있습니다. 

필요한 Application Insights 계측은 Azure Functions에 기본 제공됩니다. 유효한 계측 키만 있으면 함수 앱을 Application Insights 리소스에 연결할 수 있습니다. Azure에서 함수 앱 리소스를 만들 때 계측 키를 애플리케이션 설정에 추가해야 합니다. 함수 앱에 이 키가 아직 없는 경우 수동으로 설정할 수 있습니다. 자세한 내용은 [Azure Functions 모니터링](https://docs.microsoft.com/azure/azure-functions/functions-monitoring?tabs=cmd)을 참조하세요.

## <a name="distributed-tracing-for-java-applications-public-preview"></a>Java 애플리케이션에 대한 분산 추적(공개 미리 보기)


> [!IMPORTANT]
> 이 기능은 현재 Java Azure Functions에 대한 공개 미리 보기로 제공됩니다. 사용 계획에서 콜드 부팅 시간은 8-9초입니다.

애플리케이션을 Java로 작성하는 경우, 함수 애플리케이션에서 요청, 종속성, 로그 및 메트릭 등 다양한 데이터를 볼 수 있습니다. 추가 데이터를 사용하여 엔드투엔드 트랜잭션을 보고 진단하고, 애플리케이션 맵도 볼 수 있습니다. 여기에는 다수의 트랜잭션을 집계하여 시스템이 어떻게 상호 작용하는지에 대한 토폴로지 보기가 표시되고 평균 성능 및 오류율이 얼마인지를 보여 줍니다.

엔드투엔드 진단 및 애플리케이션 맵은 단일 트랜잭션/요청에 대한 가시성을 제공합니다. 이러한 두 기능을 함께 사용할 경우 요청 단위를 기준으로 안정성 문제와 성능 병목 현상의 근본 원인을 찾는 데 매우 유용합니다.

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>Java 함수 앱에 대해 분산 추적을 사용하도록 설정하는 방법

함수 앱 개요 블레이드의 구성으로 이동합니다. 애플리케이션 설정에서 "+ 새 애플리케이션 설정"을 클릭합니다. 다음 두 가지 애플리케이션 설정을 아래 값으로 추가하고 왼쪽 위에서 저장을 클릭합니다. 완료!

```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

## <a name="next-steps"></a>다음 단계

* [Azure Functions 모니터링](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)에서 모니터링에 대한 자세한 지침 및 내용 확인
* [분산 추적](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)의 개요 확인
* 비즈니스를 위해 수행할 수 있는 [애플리케이션 맵](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) 보기
* [Java 앱에 대한 요청 및 종속성](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) 검토
* [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 및 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)에 대해 자세히 알아보기