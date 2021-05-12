---
title: Azure Spring Cloud에서 Application Insights Java In-Process 에이전트를 사용하는 방법
description: Azure Spring Cloud에서 Application Insights Java In-Process 에이전트를 사용하여 앱 및 마이크로 서비스를 모니터링하는 방법입니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: f6f1ed1a3e09397a720dfd4d842b79cd88aa738d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878556"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Azure Spring Cloud의 Application Insights Java In-Process 에이전트(미리 보기)

이 문서는 Azure Spring Cloud의 Application Insights Java 에이전트를 사용하여 앱 및 마이크로 서비스를 모니터링하는 방법을 설명합니다. 

이 기능을 사용하면 다음을 수행할 수 있습니다.

* 다양한 필터로 추적 데이터 검색
* 마이크로 서비스의 종속성 맵을 봅니다.
* 요청 성능을 확인합니다.
* 실시간 라이브 메트릭을 모니터링합니다.
* 요청 실패를 확인합니다.
* 애플리케이션 메트릭을 확인합니다.

Application Insights는 다음을 포함하여 관찰 가능한 여러 큐브 뷰를 제공합니다.

* 애플리케이션 맵
* 성능
* 오류
* 메트릭
* 라이브 메트릭
* 가용성

## <a name="enable-java-in-process-agent-for-application-insights"></a>Application Insights에 대한 Java In-Process 에이전트 사용

다음 절차를 사용하여 Java In-Process Agent 미리 보기 기능을 사용하도록 설정합니다.

1. 서비스 인스턴스의 서비스 개요 페이지로 이동합니다.
2. 모니터링 블레이드 아래에서 **Application Insights** 항목을 클릭합니다.
3. **Application Insights 사용 설정** 단추를 클릭하여 **Application Insights** 통합을 사용하도록 설정합니다.
4. Application Insights의 기존 인스턴스를 선택하거나 새 인스턴스를 만듭니다.
5. **Java In Process 에이전트 사용 설정** 을 확인하여 Java In Process 에이전트 미리 보기 기능을 사용하도록 설정합니다. 여기에서 샘플링 주기를 0에서 100으로 사용자 지정할 수도 있습니다.
6.  **저장** 을 클릭하여 변경 내용을 저장합니다.

## <a name="portal"></a>포털

1. **서비스 | 개요** 페이지로 이동하여 **모니터링** 섹션에서 **Application Insights** 를 선택합니다. 
2. **Application Insights 사용 설정** 을 클릭하여 Azure Spring Cloud에서 Application Insights를 사용하도록 설정합니다.
3. **Java In Process 에이전트 사용 설정** 을 클릭하여 Java IPA 미리 보기 기능을 사용하도록 설정합니다. IPA 미리 보기 기능을 사용 설정할 경우 선택적 샘플링 주기(기본값 10.0%)를 구성할 수 있습니다.

  [ ![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Application Insights 기능 사용

**Application Insights** 기능이 사용 설정된 경우 다음을 수행할 수 있습니다.

왼쪽 탐색 창에서 **Application Insights** 를 클릭하여 Application Insights의 **개요** 페이지로 이동합니다. 

* **애플리케이션 맵** 을 클릭하여 애플리케이션 간의 호출 상태를 확인합니다.

  [ ![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* 고객-서비스 간 링크와 `petclinic`을 클릭하여 SQL의 쿼리와 같은 자세한 정보를 확인합니다.

* 왼쪽 탐색 창에서 **성능** 을 클릭하여 모든 애플리케이션의 작업과 종속성 및 역할의 성능 데이터를 확인합니다.

  [ ![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* 왼쪽 탐색 창에서 **실패** 를 클릭하여 애플리케이션에서 예기치 않은 항목이 있는지 확인합니다.

  [ ![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* 왼쪽 탐색 창에서 **메트릭** 을 클릭하고 네임스페이스를 선택하면 Spring Boot 메트릭과 사용자 지정 메트릭(있는 경우)이 모두 표시됩니다.

  [ ![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* 왼쪽 탐색 창에서 **라이브 메트릭** 을 클릭하여 여러 차원에 대한 실시간 메트릭을 확인합니다.

  [ ![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* 왼쪽 탐색 창에서 **가용성** 을 클릭하여 [Application Insights의 가용성 테스트](../azure-monitor/app/monitor-web-app-availability.md)를 만들고 웹앱의 가용성 및 응답성을 모니터링합니다.

  [ ![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>ARM 템플릿
Azure Resource Manager 템플릿을 사용하려면 다음 콘텐츠를 `azuredeploy.json`에 복사합니다.

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
CLI 명령을 사용하여 ARM 템플릿을 적용합니다.

* 기존 Azure Spring Cloud 인스턴스의 경우:

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" –name "assignedName" –resource-group "resourceGroupName"
```
* 새로 만든 Azure Spring Cloud 인스턴스의 경우:

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" –resource-group "resourceGroupName"
```
* 앱 인사이트를 사용하지 않도록 설정하려면:

```azurecli
az spring-cloud app-insights update --disable –name "assignedName" –resource-group "resourceGroupName"

```

## <a name="see-also"></a>참고 항목
* [Azure Spring Cloud에서 분산 추적 사용](spring-cloud-howto-distributed-tracing.md)
* [로그 및 메트릭 분석](diagnostic-services.md)
* [실시간으로 로그 스트리밍](spring-cloud-howto-log-streaming.md)