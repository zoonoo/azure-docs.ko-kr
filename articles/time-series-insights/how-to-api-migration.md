---
title: 새 Azure Time Series Insights Gen2 API 버전으로 마이그레이션 | Microsoft Docs
description: Azure Time Series Insights Gen2 환경을 업데이트하여 새 정품 버전을 사용하는 방법입니다.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: cnovak
ms.reviewer: orspodek
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: shresha
ms.openlocfilehash: 07476c9d5e8d391e2c902217cf19f81d02e00903
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110615512"
---
# <a name="migrating-to-new-azure-time-series-insights-gen2-api-versions"></a>새 Azure Time Series Insights Gen2 API 버전으로 마이그레이션

## <a name="overview"></a>개요

퍼블릭 미리 보기(2020년 7월 16일 이전)에서 Azure Time Series Insights Gen2 환경을 만든 경우 이 문서에 설명된 단계에 따라 새 정품 버전의 API를 사용하도록 TSI 환경을 업데이트하세요. 이 변경은 Azure Time Series Insights Gen1 버전을 사용하는 사용자에게는 영향을 주지 않습니다.

> [!IMPORTANT]
> 이 문서에서 설명하는 업데이트는 TSI 환경에서 사용하는 API 버전만 업그레이드합니다. 이 변경은 Gen2 환경에 도입된 새로운 [JSON 평면화 및 이스케이프 규칙](./concepts-json-flattening-escaping-rules.md)과 관련이 없습니다.

새 API 버전은 `2020-07-31`이며 업데이트된 [Time Series 식 구문](/rest/api/time-series-insights/reference-time-series-expression-syntax)을 사용합니다.

사용자는 환경의 [시계열 모델 변수](./concepts-variables.md), 저장된 쿼리, Power BI 쿼리 및 API 엔드포인트를 호출하는 사용자 지정 도구를 마이그레이션해야 합니다. 마이그레이션 프로세스에 대한 질문이나 우려가 있는 경우 Azure Portal을 통해 지원 티켓을 제출하고 이 문서를 언급합니다.

> [!IMPORTANT]
> 미리 보기 API 버전 `2018-11-01-preview`는 2020년 10월 31일까지 계속 지원됩니다. 서비스 중단을 방지하려면 먼저 이 마이그레이션의 모든 해당 단계를 완료하세요.

## <a name="migrate-time-series-model-and-saved-queries"></a>시계열 모델 및 저장된 쿼리 마이그레이션

