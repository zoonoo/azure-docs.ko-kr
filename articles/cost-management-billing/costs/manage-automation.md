---
title: 자동화로 Azure 비용 관리
description: 이 문서에서는 자동화를 사용하여 Azure 비용을 관리하는 방법을 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 2a39f77e3e7409d23ab7506b525f65e01082e99e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720120"
---
# <a name="manage-costs-with-automation"></a>자동화로 비용 관리

Cost Management 자동화를 사용하여 비용 데이터를 검색하고 관리하는 사용자 지정 솔루션 집합을 빌드할 수 있습니다. 이 문서에서는 상황에 따라 사용할 수 있는 Cost Management 자동화 및 옵션에 대한 일반적인 시나리오를 설명합니다. Api를 사용하여 개발하려는 경우 일반적인 API 요청 예제를 통해 개발 프로세스를 가속화하는 데 도움을 제공합니다.

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>오프라인 분석을 위한 비용 데이터 검색 자동화

Azure 비용 데이터를 다운로드하여 다른 데이터 세트와 병합해야 할 수도 있습니다. 또는 사용자의 시스템에 비용 데이터를 통합해야 할 수도 있습니다. 관련된 데이터 양에 따라 다양한 옵션을 사용할 수 있습니다. 모든 경우에 API 및 도구를 사용하려면 적절한 범위에서 Cost Management 권한이 있어야 합니다. 자세한 내용은 [데이터에 대한 액세스 권한 할당](./assign-access-acm-data.md)을 참조하세요.

## <a name="suggestions-for-handling-large-datasets"></a>대량 데이터 세트 처리에 대한 제안

조직의 리소스 또는 구독에 대한 Azure가 많은 경우 다량의 사용 세부 정보 데이터가 포함됩니다. Excel에서는 이렇게 많은 파일을 로드할 수 없는 경우가 많습니다. 이 경우 다음 옵션을 권장합니다.

**Power BI**

Power BI는 많은 양의 데이터를 수집하고 처리하는 데 사용됩니다. 기업계약 고객인 경우 Power BI 템플릿 앱을 사용하여 청구 계정에 대한 비용을 분석할 수 있습니다. 이 보고서에는 고객이 사용하는 주요 보기가 포함되어 있습니다. 자세한 내용은 [Power BI 템플릿 앱을 사용한 Azure 비용 분석](./analyze-cost-data-azure-cost-management-power-bi-template-app.md)을 참조하세요.

**Power BI 데이터 커넥터**

매일 데이터를 분석하려면 [Power BI 데이터 커넥터](/power-bi/connect-data/desktop-connect-azure-cost-management)를 사용하여 자세한 분석을 위해 데이터를 가져오는 것이 좋습니다. 생성하는 모든 보고서는 비용이 증가하는 만큼 커넥터에서 최신 상태로 유지됩니다.

**Cost Management 내보내기**

매일 데이터를 분석할 필요가 없습니다. 이 경우 Cost Management의 [내보내기](./tutorial-export-acm-data.md) 기능을 사용하여 Azure Storage 계정으로 데이터 내보내기를 예약합니다. 그런 다음 필요에 따라 Power BI에 데이터를 로드하거나 파일이 충분히 작은 경우 Excel에서 분석할 수 있습니다. 내보내기는 Azure Portal에서 사용할 수 있으며, [API 내보내기](/rest/api/cost-management/exports)를 사용하여 내보내기를 구성할 수도 있습니다.

**사용량 세부 정보 API**

적은 비용의 데이터 세트를 사용하는 경우 [사용 세부 정보 API](/rest/api/consumption/usageDetails)를 사용하는 것이 좋습니다. 비용 데이터가 많은 경우에는 기간에 대해 가능한 한 적은 양의 사용 데이터를 요청해야 합니다. 이렇게 하려면 작은 시간 범위를 지정하거나 요청에서 필터를 사용합니다. 예를 들어 3년간의 비용 데이터를 필요로 하는 시나리오에서는 단일 호출이 아닌 여러 시간 범위에 대한 여러 호출을 수행하는 경우 API가 더 효율적입니다. 여기에서 추가 분석을 위해 데이터를 Excel에 로드할 수 있습니다.

