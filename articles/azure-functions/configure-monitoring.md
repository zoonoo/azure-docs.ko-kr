---
title: Azure Functions에 대한 모니터링 구성
description: 모니터링을 위해 함수 앱을 Application Insights에 연결하고 데이터 수집을 구성하는 방법을 알아봅니다.
ms.date: 8/31/2020
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.openlocfilehash: 5007009d9aabf9a1c1c6e1d5c2f286c0ba25b340
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493756"
---
# <a name="how-to-configure-monitoring-for-azure-functions"></a>Azure Functions에 대한 모니터링을 구성하는 방법

Azure Functions는 Application Insights와 통합되어 함수 앱을 보다 효율적으로 모니터링할 수 있습니다. Azure Monitor의 기능인 Application Insights는 앱이 로그에 쓰는 정보를 포함하여 함수 앱에 의해 생성된 데이터를 수집하는 확장 가능한 APM(애플리케이션 성능 관리) 서비스입니다. Application Insights 통합은 일반적으로 함수 앱을 만들 때 사용하도록 설정됩니다. 앱에 계측 키가 설정되어 있지 않은 경우 먼저 [Application Insights 통합을 사용하도록 설정](#enable-application-insights-integration)해야 합니다. 

사용자 지정 구성 없이 Application Insights를 사용할 수 있습니다. 기본 구성을 사용하면 대량의 데이터가 생성될 수 있습니다. Visual Studio Azure 구독을 사용하는 경우 Application Insights에 대한 데이터 제한에 도달할 수 있습니다. Application Insights 비용에 대해 자세히 알아보려면 [Application Insights 사용량 및 비용 관리](../azure-monitor/app/pricing.md)를 참조하세요.

이 문서의 뒷부분에서는 함수가 Application Insights로 보내는 데이터를 구성 및 사용자 지정하는 방법을 알아봅니다. 함수 앱의 경우 로깅은 [host.json] 파일에서 구성합니다. 

> [!NOTE]
> 특수하게 구성된 애플리케이션 설정을 사용하여 특정 환경의 host.json 파일에서 특정 설정을 나타낼 수 있습니다. 이렇게 하면 프로젝트에서 host.json 파일을 다시 게시하지 않고도 host.json 설정을 효과적으로 변경할 수 있습니다. 자세한 내용은 [host.json 값 재정의](functions-host-json.md#override-hostjson-values)를 참조하세요.

## <a name="configure-categories"></a>범주 구성

Azure Functions 로거에는 모든 로그에 대한 *범주* 가 포함되어 있습니다. 범주는 런타임 코드 또는 함수 코드의 어느 부분이 로그를 작성했는지를 나타냅니다. 버전 1.x과 이후 버전의 범주가 다릅니다. 다음 차트에는 런타임에서 만드는 주요 로그 범주가 설명되어 있습니다. 

# <a name="v2x"></a>[v2.x 이상](#tab/v2)

| 범주 | 테이블 | Description |
| ----- | ----- | ----- |
| **`Function.<YOUR_FUNCTION_NAME>`** | **종속성**| 일부 서비스의 경우 종속성 데이터가 자동으로 수집됩니다. 성공한 실행의 로그는 `Information` 수준에 있습니다. 자세한 내용은 [종속성](functions-monitoring.md#dependencies)을 참조하세요. 예외는 `Error` 수준에서 로그됩니다. 런타임은 `Warning` 수준 로그도 만들며, [포이즌 큐](functions-bindings-storage-queue-trigger.md#poison-messages)로 큐 메시지가 전송되는 경우를 예로 들 수 있습니다. | 
| **`Function.<YOUR_FUNCTION_NAME>`** | **customMetrics**<br/>**customEvents** | C# 및 JavaScript SDK를 사용하면 사용자 지정 메트릭을 수집하고 사용자 지정 이벤트를 로그할 수 있습니다. 자세히 알아보려면 [사용자 지정 원격 분석 데이터](functions-monitoring.md#custom-telemetry-data)를 참조하세요.|
| **`Function.<YOUR_FUNCTION_NAME>`** | **traces**| 특정 함수 실행의 함수 시작 및 완료 로그를 포함합니다. 성공한 실행의 로그는 `Information` 수준에 있습니다. 예외는 `Error` 수준에서 로그됩니다. 런타임은 `Warning` 수준 로그도 만들며, [포이즌 큐](functions-bindings-storage-queue-trigger.md#poison-messages)로 큐 메시지가 전송되는 경우를 예로 들 수 있습니다. | 
| **`Function.<YOUR_FUNCTION_NAME>.User`** | **traces**| 사용자 생성 로그로서 어떤 로그 수준도 될 수 있습니다. 함수에서 로그에 쓰기에 대한 자세한 내용은 [로그에 쓰기](functions-monitoring.md#writing-to-logs)를 참조하세요. | 
| **`Host.Aggregator`** | **customMetrics** | 이러한 런타임 생성 로그는 [구성 가능한](#configure-the-aggregator) 기간 동안의 함수 호출 수 및 평균을 제공합니다. 기본 기간은 30초 또는 결과 1,000개 중 먼저 도착하는 것입니다. 실행 수, 성공률 및 기간을 예로 들 수 있습니다. 이러한 로그는 모두 `Information` 수준에서 작성됩니다. `Warning` 이상에서 필터링하면 이 데이터가 표시되지 않습니다. |
| **`Host.Results`** | **requests** | 이러한 런타임 생성 로그는 함수의 성공 또는 실패를 나타냅니다. 이러한 로그는 모두 `Information` 수준에서 작성됩니다. `Warning` 이상에서 필터링하면 이 데이터가 표시되지 않습니다. |
| **`Microsoft`** | **traces** | 호스트에서 호출하는 .NET 런타임 구성 요소를 반영하는 정규화된 로그 범주입니다.  |
| **`Worker`** | **traces** | .NET이 아닌 언어의 언어 작업자 프로세스에서 생성된 로그입니다. 언어 작업자 로그는 `Microsoft.Azure.WebJobs.Script.Workers.Rpc.RpcFunctionInvocationDispatcher`와 같은 `Microsoft.*` 범주에도 로그될 수 있습니다. 이러한 로그는 `Information` 수준에서 작성됩니다.|

> [!NOTE]
> .NET 클래스 라이브러리 함수의 경우 이러한 범주는 사용자가 `ILogger<T>`가 아니라 `ILogger`를 사용한다고 가정합니다. 자세한 내용은 [Functions ILogger 설명서](functions-dotnet-class-library.md#ilogger)를 참조하세요. 

# <a name="v1x"></a>[v1.x](#tab/v1)

| 범주 | 테이블 | Description |
| ----- | ----- | ----- |
| **`Function`** | **traces**| 사용자 생성 로그로서 어떤 로그 수준도 될 수 있습니다. 함수에서 로그에 쓰기에 대한 자세한 내용은 [로그에 쓰기](functions-monitoring.md#writing-to-logs)를 참조하세요. | 
| **`Host.Aggregator`** | **customMetrics** | 이러한 런타임 생성 로그는 [구성 가능한](#configure-the-aggregator) 기간 동안의 함수 호출 수 및 평균을 제공합니다. 기본 기간은 30초 또는 결과 1,000개 중 먼저 도착하는 것입니다. 실행 수, 성공률 및 기간을 예로 들 수 있습니다. 이러한 로그는 모두 `Information` 수준에서 작성됩니다. `Warning` 이상에서 필터링하면 이 데이터가 표시되지 않습니다. |
| **`Host.Executor`** | **traces** | 특정 함수 실행의 **함수 시작** 및 **함수 완료** 로그를 포함합니다. 성공한 실행의 로그는 `Information` 수준입니다. 예외는 `Error` 수준에서 로깅됩니다. 런타임은 `Warning` 수준 로그도 만들며, [포이즌 큐](functions-bindings-storage-queue-trigger.md#poison-messages)로 큐 메시지가 전송되는 경우를 예로 들 수 있습니다.  |
| **`Host.Results`** | **requests** | 이러한 런타임 생성 로그는 함수의 성공 또는 실패를 나타냅니다. 이러한 로그는 모두 `Information` 수준에서 작성됩니다. `Warning` 이상에서 필터링하면 이 데이터가 표시되지 않습니다. |

---

**Table** 열은 로그가 기록되는 Application Insights의 테이블을 나타냅니다. 

## <a name="configure-log-levels"></a>로그 수준 구성

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

각 범주에 대해 보낼 최소 로그 수준을 나타낼 수 있습니다. host.json 설정은 [Functions 런타임 버전](functions-versions.md)에 따라 달라집니다. 

아래 예제는 다음 규칙에 따라 로깅을 정의합니다.

+ `Host.Results` 또는 `Function` 로그의 경우 `Error` 또는 상위 수준에서만 이벤트를 로그합니다. 
+ `Host.Aggregator` 로그의 경우 생성된 모든 메트릭(`Trace`)을 로그합니다.
+ 사용자 로그를 포함한 기타 모든 로그의 경우 `Information` 수준 및 상위 이벤트만 로그합니다.

# <a name="v2x"></a>[v2.x 이상](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

# <a name="v1x"></a>[v1.x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

---

[host.json]에 동일한 문자열로 시작되는 여러 로그가 포함된 경우 더 자세히 정의된 로그가 먼저 일치됩니다. 런타임에 `Host.Aggregator`를 제외하고 모든 것을 `Error` 수준에서 로그하는 다음 예제를 생각해 보세요.

# <a name="v2x"></a>[v2.x 이상](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

# <a name="v1x"></a>[v1.x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

---

`None` 로그 수준 설정을 사용하면 어떤 로그도 범주에 기록되지 않도록 할 수 있습니다. 

## <a name="configure-the-aggregator"></a>수집기 구성

이전 섹션에서 언급했듯이, 런타임은 일정 기간 동안 함수 실행에 대한 데이터를 집계합니다. 기본 기간은 30초 또는 실행 1,000개 중 먼저 도착하는 것입니다. [host.json] 파일에서 이 설정을 구성할 수 있습니다.  예를 들면 다음과 같습니다.

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>샘플링 구성

Application Insights에는 최대 부하 시 실행이 완료될 때 원격 분석 데이터를 너무 많이 생성하지 않도록 방지하는 [샘플링](../azure-monitor/app/sampling.md) 기능이 포함되어 있습니다. 들어오는 실행 비율이 지정된 임계값을 초과하면 Application Insights는 들어오는 항목 중 일부를 임의로 무시하기 시작합니다. 초당 최대 실행 수의 기본 설정은 20입니다(1.x 버전은 5). [host.json](./functions-host-json.md#applicationinsights)에서 샘플링을 구성할 수 있습니다.  예를 들면 다음과 같습니다.

# <a name="v2x"></a>[v2.x 이상](#tab/v2)

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request;Exception"
      }
    }
  }
}
```


샘플링에서 특정 유형의 원격 분석을 제외할 수 있습니다. 이 예제에서 `Request` 및 `Exception` 형식의 데이터는 샘플링에서 제외됩니다. 이렇게 하면 모든 함수 실행(요청)과 예외를 로그하면서 다른 형식의 원격 분석은 여전히 샘플링되도록 할 수 있습니다. 

# <a name="v1x"></a>[v1.x](#tab/v1)  

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

자세한 내용은 [Application Insights의 샘플링](../azure-monitor/app/sampling.md)을 참조하세요.

## <a name="configure-scale-controller-logs"></a>스케일링 컨트롤러 로그 구성

‘이 기능은 미리 보기로 제공됩니다.’ 

[Azure Functions 스케일링 컨트롤러](./event-driven-scaling.md#runtime-scaling)가 로그를 Application Insights 또는 Blob 스토리지로 내보내도록 하면 스케일링 컨트롤러가 함수 앱을 위해 내리는 결정을 더 잘 이해할 수 있습니다.

이 기능을 사용하도록 설정하려면 `SCALE_CONTROLLER_LOGGING_ENABLED`라는 애플리케이션 설정을 함수 앱 설정에 추가합니다. 이 설정의 값은 다음을 기반으로 하는 `<DESTINATION>:<VERBOSITY>` 형식이어야 합니다.

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

예를 들어 다음 Azure CLI 명령은 스케일링 컨트롤러에서 Application Insights로 자세한 정보 로깅을 설정합니다.

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

이 예제에서 `<FUNCTION_APP_NAME>` 및 `<RESOURCE_GROUP_NAME>`을 각각 함수 앱의 이름과 리소스 그룹의 이름으로 바꿉니다. 

다음 Azure CLI 명령은 자세한 정도를 `None`으로 설정하여 로깅을 사용하지 않도록 설정합니다.

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

다음 Azure CLI 명령으로 `SCALE_CONTROLLER_LOGGING_ENABLED` 설정을 제거하여 로깅을 사용하지 않도록 설정할 수도 있습니다.

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

스케일링 컨트롤러 로깅을 사용하도록 설정하면 [스케일링 컨트롤러 로그를 쿼리](analyze-telemetry-data.md#query-scale-controller-logs)할 수 있습니다. 

## <a name="enable-application-insights-integration"></a>Application Insights 통합 사용

함수 앱이 Application Insights로 데이터를 보내려면 Application Insights 리소스의 계측 키를 알고 있어야 합니다. 이 키는 **APPINSIGHTS_INSTRUMENTATIONKEY** 라는 앱 설정에 있어야 합니다.

[Azure Portal](./functions-get-started.md)의 명령줄에서 [Azure Functions Core Tools](./create-first-function-cli-csharp.md)를 사용하여 또는 [Visual Studio Code](./create-first-function-vs-code-csharp.md)를 사용하여 함수 앱을 만들 때 Application Insights 통합이 기본적으로 사용하도록 설정됩니다. Application Insights 리소스는 함수 앱과 동일한 이름을 가지며, 동일한 지역 또는 가장 가까운 지역에 생성됩니다.

### <a name="new-function-app-in-the-portal"></a>포털의 새 함수 앱

생성되는 Application Insights 리소스를 검토하려면 해당 리소스를 선택하여 **Application Insights** 창을 확장합니다. **새 리소스 이름** 을 변경하거나 데이터를 저장하려는 [Azure 지리적 위치](https://azure.microsoft.com/global-infrastructure/geographies/)에서 다른 **위치** 를 선택합니다.

![함수 앱을 만들 때 Application Insights 사용](media/functions-monitoring/enable-ai-new-function-app.png)

**만들기** 를 선택하면 함수 앱을 사용하여 Application Insights 리소스가 생성되고, 이 리소스는 애플리케이션 설정에서 `APPINSIGHTS_INSTRUMENTATIONKEY`로 설정됩니다. 모든 준비가 끝났습니다.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>기존 함수 앱에 추가 

함수 앱을 사용하여 Application Insights 리소스를 만들지 않은 경우 다음 단계를 사용하여 리소스를 만듭니다. 그런 다음, 함수 앱에서 해당 리소스의 계측 키를[애플리케이션 설정](functions-how-to-use-azure-function-app-settings.md#settings)으로 추가할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 **함수 앱** 을 검색하고 선택한 다음 함수 앱을 선택합니다. 

1. 창 상단에 있는 **Application Insights가 구성되지 않음** 배너를 선택합니다. 이 배너가 표시되지 않으면 앱이 이미 Application Insights를 사용하도록 설정되어 있을 수 있습니다.

    :::image type="content" source="media/configure-monitoring/enable-application-insights.png" alt-text="포털에서 Application Insights를 사용하도록 설정":::

1. **리소스 변경** 을 펼치고 다음 표에 지정된 설정을 사용하여 Application Insights 리소스를 만듭니다.  

    | 설정      | 제안 값  | 설명                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **새 리소스 이름** | 고유한 앱 이름 | 함수 앱과 동일한 이름을 사용하는 것이 가장 간단하며, 구독 내에서 고유해야 합니다. | 
    | **위치** | 서유럽 | 가능하다면 함수 앱과 동일한 [지역](https://azure.microsoft.com/regions/)을 사용하거나 해당 지역에 근접한 지역을 사용합니다. |

    :::image type="content" source="media/configure-monitoring/ai-general.png" alt-text="Application Insights 리소스 만들기":::

1. **적용** 을 선택합니다. 

   함수 앱과 동일한 리소스 그룹 및 구독에 Application Insights 리소스가 만들어집니다. 리소스를 만든 후 Application Insights 창을 닫습니다.

1. 함수 앱의 **설정** 에서 **구성** 을 선택한 다음, **애플리케이션 설정** 을 선택합니다. `APPINSIGHTS_INSTRUMENTATIONKEY`라는 설정이 표시되면 Azure에서 실행 중인 함수 앱에 대해 Application Insights 통합이 활성화됩니다. 어떤 이유로 이 설정이 존재하지 않는 경우 Application Insights 계측 키를 값으로 사용하여 추가합니다.

> [!NOTE]
> Functions 초기 버전에서는 기본 제공 모니터링을 사용했지만, 더 이상 권장하지 않습니다. 또한 이러한 함수 앱에 Application Insights 통합을 사용하도록 설정할 때 [기본 제공 로깅을 사용하지 않도록 설정](#disable-built-in-logging)해야 합니다.  

## <a name="disable-built-in-logging"></a>기본 제공 로깅을 사용하지 않도록 설정

Application Insight를 사용하도록 설정하는 경우 Azure Storage를 사용하는 기본 제공 로깅을 사용하지 않도록 설정하세요. 기본 제공 로깅은 가벼운 워크로드를 테스트할 때 유용하지만, 부하가 높은 프로덕션 용도로는 적합하지 않습니다. 프로덕션 모니터링에는 Application Insights를 사용하는 것이 좋습니다. 프로덕션에 기본 제공 로깅을 사용하면 Azure Storage의 제한으로 인해 로깅 레코드가 불완전할 수 있습니다.

기본 제공 로깅을 사용하지 않도록 설정하려면 `AzureWebJobsDashboard` 앱 설정을 삭제합니다. Azure Portal에서 앱 설정을 삭제하는 방법에 대한 자세한 내용은 [함수 앱을 관리하는 방법](functions-how-to-use-azure-function-app-settings.md#settings)의 **애플리케이션 설정** 섹션을 참조하세요. 앱 설정을 삭제하기 전에, 동일한 함수 앱의 기존 함수 중 Azure Storage 트리거 또는 바인딩에 해당 설정을 사용하는 함수가 없는지 확인합니다.

## <a name="next-steps"></a>다음 단계

모니터링에 대한 자세한 내용은 다음을 참조하세요.

+ [Azure Functions 모니터링](functions-monitoring.md)
+ [Application Insights에서 Azure Functions 원격 분석 데이터 분석](analyze-telemetry-data.md)
+ [Application Insights](/azure/application-insights/)


[host.json]: functions-host-json.md
