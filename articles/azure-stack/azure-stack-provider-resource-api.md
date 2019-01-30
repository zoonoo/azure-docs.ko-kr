---
title: 공급자 리소스 사용 API | Microsoft Docs
description: Azure Stack 사용 정보를 검색 하는 리소스 사용량 API에 대 한 참조
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
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 01/25/2018
ms.openlocfilehash: 9568ac58e646be03d367fe466ae972955f23488c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252237"
---
# <a name="provider-resource-usage-api"></a>공급자 리소스 사용 API
용어 *공급자* 모든 위임 된 공급자 및 서비스 관리자에 적용 됩니다. Azure Stack 운영자 및 위임 된 공급자 공급자 사용량 API를 사용 하 여 직접 테 넌 트의 사용량을 볼 수 있습니다. 예를 들어 다이어그램에 표시 된 것과 같이 P0 수 P1의에서 사용량 정보를 가져오는 API 공급자 호출 하 고 P1 및 P2의 직접 사용 수 P3 및 P4 사용량 정보에 대 한 합니다.

![공급자 계층 구조 개념적 모델](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>API 호출 참조
### <a name="request"></a>요청
요청 요청된 시간 프레임 및 요청 된 구독에 대 한 사용량 세부 정보를 가져옵니다. 요청 본문이 없습니다.

이 사용량 API 공급자 API 이므로 호출자가 공급자의 구독에 소유자, 참가자 또는 읽기 권한자 역할을 할당 해야 합니다.

| **메서드** | **요청 URI** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity} & subscriberId = {sub1.1} & api-버전 = 2015-06-01-미리 보기 & continuationToken = {토큰-value} |

### <a name="arguments"></a>인수
| **인수** | **설명** |
| --- | --- |
| *armendpoint* |Azure Stack 환경과의 azure Resource Manager 끝점입니다. Azure Stack 규칙은 Azure Resource Manager 끝점의 이름 형식으로 `https://adminmanagement.{domain-name}`입니다. 예를 들어 개발 키트용, 도메인 이름이 *local.azurestack.external*, Resource Manager 끝점은 `https://adminmanagement.local.azurestack.external`합니다. |
| *subId* |호출을 수행 하는 사용자의 구독 ID입니다. |
| *reportedStartTime* |쿼리 시간을 시작 합니다. 에 대 한 값 *날짜/시간* utc (협정 세계시)로 및 예를 들어 13시 시간 시작 부분에 있어야 합니다. 일별로 집계에 대 한 UTC 자정에이 값을 설정 합니다. 형식은 *이스케이프* ISO 8601 합니다. 예를 들어 *2015-06-16T18% 3a53% 3a11% 2b00 %3a00Z*콜론은 이스케이프 되 *%3a* 더하기로 이스케이프 됩니다 및 *%2b* URI 되도록 합니다. |
| *reportedEndTime* |쿼리의 종료 시간입니다. 에 적용 되는 제약 조건을 *reportedStartTime* 이 인수에도 적용 합니다. 에 대 한 값 *reportedEndTime* 또는 현재 날짜 나중에 사용할 수 없습니다. 이 경우 결과로 "처리 되지 완료 합니다." |
| *aggregationGranularity* |두 불연속 잠재적인 값이 포함 된 선택적 매개 변수: 매일, 매시간. 값 제안으로 일별 단위로 데이터를 반환 하나 고 다른 하나는 시간을 확인 합니다. 매일 옵션에는 기본값입니다. |
| *subscriberId* |구독 ID가 표시됩니다. 필터링 된 데이터를 가져오려면 공급자의 직접 테 넌 트의 구독 ID가 필요 합니다. 없는 구독 ID 매개 변수를 지정 하는 경우 호출 공급자의 모든 직접 테 넌 트에 대 한 사용 현황 데이터를 반환 합니다. |
| *api-version* |이 요청을 만드는 데 사용 되는 프로토콜의 버전입니다. 이 값으로 설정 됩니다 *2015-06-01-미리 보기*합니다. |
| *continuationToken* |토큰 마지막 사용량 API 공급자 호출에서 검색 합니다. 이 토큰 응답 1,000 줄 보다 큽니다. 진행률에 대 한 책갈피로 역할 때 필요 합니다. 토큰이 없는 경우 날의 시작 부분에서 데이터를 검색 하거나 시간, 세분성에 따라 전달 합니다. |