## <a name="automate-retrieval-with-usage-details-api"></a>사용량 세부 정보 API를 사용하여 검색 자동화

[사용 세부 정보 API](/rest/api/consumption/usageDetails)는 Azure 청구서에 해당하는 원시 집계 비용 데이터를 가져오는 쉬운 방법을 제공합니다. API는 조직에서 프로그래밍 방식으로 데이터 검색 솔루션을 필요로 하는 경우에 유용합니다. 더 작은 비용 데이터 세트를 분석하려는 경우 API를 사용하는 것이 좋습니다. 그러나 데이터 세트가 큰 경우 이전에 식별된 다른 솔루션을 사용해야 합니다. 사용량 세부 정보의 데이터는 시간당 미터 단위로 제공됩니다. 월별 청구를 계산할 때 사용됩니다. 이러한 API의 GA(일반 공급) 버전은 `2019-10-01`입니다. `2019-04-01-preview`를 사용하여 API로 예약 및 Azure Marketplace 구매 미리 보기 버전에 액세스합니다.

대량의 내보낸 데이터를 정기적으로 가져오려면 [내보내기를 사용하여 반복적으로 대규모 비용 데이터 세트 검색](ingest-azure-usage-at-scale.md)을 참조하세요.

### <a name="usage-details-api-suggestions"></a>사용량 세부 정보 API 제안

**요청 일정**

하루에 _두 번 이상 사용량 세부 정보 API에 요청하지 않는_ 것이 좋습니다. 비용 데이터를 새로 고침 빈도 및 반올림 처리 방법에 대한 자세한 내용은 [비용 관리 데이터 이해](./understand-cost-mgt-data.md)를 참조하세요.

**필터링하지 않은 최상위 수준 범위를 대상으로 하기**

API를 사용하여 사용 가능한 최상위 범위에서 필요한 모든 데이터를 가져옵니다. 필터링, 그룹화 또는 집계된 분석을 수행하기 전에 필요한 모든 데이터가 수집될 때까지 기다립니다. API는 집계되지 않은 원시 비용 데이터를 제공하기 위해 특별히 최적화되어 있습니다. Cost Management에서 사용할 수 있는 범위에 대한 자세한 내용은 [범위 이해 및 작업](./understand-work-scopes.md)을 참조하세요. 범위에 필요한 데이터를 다운로드한 후에는 Excel을 사용하여 필터 및 피벗 테이블로 데이터를 추가로 분석할 수 있습니다.

### <a name="notes-about-pricing"></a>가격 책정에 대한 참고 사항

사용량 및 요금을 가격표 또는 송장에 맞게 조정하려면 다음 정보를 참고하세요.

가격표 가격 동작 - 가격표에 표시되는 가격은 Azure에서 받은 가격입니다. 특정 측정 단위로 크기가 조정됩니다. 안타깝게도 측정 단위가 실제 리소스 사용량과 요금이 부과되는 측정 단위와 항상 일치하지는 않습니다.

사용량 세부 정보 가격 동작 - 사용량 파일은 가격표와 정확하게 일치하지 않을 수 있는 조정된 정보를 표시합니다. 특히 다음에 대한 내용을 설명합니다.

- 단가 - 가격은 Azure 리소스에서 실제로 요금이 부과되는 측정 단위와 일치하도록 조정됩니다. 크기 조정이 발생하면 가격은 가격표에 표시된 가격과 일치하지 않습니다.
- 측정 단위 - Azure 리소스에서 실제로 요금을 부과하는 측정 단위를 나타냅니다.
- 유효 가격 / 리소스 요율 - 가격은 할인이 고려된 후 단위당 지불하게 되는 실제 요율을 나타냅니다. 요금을 조정하기 위해 가격 * 수량 계산을 수행하는 데 사용해야 하는 가격입니다. 가격에는 다음 시나리오와 파일에도 표시되는 조정된 단가가 고려됩니다. 따라서 조정된 단가와 다를 수 있습니다.
  - 계층화된 가격 책정 - 예: 처음 100개 단위는 $10, 다음 100개 단위는 $8입니다.
  - 포항된 수량 - 예: 처음 100개 단위는 무료이며 단위당 $10입니다.
  - 예약
  - 계산 중에 발생하는 반올림 - 반올림은 사용된 수량, 계층화/포함된 수량 가격 및 조정된 단가를 고려합니다.

