---
title: 자동화로 Azure 비용 관리
description: 이 문서에서는 자동화를 사용하여 Azure 비용을 관리하는 방법을 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 04/15/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 0727f98b917944f3721c6c6758fde05c2efd8773
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449834"
---
# <a name="manage-costs-with-automation"></a>자동화로 비용 관리

Cost Management 자동화를 사용하여 비용 데이터를 검색하고 관리하는 사용자 지정 솔루션 집합을 빌드할 수 있습니다. 이 문서에서는 상황에 따라 사용할 수 있는 Cost Management 자동화 및 옵션에 대한 일반적인 시나리오를 설명합니다. Api를 사용하여 개발하려는 경우 일반적인 API 요청 예제를 통해 개발 프로세스를 가속화하는 데 도움을 제공합니다.

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>오프라인 분석을 위한 비용 데이터 검색 자동화

Azure 비용 데이터를 다운로드하여 다른 데이터 세트와 병합해야 할 수도 있습니다. 또는 사용자의 시스템에 비용 데이터를 통합해야 할 수도 있습니다. 관련된 데이터 양에 따라 다양한 옵션을 사용할 수 있습니다. 모든 경우에 API 및 도구를 사용하려면 적절한 범위에서 Cost Management 권한이 있어야 합니다. 자세한 내용은 [데이터에 대한 액세스 권한 할당](https://docs.microsoft.com/azure/cost-management-billing/costs/assign-access-acm-data)을 참조하세요.

## <a name="suggestions-for-handling-large-datasets"></a>대량 데이터 세트 처리에 대한 제안

조직의 리소스 또는 구독에 대한 Azure가 많은 경우 다량의 사용 세부 정보 데이터가 포함됩니다. Excel에서는 이렇게 많은 파일을 로드할 수 없는 경우가 많습니다. 이 경우 다음 옵션을 권장합니다.

**Power BI**

Power BI는 많은 양의 데이터를 수집하고 처리하는 데 사용됩니다. 기업계약 고객인 경우 Power BI 템플릿 앱을 사용하여 청구 계정에 대한 비용을 분석할 수 있습니다. 이 보고서에는 고객이 사용하는 주요 보기가 포함되어 있습니다. 자세한 내용은 [Power BI 템플릿 앱을 사용한 Azure 비용 분석](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)을 참조하세요.

**Power BI 데이터 커넥터**

매일 데이터를 분석하려면 [Power BI 데이터 커넥터](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management)를 사용하여 자세한 분석을 위해 데이터를 가져오는 것이 좋습니다. 생성하는 모든 보고서는 비용이 증가하는 만큼 커넥터에서 최신 상태로 유지됩니다.

**Cost Management 내보내기**

매일 데이터를 분석할 필요가 없습니다. 이 경우 Cost Management의 [내보내기](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) 기능을 사용하여 Azure Storage 계정으로 데이터 내보내기를 예약합니다. 그런 다음 필요에 따라 Power BI에 데이터를 로드하거나 파일이 충분히 작은 경우 Excel에서 분석할 수 있습니다. 내보내기는 Azure Portal에서 사용할 수 있으며, [API 내보내기](https://docs.microsoft.com/rest/api/cost-management/exports)를 사용하여 내보내기를 구성할 수도 있습니다.

**사용량 세부 정보 API**

적은 비용의 데이터 세트를 사용하는 경우 [사용 세부 정보 API](https://docs.microsoft.com/rest/api/consumption/usageDetails)를 사용하는 것이 좋습니다. 비용 데이터가 많은 경우에는 기간에 대해 가능한 한 적은 양의 사용 데이터를 요청해야 합니다. 이렇게 하려면 작은 시간 범위를 지정하거나 요청에서 필터를 사용합니다. 예를 들어 3년간의 비용 데이터를 필요로 하는 시나리오에서는 단일 호출이 아닌 여러 시간 범위에 대한 여러 호출을 수행하는 경우 API가 더 효율적입니다. 여기에서 추가 분석을 위해 데이터를 Excel에 로드할 수 있습니다.

## <a name="automate-retrieval-with-usage-details-api"></a>사용량 세부 정보 API를 사용하여 검색 자동화

[사용 세부 정보 API](https://docs.microsoft.com/rest/api/consumption/usageDetails)는 Azure 청구서에 해당하는 원시 집계 비용 데이터를 가져오는 쉬운 방법을 제공합니다. API는 조직에서 프로그래밍 방식으로 데이터 검색 솔루션을 필요로 하는 경우에 유용합니다. 더 작은 비용 데이터 세트를 분석하려는 경우 API를 사용하는 것이 좋습니다. 그러나 데이터 세트가 큰 경우 이전에 식별된 다른 솔루션을 사용해야 합니다. 사용량 세부 정보의 데이터는 시간당 미터 단위로 제공됩니다. 월별 청구를 계산할 때 사용됩니다. 이러한 API의 GA(일반 공급) 버전은 `2019-10-01`입니다. `2019-04-01-preview`를 사용하여 API로 예약 및 Azure Marketplace 구매 미리 보기 버전에 액세스합니다.

### <a name="usage-details-api-suggestions"></a>사용량 세부 정보 API 제안

**요청 일정**

하루에 _두 번 이상 사용량 세부 정보 API에 요청하지 않는_ 것이 좋습니다. 비용 데이터를 새로 고침 빈도 및 반올림 처리 방법에 대한 자세한 내용은 [비용 관리 데이터 이해](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data#rated-usage-data-refresh-schedule)를 참조하세요.

**필터링하지 않은 최상위 수준 범위를 대상으로 하기**

API를 사용하여 사용 가능한 최상위 범위에서 필요한 모든 데이터를 가져옵니다. 필터링, 그룹화 또는 집계된 분석을 수행하기 전에 필요한 모든 데이터가 수집될 때까지 기다립니다. API는 집계되지 않은 원시 비용 데이터를 제공하기 위해 특별히 최적화되어 있습니다. Cost Management에서 사용할 수 있는 범위에 대한 자세한 내용은 [범위 이해 및 작업](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-work-scopes)을 참조하세요. 범위에 필요한 데이터를 다운로드한 후에는 Excel을 사용하여 필터 및 피벗 테이블로 데이터를 추가로 분석할 수 있습니다.

## <a name="example-usage-details-api-requests"></a>예제 사용량 정보 API 요청

다음 예제 요청은 Microsoft 고객이 제공하는 일반적인 시나리오를 해결하는 데 사용됩니다.

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>특정 날짜 범위 동안 범위에 대한 사용량 세부 정보 가져오기

요청에 의해 반환되는 데이터는 청구 시스템이 사용량을 받은 날짜에 해당합니다. 여기에는 여러 청구서의 비용이 포함될 수 있습니다.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01

```

### <a name="get-amortized-cost-details"></a>분할 상환 비용 세부 정보 얻기

발생한 구매를 표시하는 데 실제 비용이 필요한 경우 다음 요청에서 *메트릭*을 `ActualCost`으로 변경합니다. 분할 상환 및 실제 비용을 사용하려면 `2019-04-01-preview` 버전을 사용해야 합니다. 현재 API 버전은 새 형식/메트릭 특성과 변경된 속성 이름을 제외하고 `2019-10-01` 버전과 동일하게 작동합니다. Microsoft 고객 계약이 있는 경우 다음 예제에서 필터를 `startDate`하고 `endDate` 합니다.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>내보내기를 사용하여 대량 비용 데이터 세트 순환 검색

내보내기 기능은 비용 데이터 덤프를 정기적으로 예약하는 솔루션입니다. 사용량 파일이 너무 커서 사용량 세부 정보 API를 사용하여 데이터를 안정적으로 호출하고 다운로드할 수 있는 조직에 대해 집계되지 않은 비용 데이터를 검색하는 것이 좋습니다. 데이터는 사용자가 선택한 Azure Storage 계정에 배치됩니다. 여기에서 사용자의 시스템에 로드하고 팀의 필요에 따라 분석할 수 있습니다. Azure Portal에서 내보내기를 구성하려면 [데이터 내보내기](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data)를 참조하세요.

## <a name="data-latency-and-rate-limits"></a>데이터 대기 시간 및 속도 제한

API는 하루에 한 번만 호출하는 것이 좋습니다. Azure 리소스 공급자에서 새 사용량 데이터가 수신되면 Cost Management 데이터가 4시간마다 데이터를 새로 고칩니다. 더 자주 호출하면 추가 데이터가 제공되지 않습니다. 대신 부하가 증가합니다. 데이터 변경 빈도와 데이터 대기 시간 처리 방법에 대한 자세한 내용은 [비용 관리 데이터 이해](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data)를 참조하세요.

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>오류 코드 429-호출 횟수가 속도 제한을 초과했습니다.

모든 Cost Management 구독자가 일관된 환경을 사용하도록 설정하기 위해 Cost Management API는 속도가 제한됩니다. 한도에 도달하면 HTTP 상태 코드 `429: Too many requests`이 표시됩니다. API에 대한 현재 처리량 제한은 다음과 같습니다.

- 분당 30개 호출 - 범위, 사용자 또는 응용 프로그램별로 수행됩니다.
- 분당 200개 호출- 테넌트, 사용자 또는 응용 프로그램별로 수행됩니다.

## <a name="next-steps"></a>다음 단계

- [Power BI 템플릿 앱을 사용하여 Azure 비용 분석](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)
- 내보내기로 [내보낸 데이터 만들기 및 관리](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data)
- [사용량 세부 정보 API](https://docs.microsoft.com/rest/api/consumption/usageDetails)에 대해 자세히 알아보세요.