사용자가 [시계열 모델 변수](./concepts-variables.md) 및 저장된 쿼리를 마이그레이션할 수 있도록 지원하기 위해 [Azure Time Series Insights 탐색기](https://insights.timeseries.azure.com)를 통해 사용할 수 있는 기본 제공 도구가 제공됩니다. 마이그레이션하려는 환경으로 이동하여 다음 단계를 수행합니다. **마이그레이션을 부분적으로 완료하고 되돌아가 나중에 완료할 수 있지만 업데이트를 되돌릴 수 없습니다.**

> [!NOTE]
> 시계열 모델 및 저장된 쿼리를 업데이트하려면 환경에 대한 기여자여야 합니다. 기여자가 아닌 경우에는 저장된 개인 쿼리만 마이그레이션할 수 있습니다. 계속하기 전에 [환경 액세스 정책](./concepts-access-policies.md) 및 액세스 수준을 검토하세요.

1. 탐색기에서 시계열 모델 변수 및 저장된 쿼리에 사용되는 구문을 업데이트하라는 메시지가 표시됩니다.

    [![prompt](media/api-migration/ux-prompt.png)](media/v2-update-overview/overview-one.png#lightbox)

    알림을 실수로 닫은 경우 알림 패널에서 찾을 수 있습니다.

1. **업데이트 표시** 를 클릭하여 마이그레이션 도구를 엽니다.

1. **형식 다운로드** 를 클릭합니다. 마이그레이션은 현재 형식을 덮어써서 변수 구문을 수정하므로 현재 형식의 복사본을 저장해야 합니다. 이 도구는 형식이 다운로드될 때 사용자에게 알립니다.

    [![형식 다운로드](media/api-migration/ux-migration-tool.png)](media/v2-update-overview/overview-one.png#lightbox)

1. **변수 업데이트** 를 클릭합니다. 변수가 업데이트되면 사용자에게 알림이 표시됩니다.

    > [!IMPORTANT]
    > 유형을 업데이트하는 경우 이전 API 버전(`2018-11-01-preview`)을 사용하는 사용자 지정 애플리케이션이 계속 작동하려면 새 API 버전(`2020-07-31`)을 사용해야 합니다. 사용 중인 API 버전을 잘 모르겠으면 업데이트하기 전에 관리자에게 확인하세요. 마이그레이션 도구를 닫고 나중에 이러한 단계로 돌아갈 수 있습니다. 자세한 내용은 [사용자 지정 애플리케이션을 마이그레이션하는 방법](#migrate-custom-applications)을 참조하세요.

    [![변수 업데이트](media/api-migration/ux-migration-tool-downloaded-types.png)](media/v2-update-overview/overview-one.png#lightbox)

1. **저장된 쿼리 업데이트** 를 클릭합니다. 저장된 쿼리가 업데이트되면 이 도구에 알림이 표시됩니다.

    [![저장된 쿼리 업데이트](media/api-migration/ux-migration-tool-updated-variables.png)](media/v2-update-overview/overview-one.png#lightbox)

1. **Done** 을 클릭합니다.

    [![완료된 마이그레이션](media/api-migration/ux-migration-tool-updated-saved-queries.png)](media/v2-update-overview/overview-one.png#lightbox)

새로 만든 변수 및 저장된 쿼리 중 일부를 차트로 작성하여 업데이트된 환경을 검토합니다. 차트를 작성하는 동안 예기치 않은 동작이 표시되면 탐색기에서 피드백 도구를 사용하여 피드백을 보내주세요.

## <a name="migrate-power-bi-queries"></a>Power BI 쿼리 마이그레이션

Power BI Connector를 사용하여 쿼리를 생성한 경우 미리 보기 API 버전 및 이전 시계열 식 구문을 사용하여 Azure Time Series Insights를 호출하게 됩니다. 이러한 쿼리는 미리 보기 API가 더 이상 사용되지 않을 때까지 계속해서 데이터를 검색합니다.

새 API 버전 및 새 시계열 식 구문을 사용하도록 쿼리를 업데이트하려면 탐색기에서 쿼리를 다시 생성해야 합니다. [Power BI Connector를 사용하여 쿼리를 만드는 방법](./how-to-connect-power-bi.md)에 대해 자세히 알아보세요.

> [!NOTE]
> Power BI Desktop 2020년 7월 버전 이상을 사용해야 합니다. 그러지 않으면 "잘못된 쿼리 페이로드 버전" 오류가 표시될 수 있습니다.

## <a name="migrate-custom-applications"></a>사용자 지정 애플리케이션 마이그레이션

사용자 지정 애플리케이션이 다음 REST 엔드포인트를 호출하는 경우 URI에서 API 버전을 `2020-07-31`로 업데이트하는 것으로 충분합니다.

- Time Series Model API
  - 모델 설정 API
    - [가져오기](/rest/api/time-series-insights/dataaccessgen2/modelsettings/get)
    - [업데이트](/rest/api/time-series-insights/dataaccessgen2/modelsettings/update)
  - 인스턴스 API
    - [모든 Batch 작업](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)
    - [목록](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/list)
    - [검색](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)
    - [제안](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)
  - 계층 구조 API
    - [모든 Batch 작업](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    - [목록](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/list)
  - 형식 API
    - [삭제, 가져오기 작업](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    - [목록](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/list)

다음 REST 엔드포인트의 경우 URI에서 API 버전을 `2020-07-31`로 업데이트하고 `tsx` 속성의 모든 항목이 업데이트된 [시계열 식 구문](/rest/api/time-series-insights/reference-time-series-expression-syntax)을 사용하는지 확인해야 합니다.

- 형식 API
  - [PUT 작업](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput)
- 쿼리 API
  - [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)
  - [GetSeries](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)
  - [GetAggregateSeries](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)

### <a name="examples"></a>예

#### <a name="typesbatchput"></a>TypesBatchPut

이전 요청 본문(`2018-11-01-preview`에서 사용됨):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.[Temperature_Celsius].Double"
          },
          "filter": {
            "tsx": "$event.[Mode].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

업데이트된 요청 본문(`2020-07-31`에서 사용됨):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event['Temperature_Celsius'].Double"
          },
          "filter": {
            "tsx": "$event['Mode'].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

또는 `filter`가 `$event.Mode.String = 'outdoor'`일 수도 있습니다. `value`는 괄호를 사용하여 특수 문자(`_`)를 이스케이프해야 합니다.

#### <a name="getevents"></a>GetEvents

이전 요청 본문(`2018-11-01-preview`에서 사용됨):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.[Value].Double != null) OR ($event.[Status].String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

업데이트된 요청 본문(`2020-07-31`에서 사용됨):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.Value.Double != null) OR ($event.Status.String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

또는 `filter`가 `($event['Value'].Double != null) OR ($event['Status'].String = 'Good')`일 수도 있습니다.

#### <a name="getseries"></a>GetSeries

이전 요청 본문(`2018-11-01-preview`에서 사용됨):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event.[Bar-Pressure-Offset]"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

업데이트된 요청 본문(`2020-07-31`에서 사용됨):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event['Bar-Pressure-Offset']"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

또는 `value`가 `$event['Bar-Pressure-Offset'].Double`일 수도 있습니다. 데이터 형식이 지정되지 않은 경우 데이터 형식은 항상 Double로 가정됩니다. 특수 문자(`-`)를 이스케이프하려면 대괄호 표기법을 사용해야 합니다.

#### <a name="aggregateseries"></a>AggregateSeries

이전 요청 본문(`2018-11-01-preview`에서 사용됨):

```JSON
{
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.[Temp].Double, toDouble($event.[Temp].Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

업데이트된 요청 본문(`2020-07-31`에서 사용됨):

```JSON
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.Temp.Double, toDouble($event.Temp.Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

또는 `value`가 `coalesce($event['Temp'].Double, toDouble($event['Temp'].Long))`일 수도 있습니다.

### <a name="potential-errors"></a>잠재적 오류

#### <a name="invalidinput"></a>InvalidInput

다음 오류가 표시되면 새 API 버전(`2020-07-31`)을 사용하지만 TSX 구문이 업데이트되지 않은 것입니다. 위의 [시계열 식 구문](/rest/api/time-series-insights/reference-time-series-expression-syntax) 및 마이그레이션 예제를 검토하세요. API 요청을 다시 제출하기 전에 모든 `tsx` 속성이 올바르게 업데이트되었는지 확인합니다.

```JSON
{
    "error": {
        "code": "InvalidInput",
        "message": "Unable to parse 'value' time series expression (TSX) in variable 'Temperature'.",
        "target": "projectedVariables.Temperature.value",
        "innerError": {
            "parseErrorDetails": [
                {
                    "pos": [
                        0,
                        5
                    ],
                    "line": 1,
                    "msg": "Unsupported Time Series Expression version TSX01 used instead of TSX00.",
                    "code": "UnsupportedTSXVersionTSX01",
                    "target": "$event"
                }
            ],
            "code": "TsxParseError"
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

- [Azure Time Series Insights 탐색기](./concepts-ux-panels.md) 또는 사용자 지정 애플리케이션을 통해 환경을 테스트합니다.