### <a name="response"></a>response
/Subscriptions/sub1/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 및 reportedEndTime 가져오기 = 2015-06-01T00% 3a00% 3a00% 2b00 %3a00 & aggregationGranularity 매일 subscriberId = = sub1.1 api-버전 = 1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce.Admin/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce.Admin/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
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
| *id* |사용 현황 집계의 고유 ID입니다. |
| *name* |사용 현황 집계의 이름입니다. |
| *type* |리소스 정의 합니다. |
| *subscriptionId* |Azure Stack 사용자의 구독 식별자입니다. |
| *usageStartTime* |UTC 시간인이 사용 현황 집계가 속하는 사용 현황 버킷의 시작 합니다.|
| *usageEndTime* |이 사용 현황 집계가 속하는 사용 현황 버킷의 UTC 종료 시간입니다. |
| *instanceData* |키-값 쌍을 새 형식으로 인스턴스 세부 정보:<br> *resourceUri*: 리소스 그룹 및 인스턴스 이름을 포함 하는 정규화 된 리소스 ID입니다. <br> *location*: 이 서비스를 실행 하는 영역입니다. <br> *tags*: 사용자가 지정 된 리소스 태그입니다. <br> *additionalInfo*: 리소스 사용 된 예를 들어, OS 버전 또는 이미지에 대 한 자세한 세부 정보를 입력합니다. |
| *quantity* |이 시간 내에 발생 한 리소스 사용량의 양입니다. |
| *meterId* |사용 된 리소스에 대 한 고유 ID (라고도 *ResourceID*). |


## <a name="retrieve-usage-information"></a>사용 정보를 검색 합니다.

### <a name="powershell"></a>PowerShell

사용 현황 데이터를 생성 하려면 실행 되 고 시스템을 예를 들어 사용 중인 리소스를, 활성 가상 컴퓨터를 또는 등 일부 데이터가 포함 된 저장소 계정이 있어야 합니다. 확실 하지 않은 Azure Stack Marketplace에서 실행 중인 모든 리소스, 가상 머신 (VM), 배포 및 VM 확인 여부 경우 되도록 블레이드에서 모니터링 실행 됩니다. 사용 현황 데이터를 보려면 다음 PowerShell cmdlet을 사용 합니다.

1. [Azure Stack 용 PowerShell을 설치 합니다.](azure-stack-powershell-install.md)
2. [Azure Stack 사용자 구성](user/azure-stack-powershell-configure-user.md) 또는 [Azure Stack 운영자](azure-stack-powershell-configure-admin.md) PowerShell 환경 
3. 사용 현황 데이터를 검색 하려면 사용 합니다 [Get UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) PowerShell cmdlet:
```powershell
Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
```
### <a name="rest-api"></a>REST API

Microsoft.Commerce.Admin 서비스를 호출 하 여 삭제 된 구독에 대 한 사용 정보를 수집할 수 있습니다. 

**모든 테 넌 트 사용을 반환 하려면 현재 사용자에 대 한 삭제:**

| **메서드** | **요청 URI** |
| --- | --- |
| GET | https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version= 2015-06-01-미리 보기 |

**삭제 또는 활성 테 넌 트에 대 한 사용량 반환:**

| **메서드** | **요청 URI** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={ 구독자-id} & api-버전 = 2015-06-01-미리 보기 |


## <a name="next-steps"></a>다음 단계
[테 넌 트 리소스 사용 API 참조](azure-stack-tenant-resource-usage-api.md)

[사용 관련 FAQ](azure-stack-usage-related-faq.md)
