---
title: Azure 함수에서 소비 계획 비용 예측
description: Azure의 소비 계획에서 함수 앱을 실행할 때 발생할 수 있는 비용을 더 잘 예측하는 방법에 대해 알아봅니다.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 0e3177d7c65eb1624441427f123e6f95095bdbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963991"
---
# <a name="estimating-consumption-plan-costs"></a>소비 계획 비용 예측

현재 Azure Functions에서 실행되는 앱에 대한 세 가지 유형의 호스팅 계획이 있으며 각 계획에는 고유한 가격 책정 모델이 있습니다. 

| 계획 | 설명 |
| ---- | ----------- |
| [**소비**](functions-scale.md#consumption-plan) | 함수 앱이 실행되는 시간에 대해서만 요금이 청구됩니다. 이 플랜에는 구독별로 [무료 교부금][가격 페이지가] 포함되어 있습니다.|
| [**프리미엄**](functions-scale.md#premium-plan) | 소비 계획과 동일한 기능 및 크기 조정 메커니즘을 제공하지만 향상된 성능 및 VNET 액세스를 제공합니다. 비용은 선택한 가격 책정 계층을 기준으로 합니다. 자세한 내용은 [Azure Functions 프리미엄 계획을](functions-premium-plan.md)참조하십시오. |
| [**전용 (앱 서비스)**](functions-scale.md#app-service-plan) <br/>(기본 계층 이상) | 전용 VM에서 실행하거나 격리하여 실행해야 하는 경우 사용자 지정 이미지를 사용하거나 초과 앱 서비스 계획 용량을 사용하려는 경우 [일반 앱 서비스 요금제 청구를](https://azure.microsoft.com/pricing/details/app-service/)사용합니다. 비용은 선택한 가격 책정 계층을 기준으로 합니다.|

기능 성능 및 비용 요구 사항을 가장 잘 지원하는 계획을 선택했습니다. 자세한 내용은 [Azure Functions 크기 조정 및 호스팅](functions-scale.md)을 참조하세요.

이 문서는 소비 계획만 처리합니다. 이 문서에서는 소비 [계획 비용 청구 FAQ](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) 문서를 대체합니다.

지속형 함수는 소비 계획에서도 실행할 수 있습니다. 지속형 함수를 사용할 때 비용 고려 사항에 대해 자세히 알아보려면 [지속 함수 청구를](./durable/durable-functions-billing.md)참조하십시오.

## <a name="consumption-plan-costs"></a>소비 계획 비용

단일 함수 실행의 실행 *비용은* *GB 초*단위로 측정됩니다. 실행 비용은 메모리 사용량과 실행 시간을 결합하여 계산됩니다. 더 많은 메모리를 소비하는 함수와 마찬가지로 더 오래 실행되는 함수는 더 많은 비용이 듭니다. 

함수에서 사용하는 메모리 양이 일정하게 유지되는 경우를 고려합니다. 이 경우 비용을 계산하는 것은 간단한 곱셈입니다. 예를 들어 함수가 3초 동안 0.5GB를 소비했다고 가정해 보겠습니다. 그런 다음 실행 `0.5GB * 3s = 1.5 GB-seconds`비용은 . 

메모리 사용량은 시간이 지남에 따라 변경되므로 계산은 기본적으로 시간이 지남에 따라 메모리 사용량의 통합입니다.  시스템은 정기적으로 프로세스의 메모리 사용량(하위 프로세스와 함께)을 샘플링하여 이 계산을 수행합니다. 가격 책정 [페이지에서]설명한 대로 메모리 사용량은 가장 가까운 128MB 버킷으로 반올림됩니다. 프로세스에서 160MB를 사용하는 경우 256MB의 요금이 부과됩니다. 계산은 동일한 프로세스에서 여러 동시 함수 실행인 동시성을 고려합니다.

> [!NOTE]
> CPU 사용량은 실행 비용으로 직접 고려되지는 않지만 함수의 실행 시간에 영향을 미치는 경우 비용에 영향을 미칠 수 있습니다.

## <a name="other-related-costs"></a>기타 관련 비용

모든 계획에서 함수를 실행하는 데 드는 전체 비용을 추정할 때 함수 런타임은 각각 별도로 청구되는 여러 다른 Azure 서비스를 사용한다는 것을 기억하십시오. 함수 앱에 대한 가격 책정을 계산할 때 다른 Azure 서비스와 통합된 모든 트리거 및 바인딩을 만들려면 해당 추가 서비스를 만들고 비용을 지불해야 합니다. 

소비 계획에서 실행되는 함수의 경우 총 비용은 함수의 실행 비용과 대역폭 및 추가 서비스 비용입니다. 

함수 앱 및 관련 서비스의 전체 비용을 추정할 때 [Azure 가격 계산기를](https://azure.microsoft.com/pricing/calculator/?service=functions)사용합니다. 

| 관련 비용 | 설명 |
| ------------ | ----------- |
| **Storage 계정** | 각 함수 앱에는 [별도로 청구되는](https://azure.microsoft.com/pricing/details/storage/)연결된 범용 [Azure Storage 계정이](../storage/common/storage-introduction.md#types-of-storage-accounts)있어야 합니다. 이 계정은 함수 런타임에서 내부적으로 사용되지만 저장소 트리거 및 바인딩에도 사용할 수 있습니다. 저장소 계정이 없는 경우 함수 앱을 만들 때 저장소 계정이 만들어집니다. 자세한 내용은 [저장소 계정 요구 사항을](storage-considerations.md#storage-account-requirements)참조하십시오.|
| **Application Insights** | 함수는 응용 [프로그램 인사이트를](../azure-monitor/app/app-insights-overview.md) 사용하여 기능 앱에 대한 고성능 모니터링 환경을 제공합니다. 필수는 아니지만 [응용 프로그램 인사이트 통합을 사용하도록 설정해야](functions-monitoring.md#enable-application-insights-integration)합니다. 매월 원격 분석 데이터의 무료 부여가 포함됩니다. 자세한 내용은 [Azure 모니터 가격 페이지를](https://azure.microsoft.com/pricing/details/monitor/)참조하십시오. |
| **네트워크 대역폭** | 동일한 리전의 Azure 서비스 간에 데이터 전송에 대한 비용을 지불하지 않습니다. 그러나 다른 지역 또는 Azure 외부로의 아웃바운드 데이터 전송에 대한 비용이 발생할 수 있습니다. 자세한 내용은 [대역폭 가격 세부 정보를](https://azure.microsoft.com/pricing/details/bandwidth/)참조하십시오. |

## <a name="behaviors-affecting-execution-time"></a>실행 시간에 영향을 미치는 동작

함수의 다음 동작은 실행 시간에 영향을 미칠 수 있습니다.

+ **트리거 및 바인딩:** 함수 바인딩에 대한 입력을 읽고 출력을 쓰는 데 걸린 시간은 실행 시간으로 [계산됩니다.](functions-triggers-bindings.md) 예를 들어 함수가 출력 바인딩을 사용하여 Azure 저장소 큐에 메시지를 작성하는 경우 실행 시간에는 함수 비용 계산에 포함된 큐에 메시지를 작성하는 데 걸린 시간이 포함됩니다. 

+ **비동기 실행**: 함수가 비동기 요청(C#)의`await` 결과를 기다리는 시간은 실행 시간으로 계산됩니다. GB 초 계산은 함수의 시작 및 종료 시간과 해당 기간 동안의 메모리 사용량을 기반으로 합니다. CPU 활동 측면에서 그 시간 동안 일어나는 일은 계산에 반영되지 않습니다. [지속형 함수를](durable/durable-functions-overview.md)사용하여 비동기 작업 중에 비용을 절감할 수 있습니다. 오케스트레이터 함수에서 대기하는 데 소요된 시간에 대한 요금이 청구되지 않습니다.

## <a name="view-execution-data"></a>실행 데이터 보기

[송장에서](/azure/billing/billing-download-azure-invoice) **총 실행(함수** 및 **실행 시간 - 함수)의**비용 관련 데이터와 실제 청구 비용을 볼 수 있습니다. 그러나 이 송장 데이터는 과거 송장 기간에 대한 월별 집계입니다. 

함수의 비용 영향을 더 잘 이해하려면 Azure Monitor를 사용하여 현재 함수 앱에서 생성되는 비용 관련 메트릭을 볼 수 있습니다. [Azure Portal에서] [Azure Monitor 메트릭 탐색기를](../azure-monitor/platform/metrics-getting-started.md) 사용하거나 REST API를 사용하여 이 데이터를 얻을 수 있습니다.

### <a name="monitor-metrics-explorer"></a>메트릭 탐색기 모니터링

[Azure Monitor 메트릭 탐색기를](../azure-monitor/platform/metrics-getting-started.md) 사용하여 소비 계획 기능 앱에 대한 비용 관련 데이터를 그래픽 형식으로 볼 수 있습니다. 

1. 검색 서비스, 리소스 및 문서에서 Azure [포털] 맨 위에 `monitor` 있는 **서비스**에서 **모니터링을** **검색하고** 선택합니다.

1. 왼쪽에서 메트릭 **리소스** > **선택을**선택한 다음 이미지 아래의 설정을 사용하여 함수 앱을 선택합니다.

    ![함수 앱 리소스 선택](media/functions-consumption-costing/select-a-resource.png)

      
    |설정  |제안 값  |설명  |
    |---------|---------|---------|
    | Subscription    |  사용자의 구독  | 함수 앱의 구독입니다.  |
    | Resource group     | 리소스 그룹  | 함수 앱을 포함하는 리소스 그룹입니다.   |
    | 리소스 유형     |  App Services | 함수 앱은 모니터의 앱 서비스 인스턴스로 표시됩니다. |
    | 리소스     |  함수 앱  | 모니터링 할 기능 응용 프로그램.        |

1. 응용 프로그램을 모니터링할 리소스로 선택하려면 **적용을** 선택합니다.

1. **메트릭에서** **집계에** **대한**함수 실행 수 및 **합계를** 선택합니다. 이렇게 하면 선택한 기간 동안의 실행 횟수합계가 차트에 추가됩니다.

    ![차트에 추가할 함수 앱 메트릭 정의](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. **메트릭 추가및** 반복 단계 2-4를 선택하여 **함수 실행 단위를** 차트에 추가합니다. 

결과 차트에는 선택한 시간 범위의 두 실행 메트릭에 대한 합계가 포함되며, 이 경우 2시간입니다.

![함수 실행 횟수 및 실행 단위 그래프](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

실행 단위 수가 실행 수보다 훨씬 많기 때문에 차트에는 실행 단위만 표시됩니다.

이 차트는 MB-밀리초단위로 `Function Execution Units` 측정된 2시간 동안 총 11억 1천만 소비를 보여줍니다. GB 초로 변환하려면 1024000으로 나눕니다. 이 예제에서는 함수 앱에서 GB 초를 소비했습니다. `1110000000 / 1024000 = 1083.98` 이 값을 받아 [함수 가격 책정 페이지]가격 책정[페이지에서]현재 실행 시간 가격을 곱할 수 있으며, 이미 실행 시간의 무료 부여를 사용했다고 가정하면 이 두 시간의 비용을 제공합니다. 

### <a name="azure-cli"></a>Azure CLI

[Azure CLI에는](/cli/azure/) 메트릭을 검색하는 명령이 있습니다. 로컬 명령 환경에서 또는 [Azure Cloud Shell을](../cloud-shell/overview.md)사용하여 포털에서 직접 CLI를 사용할 수 있습니다. 예를 들어 다음 [az 모니터 메트릭 목록](/cli/azure/monitor/metrics#az-monitor-metrics-list) 명령은 이전에 사용된 동일한 기간 동안 시간별 데이터를 반환합니다.

명령을 실행하는 `<AZURE_SUBSCRIPTON_ID>` Azure 구독 ID로 바꿔야 합니다.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

이 명령은 다음과 같은 JSON 페이로드를 반환합니다.

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
이 특정 응답은 `2019-09-11T21:46` `2019-09-11T23:18`앱에서 1110000000MB-밀리초(1083.98GB 초)를 소비했다는 것을 보여줍니다.

## <a name="determine-memory-usage"></a>메모리 사용량 결정

함수 실행 단위는 실행 시간과 메모리 사용량의 조합으로 메모리 사용량을 이해하기가 어렵습니다. 메모리 데이터는 Azure Monitor를 통해 현재 사용할 수 있는 메트릭이 아닙니다. 그러나 앱의 메모리 사용량을 최적화하려는 경우 Application Insights에서 수집한 성능 카운터 데이터를 사용할 수 있습니다.  

아직 수행하지 않은 경우 [함수 앱에서 응용 프로그램 인사이트를 사용하도록 설정합니다.](functions-monitoring.md#enable-application-insights-integration) 이 통합을 사용하도록 설정하면 [포털에서 이 원격 분석 데이터를 쿼리할](functions-monitoring.md#query-telemetry-data)수 있습니다.  

**모니터링에서** **로그(분석)를**선택한 다음 다음 원격 분석 쿼리를 복사하여 쿼리 창에 붙여넣고 **실행을**선택합니다. 이 쿼리는 샘플링된 각 시간의 총 메모리 사용량을 반환합니다.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

결과는 다음과 같습니다.

| 타임스탬프 \[UTC\]          | name          | value       |
|----------------------------|---------------|-------------|
| 2019/9/12, 오전 1:05:14\.947 | 프라이빗 바이트 | 209,932,288 |
| 2019년 9월 12일, 오전 1:06:14\.994 | 프라이빗 바이트 | 212,189,184 |
| 2019년 9월 12일, 오전 1:06:30\.010 | 프라이빗 바이트 | 231,714,816 |
| 2019년 9월 12일, 오전 1:07:15\.040 | 프라이빗 바이트 | 210,591,744 |
| 2019년 9월 12일 오전 1:12:16\.285 | 프라이빗 바이트 | 216,285,184 |
| 2019년 9월 12일, 오전 1:12:31\.376 | 프라이빗 바이트 | 235,806,720 |

## <a name="function-level-metrics"></a>기능 수준 메트릭

Azure Monitor는 함수에 대한 함수 앱인 리소스 수준에서 메트릭을 추적합니다. 애플리케이션 인사이트 통합은 기능별로 메트릭을 방출합니다. 다음은 함수의 평균 기간을 얻기 위한 예제 분석 쿼리입니다.

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | 평균지속밀리초 |
|----------------------------|-----------------------------|
| 큐트리거 평균 기간 | 16\.087                     |
| 큐트리거 최대 지속 시간 | 90\.249                     |
| 큐트리거 최소 지속 시간 | 8\.522                      |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기능 앱 모니터링에 대해 자세히 알아보기](functions-monitoring.md)

[가격 책정 페이지]:https://azure.microsoft.com/pricing/details/functions/
[Azure 포털]: https://portal.azure.com
