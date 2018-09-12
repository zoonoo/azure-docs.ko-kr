---
title: 테 넌 트 리소스 사용량 API | Microsoft Docs
description: Azure Stack 사용 정보를 검색 하는 리소스 사용량 API에 대 한 참조입니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: ab5dad550e590cd70f54ad5c8d4727d0f6370190
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379715"
---
# <a name="tenant-resource-usage-api"></a>테 넌 트 리소스 사용량 API

테 넌 트를 테 넌 트 API를 사용 하 여 테 넌 트의 고유한 리소스 사용량 데이터를 볼 수 있습니다. 이 API는 Azure 사용량 API를 사용 하 여 일관 된 (현재 비공개 미리 보기)입니다.

Windows PowerShell cmdlet을 사용할 수 있습니다 **Get UsageAggregates** Azure에서와 같은 사용 현황 데이터를 가져오려고 합니다.

## <a name="api-call"></a>API 호출
### <a name="request"></a>요청
요청 요청된 시간 프레임 및 요청 된 구독에 대 한 사용량 세부 정보를 가져옵니다. 요청 본문이 없습니다.

| **메서드** | **요청 URI** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>인수
| **인수** | **설명** |
| --- | --- |
| *armendpoint* |Azure Stack 환경과의 azure Resource Manager 끝점입니다. Azure Stack 규칙은 Azure Resource Manager 끝점의 이름 형식으로 `https://management.{domain-name}`입니다. 예를 들어, 개발 키트에 대 한 도메인 이름이 local.azurestack.external, 그런 다음 Resource Manager 끝점은 `https://management.local.azurestack.external`합니다. |
| *subId* |호출 하는 사용자의 구독 ID입니다. 단일 구독의 사용량에 대 한 쿼리에만이 API를 사용할 수 있습니다. 공급자 쿼리 사용 하는 공급자 리소스 사용량 API를 사용 하 여 모든 테 넌 트에 대 한 수 있습니다. |
| *reportedStartTime* |쿼리 시간을 시작 합니다. 에 대 한 값 *날짜/시간* UTC에 시작 시간, 예를 들어 13:00 부분에 있어야 합니다. 일별로 집계에 대 한 UTC 자정에이 값을 설정 합니다. 형식은 *이스케이프* ISO 8601, 예를 들어 2015-06-16T18% 3a53% 3a11% 2b00 %3a00Z, %3a로 콜론은 이스케이프 하는 위치 및 plus는 URI %2b으로 이스케이프 됩니다. |
| *reportedEndTime* |쿼리의 종료 시간입니다. 에 적용 되는 제약 조건을 *reportedStartTime* 이 인수에도 적용 합니다. 에 대 한 값 *reportedEndTime* 나중에 사용할 수 없습니다. |
| *aggregationGranularity* |두 불연속 잠재적인 값이 포함 된 선택적 매개 변수: 매일, 매시간. 값 제안으로 일별 단위로 데이터를 반환 하나 고 다른 하나는 시간을 확인 합니다. 매일 옵션에는 기본값입니다. |
| *api-version* |이 요청을 만드는 데 사용 되는 프로토콜의 버전입니다. 2015-06-01-미리 보기를 사용 해야 합니다. |
| *continuationToken* |토큰 마지막 사용량 API 공급자 호출에서 검색 합니다. 이 토큰 응답 1,000 줄 보다 큽니다. 진행률에 대 한 책갈피로 역할 때 필요 합니다. 가 없으면 일의 시작 부분에서 데이터를 검색 하거나 시간, 세분성에 따라 전달 합니다. |

### <a name="response"></a>response
GET /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&api-version=1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",
"name": "sub1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

…
```

### <a name="response-details"></a>응답 세부 정보
| **인수** | **설명** |
| --- | --- |
| *id* |사용 현황 집계의 고유 ID |
| *name* |사용 현황 집계의 이름 |
| *type* |리소스 정의 |
| *subscriptionId* |Azure 사용자의 구독 id |
| *usageStartTime* |UTC 시간인이 사용 현황 집계가 속하는 사용 현황 버킷의 시작 |
| *usageEndTime* |이 사용 현황 집계가 속하는 사용 현황 버킷의 UTC 종료 시간 |
| *instanceData* |키-값 쌍을 새 형식으로 인스턴스 세부 정보:<br>  *resourceUri*: 정규화 된 리소스 그룹 및 인스턴스 이름을 비롯 한 리소스 ID <br>  *위치*:이 서비스를 실행 하는 지역 <br>  *태그*: 사용자 지정 하는 리소스 태그 <br>  *additionalInfo*: OS 버전 또는 이미지 형식을 예를 들어, 사용 된 리소스에 대 한 자세한 내용은 |
| *quantity* |이 시간 내에 발생 한 리소스 소비 |
| *meterId* |사용 된 리소스에 대 한 고유 ID (라고도 *ResourceID*) |


## <a name="next-steps"></a>다음 단계
[공급자 리소스 사용 API](azure-stack-provider-resource-api.md)

[사용 관련 FAQ](azure-stack-usage-related-faq.md)