### <a name="a-single-resource-might-have-multiple-records-for-a-single-day"></a>단일 리소스에는 하루의 레코드가 여러 개 있을 수 있습니다.

Azure 리소스 공급자는 사용량 및 요금을 청구 시스템에 내보내고 사용량 레코드의 `Additional Info` 필드를 채웁니다. 경우에 따라 리소스 공급자는 특정 일의 사용량을 내보내고 사용량 레코드의 `Additional Info` 필드에서 다른 데이터 센터가 있는 레코드를 스탬프 처리할 수 있습니다. 이로 인해 하루의 사용량 파일에 미터/리소스의 레코드가 여러 개 표시될 수 있습니다. 이 경우 초과 요금이 부과되지 않습니다. 여러 레코드는 해당 일의 리소스에 대한 미터 전체 비용을 나타냅니다.

## <a name="example-usage-details-api-requests"></a>예제 사용량 정보 API 요청

다음 예제 요청은 Microsoft 고객이 제공하는 일반적인 시나리오를 해결하는 데 사용됩니다.

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>특정 날짜 범위 동안 범위에 대한 사용량 세부 정보 가져오기

요청에 의해 반환되는 데이터는 청구 시스템이 사용량을 받은 날짜에 해당합니다. 여기에는 여러 청구서의 비용이 포함될 수 있습니다. 사용할 호출은 구독 유형에 따라 달라집니다.

EA(기업계약) 또는 종량제 구독이 있는 레거시 고객의 경우 다음 호출을 사용합니다.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01
```

Microsoft 고객 계약이 있는 최신 고객의 경우 다음 호출을 사용합니다.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?startDate=2020-08-01&endDate=&2020-08-05$top=1000&api-version=2019-10-01
```

### <a name="get-amortized-cost-details"></a>분할 상환 비용 세부 정보 얻기

발생한 구매를 표시하는 데 실제 비용이 필요한 경우 다음 요청에서 *메트릭* 을 `ActualCost`으로 변경합니다. 분할 상환 및 실제 비용을 사용하려면 `2019-04-01-preview` 버전을 사용해야 합니다. 현재 API 버전은 새 형식/메트릭 특성과 변경된 속성 이름을 제외하고 `2019-10-01` 버전과 동일하게 작동합니다. Microsoft 고객 계약이 있는 경우 다음 예제에서 필터를 `startDate`하고 `endDate` 합니다.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="automate-alerts-and-actions-with-budgets"></a>예산으로 경고 및 작업 자동화

클라우드에서 투자 가치를 극대화하는 데 중요한 두 가지 구성 요소가 있습니다. 하나는 자동 예산 만들기입니다. 다른 하나는 예산 경고에 대한 응답으로 비용 기반 오케스트레이션을 구성하는 것입니다. Azure 예산 만들기를 자동화하는 방법에는 여러 가지가 있습니다. 구성된 경고 임계값을 초과하면 다양한 경고 응답이 발생합니다.

다음 섹션에서는 사용 가능한 옵션에 대해 설명하고, 예산 자동화를 시작하기 위한 샘플 API 요청을 제공합니다.

### <a name="how-costs-are-evaluated-against-your-budget-threshold"></a>예산 임계값과 비교하여 비용을 평가하는 방법

비용은 하루에 한 번 예산 임계값과 비교하여 평가됩니다. 새 예산을 만들 때 또는 예산 재설정일에는 평가를 수행하지 않았을 수 있으므로 임계값과 비교한 비용이 0/null이 됩니다.

Azure에서 비용이 임계값을 초과했음을 감지하는 경우 알림이 감지 기간의 시간 내에 트리거됩니다.

### <a name="view-your-current-cost"></a>현재 비용 보기

현재 비용을 보려면 [쿼리 API](/rest/api/cost-management/query)를 사용하여 GET 호출을 수행해야 합니다.

예산 API에 대한 GET 호출은 비용 분석에 표시된 현재 비용을 반환하지 않습니다. 대신, 호출에서 마지막으로 평가된 비용을 반환합니다.

