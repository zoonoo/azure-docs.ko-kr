---
title: Azure 스프링 클라우드에서 Application Insights Java In-Process 에이전트를 사용 하는 방법
description: Azure 스프링 클라우드의 Application Insights Java In-Process 에이전트를 사용 하 여 앱 및 마이크로 서비스를 모니터링 하는 방법입니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: c4871c3de8028eec1b6184c1d03ac2180b50f57d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881353"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Azure 스프링 클라우드의 Application Insights Java In-Process 에이전트 (미리 보기)

이 문서에서는 Azure 스프링 클라우드의 Application Insights Java 에이전트를 사용 하 여 앱 및 마이크로 서비스를 모니터링 하는 방법을 설명 합니다. 

이 기능을 사용 하 여 다음을 수행할 수 있습니다.

* 다양한 필터로 추적 데이터 검색
* 마이크로 서비스의 종속성 지도를 봅니다.
* 요청 성능을 확인 합니다.
* 실시간 라이브 메트릭을 모니터링 합니다.
* 요청 실패를 확인 합니다.
* 응용 프로그램 메트릭을 확인 합니다.

다음을 포함 하 여 다양 한 관찰 가능한 큐브 뷰를 제공 Application Insights 합니다.

* 애플리케이션 맵
* 성능
* 오류
* 메트릭
* 라이브 메트릭
* 가용성

## <a name="enable-java-in-process-agent-for-application-insights"></a>Application Insights에 대해 Java In-Process 에이전트 사용

다음 절차를 사용 하 여 Java In-Process Agent 미리 보기 기능을 사용 하도록 설정 합니다.

1. 서비스 인스턴스의 서비스 개요 페이지로 이동 합니다.
2. 모니터링 블레이드 아래에서 **Application Insights** 항목을 클릭 합니다.
3. **Application Insights 사용** 단추를 클릭 하 여 **Application Insights** 통합을 사용 하도록 설정 합니다.
4. Application Insights의 기존 인스턴스를 선택 하거나 새 인스턴스를 만듭니다.
5. 병아리 java in-process 에이전트에서 Java in-process 에이전트 미리 보기 기능을 사용 하도록 **설정** 합니다. 여기에서 샘플링 주기를 0에서 100으로 사용자 지정할 수도 있습니다.
6.  **저장** 을 클릭하여 변경 내용을 저장합니다.

## <a name="portal"></a>포털

1. **서비스로 이동 | 개요** 페이지를 선택 하 고 **모니터링** 섹션에서 **Application Insights** 을 선택 합니다. 
2. **Application Insights 사용** 을 클릭 하 여 Azure 스프링 클라우드에서 Application Insights를 사용 하도록 설정 합니다.
3. Java **in process Agent 사용** 을 클릭 하 여 java IPA 미리 보기 기능을 사용 하도록 설정 합니다. IPA 미리 보기 기능을 사용 하는 경우 선택적 샘플링 주기 (기본값 10.0%)를 구성할 수 있습니다.

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Application Insights 기능 사용

**Application Insights** 기능을 사용 하는 경우 다음을 수행할 수 있습니다.

왼쪽 탐색 창에서 **Application Insights** 을 클릭 하 여 Application Insights의 **개요** 페이지로 이동 합니다. 

* **응용 프로그램 맵** 을 클릭 하 여 응용 프로그램 간의 호출 상태를 확인 합니다.

  [![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* 고객-서비스 간의 링크를 클릭 `petclinic` 하 여 SQL의 쿼리와 같은 자세한 정보를 확인 합니다.

* 왼쪽 탐색 창에서 **성능** 을 클릭 하 여 모든 응용 프로그램의 작업 및 종속성 및 역할의 성능 데이터를 확인 합니다.

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* 왼쪽 탐색 창에서 **실패** 를 클릭 하 여 응용 프로그램에서 예기치 않은 항목이 있는지 확인 합니다.

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* 왼쪽 탐색 창에서 **메트릭** 을 클릭 하 고 네임 스페이스를 선택 하면 스프링 부팅 메트릭과 사용자 지정 메트릭 (있는 경우)이 모두 표시 됩니다.

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* 왼쪽 탐색 창에서 **라이브 메트릭** 을 클릭 하 여 여러 차원에 대 한 실시간 메트릭을 확인 합니다.

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* 왼쪽 탐색 창에서 **사용** 가능을 클릭 하 여 [Application Insights에서 가용성 테스트](../azure-monitor/app/monitor-web-app-availability.md)를 만들어 웹 앱의 가용성 및 응답성을 모니터링 합니다.

  [![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>ARM 템플릿
Azure Resource Manager 템플릿을 사용 하려면 다음 콘텐츠를에 복사 `azuredeploy.json` 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

## <a name="cli"></a>CLI
CLI 명령을 사용 하 여 ARM 템플릿을 적용 합니다.

* 기존 Azure 스프링 클라우드 인스턴스의 경우:

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" –name "assignedName" –resource-group "resourceGroupName"
```
* 새로 만든 Azure 스프링 클라우드 인스턴스의 경우:

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" –resource-group "resourceGroupName"
```
* 앱 정보를 사용 하지 않도록 설정 하려면:

```azurecli
az spring-cloud app-insights update --disable –name "assignedName" –resource-group "resourceGroupName"

```

## <a name="see-also"></a>추가 정보
* [Azure Spring Cloud에서 분산 추적 사용](spring-cloud-tutorial-distributed-tracing.md)
* [로그 및 메트릭 분석](diagnostic-services.md)
* [실시간으로 로그 스트리밍](spring-cloud-howto-log-streaming.md)