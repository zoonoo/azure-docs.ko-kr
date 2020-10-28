---
title: 빠른 시작 - 로그, 메트릭 및 추적을 사용하여 Azure Spring Cloud 앱 모니터링
description: 로그 스트리밍, 로그 분석, 메트릭 및 추적을 사용하여 Azure Spring Cloud에서 Piggymetrics 샘플 앱을 모니터링합니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 2af9608006eac37ccdfe76d66e619d5a64cad9f1
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746642"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>빠른 시작: 로그, 메트릭 및 추적을 사용하여 Azure Spring Cloud 앱 모니터링

::: zone pivot="programming-language-csharp"
Azure Spring Cloud의 기본 제공 모니터링 기능을 사용하여 복잡한 문제를 디버그하고 모니터링할 수 있습니다. Azure Spring Cloud는 Steeltoe [분산 추적](https://steeltoe.io/docs/3/tracing/distributed-tracing)을 Azure의 [Application Insights](../azure-monitor/app/app-insights-overview.md)와 통합합니다. 이 통합은 Azure Portal에서 강력한 로그, 메트릭 및 분산 추적 기능을 제공합니다.

다음 절차에서는 이전 빠른 시작에서 배포한 샘플 앱에서 로그 스트리밍, Log Analytics, 메트릭 및 분산 추적을 사용하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 이 시리즈의 이전 빠른 시작을 완료합니다.

  * [Azure Spring Cloud 서비스 프로비저닝](spring-cloud-quickstart-provision-service-instance.md)
  * [Azure Spring Cloud 구성 서버 설정](spring-cloud-quickstart-setup-config-server.md)
  * [앱을 빌드 및 배포](spring-cloud-quickstart-deploy-apps.md)합니다.

## <a name="logs"></a>로그

Azure Spring Cloud에 대한 로그를 확인하는 방법에는 두 가지가 있습니다. 앱 인스턴스당 실시간 로그의 **로그 스트리밍** 또는 고급 쿼리 기능이 있는 집계 로그의 **Log Analytics** .

### <a name="log-streaming"></a>스트리밍 로그

다음 명령을 사용하여 Azure CLI에서 로그 스트리밍을 사용할 수 있습니다.

```azurecli
az spring-cloud app logs -n solar-system-weather -f
```

다음 예제와 비슷한 출력이 표시됩니다.

```output
=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing action method Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather) - Validation state: Valid
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController[0]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Retrieved weather data from 4 planets
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing ObjectResult, writing value of type 'System.Collections.Generic.KeyValuePair`2[[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e],[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e]][]'.
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
```

> [!TIP]
> `az spring-cloud app logs -h`를 사용하여 더 많은 매개 변수 및 로그 스트림 기능을 탐색합니다.

### <a name="log-analytics"></a>Log Analytics

1. Azure Portal에서 **서비스 | 개요** 페이지로 이동하여 **모니터링** 섹션에서 **로그** 를 선택합니다. Azure Spring Cloud에 대한 샘플 쿼리 중 하나에서 **실행** 을 선택합니다.

   [ ![Logs Analytics 항목](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. 경고 및 오류 로그에 대한 표시를 제한하는 Where 절을 제거하도록 쿼리를 편집합니다.

1. 그런 다음, `Run`을 선택하면 로그가 표시됩니다. 쿼리 작성에 대한 자세한 지침은 [Azure Log Analytics 문서](../azure-monitor/log-query/get-started-queries.md)를 참조하세요.

   [ ![Logs Analytics 쿼리 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png#lightbox)

## <a name="metrics"></a>메트릭

1. Azure Portal에서 **서비스 | 개요** 페이지로 이동하여 **모니터링** 섹션에서 **메트릭** 을 선택합니다. **메트릭** 드롭다운의 **성능(.NET)** 또는 **요청(.NET** )에서 .NET 메트릭 중 하나를 선택하여 첫 번째 메트릭을 추가하고 해당 메트릭에 대한 타임라인을 확인하려면 **집계** 의 `Avg`를 선택합니다.

   [ ![메트릭 항목 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png#lightbox)
    
1. 도구 모음에서 **필터 추가** 를 클릭하고, `App=solar-system-weather`를 선택하여 **solar-system-weather** 앱에 대해서만 CPU 사용량을 확인합니다.

   [ ![메트릭에서 필터 사용 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png#lightbox)

1. 이전 단계에서 만든 필터를 해제하여 **분할 적용** 을 선택하고, **값** 에 대해 `App`을 선택하여 다른 앱의 CPU 사용량을 확인합니다.

   [ ![메트릭에 분할 적용 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png#lightbox)

## <a name="distributed-tracing"></a>분산된 추적

1. Azure Portal에서 **서비스 | 개요** 페이지로 이동하여 **모니터링** 섹션에서 **분산 추적** 을 선택합니다. 그런 다음, 오른쪽의 있는 **애플리케이션 맵 보기** 탭을 선택합니다.

   [ ![분산 추적 항목 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. 이제 앱 간의 호출 상태를 볼 수 있습니다. 

   [ ![분산 추적 개요 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png#lightbox)
    
1. **solar-system-weather** 와 **planet-weather-provider** 사이의 링크를 선택하여 HTTP 메서드에 의한 가장 느린 호출과 같은 자세한 정보를 확인합니다.

   [![ 분산 추적 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png#lightbox)
    
1. 마지막으로, **성능** 조사를 선택하여 더 강력한 기본 제공 성능 분석을 탐색합니다.

   [ ![분산 추적 성능 - Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Azure Spring Cloud의 기본 제공 모니터링 기능을 사용하여 복잡한 문제를 디버그하고 모니터링할 수 있습니다. Azure Spring Cloud는 [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth)를 Azure의 [Application Insights](../azure-monitor/app/app-insights-overview.md)와 통합합니다. 이 통합은 Azure Portal에서 강력한 로그, 메트릭 및 분산 추적 기능을 제공합니다. 다음 절차에서는 배포된 PiggyMetrics 앱에서 로그 스트리밍, Log Analytics, 메트릭 및 분산 추적을 사용하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

이전 단계를 완료합니다. 

* [Azure Spring Cloud의 인스턴스 프로비저닝](spring-cloud-quickstart-provision-service-instance.md)
* [구성 서버 설정](spring-cloud-quickstart-setup-config-server.md)
* [앱을 빌드 및 배포](spring-cloud-quickstart-deploy-apps.md)합니다.

## <a name="logs"></a>로그

Azure Spring Cloud에 대한 로그를 확인하는 방법에는 두 가지가 있습니다. 앱 인스턴스당 실시간 로그의 **로그 스트리밍** 또는 고급 쿼리 기능이 있는 집계 로그의 **Log Analytics** .

### <a name="log-streaming"></a>스트리밍 로그

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

다음 명령을 사용하여 Azure CLI에서 로그 스트리밍을 사용할 수 있습니다.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

다음과 같은 로그가 표시됩니다.

[ ![Azure CLI에서 로그 스트리밍](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> `az spring-cloud app logs -h`를 사용하여 더 많은 매개 변수 및 로그 스트림 기능을 탐색합니다.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Azure Toolkit for IntelliJ를 사용하여 로그를 가져오려면 다음을 수행합니다.

1. **Azure Explorer** 를 선택한 다음, **Spring Cloud** 를 선택합니다.

1. 실행 중인 앱을 마우스 오른쪽 단추로 클릭합니다.

1. 드롭다운 목록에서 **스트리밍 로그** 를 선택합니다.

   ![스트리밍 로그 선택](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. **인스턴스** 를 선택합니다.

   ![인스턴스 선택](media/spring-cloud-intellij-howto/select-instance.png)
    
1. 스트리밍 로그는 출력 창에 표시됩니다.

   ![스트리밍 로그 출력](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. **서비스 | 개요** 페이지로 이동하여 **모니터링** 섹션에서 **로그** 를 선택합니다. Azure Spring Cloud에 대한 샘플 쿼리 중 하나에서 **실행** 을 클릭합니다. 

   [ ![Logs Analytics 항목](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. 그러면 필터링된 로그가 표시됩니다. 쿼리 작성에 대한 자세한 지침은 [Azure Log Analytics 문서](../azure-monitor/log-query/get-started-queries.md)를 참조하세요.

   [ ![Logs Analytics 쿼리](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>메트릭

1. **서비스 | 개요** 페이지로 이동하여 **모니터링** 섹션에서 **메트릭** 을 선택합니다. 전체 CPU 사용량에 대한 타임라인을 보려면 **메트릭** 에 대해 `system.cpu.usage`를 선택하고 **집계** 에 대해 `Avg`를 선택하여 첫 번째 메트릭을 추가합니다.

   [ ![메트릭 항목](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. 위의 도구 모음에서 **필터 추가** 를 클릭하고, `App=Gateway`를 선택하여 **게이트웨이** 앱에 대해서만 CPU 사용량을 확인합니다.

   [ ![메트릭에서 필터 사용](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. 위에서 만든 필터를 해제하여 **분할 적용** 을 클릭하고, **값** 에 대해 `App`을 선택하여 다른 앱의 CPU 사용량을 확인합니다.

   [ ![메트릭에 분할 적용](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>분산된 추적

1. **서비스 | 개요** 페이지로 이동하여 **모니터링** 섹션에서 **분산 추적** 을 선택합니다. 그런 다음, 오른쪽의 있는 **애플리케이션 맵 보기** 탭을 클릭합니다.

   [ ![분산 추적 항목](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. 이제 Piggymetrics 앱 간의 호출 상태를 볼 수 있습니다. 

   [![ 분산 추적 개요](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. **게이트웨이** 와 **계정 서비스** 간의 링크를 클릭하여 HTTP 메서드에 의한 가장 느린 호출과 같은 자세한 정보를 확인합니다.

   [ ![ 분산 추적](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. 마지막으로, **성능** 조사를 클릭하여 더 강력한 기본 제공 성능 분석을 탐색합니다.

   [ ![분산 추적 성능](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

이러한 빠른 시작에서는 구독에 남아있는 경우 요금이 계속 청구되는 Azure 리소스를 만들었습니다. 나중에 이러한 리소스가 필요하지 않을 것으로 예상되는 경우 포털을 사용하여 리소스 그룹을 삭제하거나 Cloud Shell에서 다음 명령을 실행하여 리소스 그룹을 삭제합니다.

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

이전 빠른 시작에서는 기본 리소스 그룹 이름도 설정합니다. 다음 빠른 시작을 계속하지 않으려면 다음 CLI 명령을 실행하여 기본값을 지웁니다.

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>다음 단계

Azure Spring Cloud의 추가 모니터링 기능을 탐색하려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [진단 서비스](diagnostic-services.md)
>
> [분산된 추적](spring-cloud-tutorial-distributed-tracing.md)
>
> [실시간으로 로그 스트리밍](spring-cloud-howto-log-streaming.md)