### <a name="automate-budget-creation"></a>예산 만들기 자동화

[예산 API](/rest/api/consumption/budgets)를 사용하여 예산 만들기를 자동화할 수 있습니다. 예산은 [예산 템플릿](quick-create-budget-template.md)을 사용하여 만들 수도 있습니다. 템플릿은 Azure 배포를 표준화하는 동시에 비용 제어가 적절하게 구성되고 적용되도록 하는 쉬운 방법입니다.

### <a name="supported-locales-for-budget-alert-emails"></a>예산 경고 이메일이 지원되는 로캘

예산을 사용하면 비용이 설정된 임계값을 초과하는 경우 경고가 표시됩니다. 예산당 최대 5명의 이메일 수신자를 설정할 수 있습니다. 예산 임계값을 초과하면 수신자는 24시간 이내에 이메일 알림을 받습니다. 그러나 수신자는 다른 언어로 된 이메일을 받을 수도 있습니다. 다음 언어 문화권 코드를 예산 API와 함께 사용할 수 있습니다. 다음 예와 유사한 `locale` 매개 변수를 사용하여 문화권 코드를 설정합니다.

```json
{
  "eTag": "\"1d681a8fc67f77a\"",
  "properties": {
    "timePeriod": {
      "startDate": "2020-07-24T00:00:00Z",
      "endDate": "2022-07-23T00:00:00Z"
    },
    "timeGrain": "BillingMonth",
    "amount": 1,
    "currentSpend": {
      "amount": 0,
      "unit": "USD"
    },
    "category": "Cost",
    "notifications": {
      "actual_GreaterThan_10_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 20,
        "locale": "en-us",
        "contactEmails": [
          "user@contoso.com"
        ],
        "contactRoles": [],
        "contactGroups": [],
        "thresholdType": "Actual"
      }
    }
  }
}

```

문화권 코드에서 지원되는 언어:

| 문화권 코드| 언어 |
| --- | --- |
| ko-kr | 영어(미국) |
| ja-jp | 일본어(일본) |
| zh-cn | 중국어(간체, 중국) |
| de-de | 독일어 (독일) |
| es-es | 스페인어(스페인, 국제) |
| fr-fr | 프랑스어(프랑스) |
| it-it | 이탈리아어(이탈리아) |
| ko-kr | 한국어(한국) |
| pt-br | 포르투갈어(브라질) |
| ru-ru | 러시아어(러시아) |
| zh-tw | 중국어(번체, 대만) |
| cs-cz | 체코어(체코) |
| pl-pl | 폴란드어(폴란드) |
| tr-tr | 터키어(터키) |
| da-dk | 덴마크어(덴마크) |
| en-gb | 영어(영국) |
| hu-hu | 헝가리어(헝가리) |
| nb-no | 노르웨이어 복말(노르웨이) |
| nl-nl | 네덜란드어(네덜란드) |
| pt-pt | 포르투갈어(포르투갈) |
| sv-se | 스웨덴어(스웨덴) |

### <a name="common-budgets-api-configurations"></a>일반적인 예산 API 구성

Azure 환경에서 예산을 구성하는 방법에는 여러 가지가 있습니다. 먼저 시나리오를 고려한 다음, 이를 사용하도록 설정하는 구성 옵션을 식별합니다. 검토할 옵션은 다음과 같습니다.

- **시간 조직** - 예산에서 비용을 발생시키고 평가하는 데 사용하는 되풀이 기간을 나타냅니다. 가장 일반적인 옵션은 매월, 매분기 및 매년입니다.
- **기간** - 예산이 유효한 기간을 나타냅니다. 예산은 유효한 상태로 유지되는 동안에만 적극적으로 모니터링하고 경고합니다.
- **알림**
  - 연락처 이메일 – 예산에서 비용이 발생하고 정의된 임계값을 초과하는 경우 이메일 주소에서 경고를 받습니다.
  - 연락처 역할 - 이 옵션을 사용하면 지정된 범위에서 일치하는 Azure 역할이 있는 모든 사용자가 이메일 경고를 받습니다. 예를 들어 구독 소유자는 구독 범위에서 만들어진 예산에 대한 경고를 받을 수 있습니다.
  - 연락처 그룹 - 경고 임계값을 초과하는 경우 예산에서 구성된 작업 그룹을 호출합니다.
- **비용 차원 필터** - 비용 분석 또는 쿼리 API에서 수행할 수 있는 것과 동일한 필터링을 예산에서도 수행할 수 있습니다. 이 필터를 사용하여 예산으로 모니터링하는 비용 범위를 줄입니다.

요구 사항에 맞는 예산 만들기 옵션이 확인되었으면 API를 사용하여 예산을 만듭니다. 아래 예제는 일반적인 예산 구성을 시작하는 데 도움이 됩니다.

**여러 리소스 및 태그로 필터링된 예산 만들기**

요청 URL: `PUT https://management.azure.com/subscriptions/{SubscriptionId} /providers/Microsoft.Consumption/budgets/{BudgetName}/?api-version=2019-10-01`

```json
{
  "eTag": "\"1d34d016a593709\"",
  "properties": {
    "category": "Cost",
    "amount": 100.65,
    "timeGrain": "Monthly",
    "timePeriod": {
      "startDate": "2017-10-01T00:00:00Z",
      "endDate": "2018-10-31T00:00:00Z"
    },
    "filter": {
      "and": [
        {
          "dimensions": {
            "name": "ResourceId",
            "operator": "In",
            "values": [
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}",
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}"
            ]
          }
        },
        {
          "tags": {
            "name": "category",
            "operator": "In",
            "values": [
              "Dev",
              "Prod"
            ]
          }
        },
        {
          "tags": {
            "name": "department",
            "operator": "In",
            "values": [
              "engineering",
              "sales"
            ]
          }
        }
      ]
    },
    "notifications": {
      "Actual_GreaterThan_80_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 80,
        "contactEmails": [
          "user1@contoso.com",
          "user2@contoso.com"
        ],
        "contactRoles": [
          "Contributor",
          "Reader"
        ],
        "contactGroups": [
          "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.insights/actionGroups/{actionGroupName}
        ],
        "thresholdType": "Actual"
      }
    }
  }
}
```

### <a name="configure-cost-based-orchestration-for-budget-alerts"></a>예산 경고에 대한 비용 기반 오케스트레이션 구성

Azure 작업 그룹을 사용하여 자동화된 작업을 시작하도록 예산을 구성할 수 있습니다. 예산을 사용하여 작업을 자동화하는 방법에 대한 자세한 내용은 [Azure 예산으로 자동화](../manage/cost-management-budget-scenario.md)를 참조하세요.

## <a name="data-latency-and-rate-limits"></a>데이터 대기 시간 및 속도 제한

API는 하루에 한 번만 호출하는 것이 좋습니다. Azure 리소스 공급자에서 새 사용량 데이터가 수신되면 Cost Management 데이터가 4시간마다 데이터를 새로 고칩니다. 더 자주 호출해도 더 많은 데이터가 제공되지 않습니다. 대신 부하가 증가합니다. 데이터 변경 빈도와 데이터 대기 시간 처리 방법에 대한 자세한 내용은 [비용 관리 데이터 이해](understand-cost-mgt-data.md)를 참조하세요.

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>오류 코드 429-호출 횟수가 속도 제한을 초과했습니다.

모든 Cost Management 구독자가 일관된 환경을 사용하도록 설정하기 위해 Cost Management API는 속도가 제한됩니다. 한도에 도달하면 HTTP 상태 코드 `429: Too many requests`이 표시됩니다. API에 대한 현재 처리량 제한은 다음과 같습니다.

- 분당 30개 호출 - 범위, 사용자 또는 응용 프로그램별로 수행됩니다.
- 분당 200개 호출- 테넌트, 사용자 또는 응용 프로그램별로 수행됩니다.

## <a name="next-steps"></a>다음 단계

- [Power BI 템플릿 앱을 사용하여 Azure 비용 분석](./analyze-cost-data-azure-cost-management-power-bi-template-app.md)
- 내보내기로 [내보낸 데이터 만들기 및 관리](./tutorial-export-acm-data.md)
- [사용량 세부 정보 API](/rest/api/consumption/usageDetails)에 대해 자세히 알아보세요.
