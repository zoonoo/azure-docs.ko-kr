---
title: Azure의 제한 오류 문제 해결 | Microsoft Docs
description: Azure Compute의 제한 오류, 다시 시도 및 백오프.
services: virtual-machines
documentationcenter: ''
author: changov
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: vashan, rajraj, changov
ms.openlocfilehash: efa10f5beae64105857b00b186683d491edb00f5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233771"
---
# <a name="troubleshooting-api-throttling-errors"></a>API 제한 오류 문제 해결 

Azure 컴퓨팅 요청은 서비스의 전반적인 성능에 도움이 되도록 지역별로 및 구독에서 제한이 있을 수 있습니다. Microsoft.Compute 네임스페이스에서 리소스를 관리하는 Azure CRP(Compute 리소스 공급자)에 대한 모든 호출이 허용되는 최대 API 요청 속도를 초과하지 않도록 합니다. 이 문서에서는 제한받지 않도록 API 제한, 제한 문제를 해결하는 방법 및 모범 사례에 대해 설명합니다.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Azure Resource Manager 대 리소스 공급자에 의한 제한  

Azure의 정문인 Azure Resource Manager는 들어오는 모든 API 요청에 대해 인증, 1차 유효성 검사 및 제한을 수행합니다. Azure Resource Manager 호출 속도 제한 및 관련 진단 응답 HTTP 헤더가 [여기](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-request-limits)에 설명돼 있습니다.
 
Azure API 클라이언트에 제한 오류가 발생하면 HTTP 상태가 429 요청이 너무 많은 상태가 됩니다. Azure Resource Manager 또는 CRP와 같은 기본 리소스 공급자가 요청 제한을 수행하는지 파악하려면 GET 요청의 경우 `x-ms-ratelimit-remaining-subscription-reads` 및 GET이 아닌 요청의 경우 `x-ms-ratelimit-remaining-subscription-writes` 응답 헤더를 검사합니다. 남은 호출 수가 0에 이른 경우 Azure Resource Manager에서 정의한 구독의 일반 호출 제한에 도달한 것입니다. 모든 구독 클라이언트에 의한 활동은 함께 집계됩니다. 그렇지 않으면, 대상 리소스 공급자(요청 URL의 `/providers/<RP>` 세그먼트에서 주소가 지정된 리소스 공급자)가 제한을 제공합니다. 

## <a name="call-rate-informational-response-headers"></a>호출 속도 정보 응답 헤더 

| 헤더                            | 값 형식                           | 예                               | 설명                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-remaining-resource |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | 이 요청의 대상을 비롯한 리소스 버킷 또는 작업 그룹을 포함하는 제한 정책의 나머지 API 호출 수                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | 호출 수는 해당 정책의 제한에 대한 이 HTTP 요청의 경우 "청구됨"으로 계산됩니다. 이 수는 가장 일반적으로 1입니다. 가상 머신 확장 집합 크기 조정의 경우와 같이 일괄 처리 요청은 여러 개수에 요금을 청구할 수 있습니다. |


API 요청은 여러 제한 정책의 대상일 수 있습니다. 각 정책에 대한 별도의 `x-ms-ratelimit-remaining-resource` 헤더가 있습니다. 

다음은 가상 머신 확장 집합 요청을 삭제하는 응답 샘플입니다.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>제한 오류 세부 정보

429 HTTP 상태는 일반적으로 호출 속도가 제한에 도달했기 때문에 요청을 거부하는 데 사용됩니다. Compute 리소스 공급자의 일반적인 제한 오류 응답은 아래 예제와 같이 표시됩니다(관련 헤더만 표시됨).

```
HTTP/1.1 429 Too Many Requests
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet3Min;46
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet30Min;0
Retry-After: 1200
Content-Type: application/json; charset=utf-8
{
  "code": "OperationNotAllowed",
  "message": "The server rejected the request because too many requests have been received for this subscription.",
  "details": [
    {
      "code": "TooManyRequests",
      "target": "HighCostGet30Min",
      "message": "{\"operationGroup\":\"HighCostGet30Min\",\"startTime\":\"2018-06-29T19:54:21.0914017+00:00\",\"endTime\":\"2018-06-29T20:14:21.0914017+00:00\",\"allowedRequestCount\":800,\"measuredRequestCount\":1238}"
    }
  ]
}

```

나머지 호출 수가 0인 정책은 제한 오류가 반환되기 때문에 발생합니다. 이 경우에 해당 정책은 `HighCostGet30Min`입니다. 응답 본문의 전체 형식은 Azure Resource Manager API 일반 오류 형식(OData 준수)입니다. `OperationNotAllowed` 기본 오류 코드는 Compute 리소스 공급자가 다른 유형의 클라이언트 오류 중 제한 오류를 보고하기 위해 사용하는 코드입니다. 내부 오류의 `message` 속성에는 제한 위반에 대한 세부 정보가 있는 직렬화된 JSON 구조가 포함되어 있습니다.

위에 표시된 것과 같이 모든 제한 오류에는 요청을 다시 시도하기 전에 클라이언트가 대기해야 하는 최소 시간(초)을 지정하는 `Retry-After` 헤더가 포함됩니다. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>API 호출 속도 및 제한 오류 분석기
문제 해결 기능의 미리 보기 버전은 Compute 리소스 공급자의 API에 대해 사용 가능합니다. 이러한 PowerShell cmdlet은 작업당 시간 간격당 API 요청률 및 작업 그룹(정책)당 제한 위반에 대한 통계를 제공합니다.
-   [내보내기-AzLogAnalyticRequestRateByInterval](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export-AzLogAnalyticThrottledRequest](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticthrottledrequest)

API 호출 통계는 구독의 클라이언트 동작에 대한 유용한 인사이트를 제공하고 제한을 발생시키는 호출 패턴을 쉽게 식별할 수 있습니다.

당분간 분석기 제한 사항은 관리 디스크의 지원에서 디스크 및 스냅샷 리소스 형식에 대한 요청을 계산하지 않는 것입니다. CRP의 원격 분석에서 데이터를 수집하므로 ARM에서 제한 오류를 식별하도록 도울 수 없습니다. 하지만 이러한 항목은 앞에서 설명한 대로 고유한 ARM 응답 헤더에 따라 쉽게 식별할 수 있습니다.

PowerShell cmdlet은 클라이언트에서 직접 쉽게 호출될 수 있는 REST 서비스 API를 사용합니다. 하지만 정식 지원은 아직입니다. HTTP 요청 서식을 보려면 -Debug 스위치를 포함한 cmdlet을 실행하거나 Fiddler의 해당 실행을 참조합니다.


## <a name="best-practices"></a>모범 사례 

- Azure 서비스 API 오류는 무조건 및/또는 즉시 다시 시도하지 않습니다. 다시 시도할 수 없는 오류가 발생하는 경우 클라이언트 코드가 신속한 다시 시도 반복 상태로 들어가는 것은 흔히 발생하는 일입니다. 다시 시도는 대상 작업 그룹에 대한 허용된 호출 제한을 다 소진시켜서 결국 구독의 다른 클라이언트에도 영향을 미칩니다. 
- 대규모 API 자동화 사례에서 대상 작업 그룹에 대한 사용할 수 있는 호출 수가 낮은 임계값 아래로 떨어질 경우 사전에 클라이언트측에서 자체 제한을 실행하는 것이 좋습니다. 
- 비동기 작업을 추적하는 경우 Retry-After 헤더 힌트를 준수합니다. 
- 클라이언트 코드에 특정 Virtual Machine에 대한 정보가 필요한 경우 이를 포함하는 리소스 그룹 또는 전체 구독에 모든 VM을 나열한 다음, 클라이언트 쪽에서 필요한 VM을 선택하는 대신 해당 VM을 직접 쿼리합니다. 
- 클라이언트 코드에 특정 Azure 위치의 VM, 디스크 및 스냅샷이 필요한 경우 모든 구독 VM을 쿼리한 다음, 클라이언트 쪽의 위치별로 필터링하는 대신 위치 기반 형식의 쿼리를 사용합니다. 즉 Compute 리소스 공급자 지역별 엔드포인트에 대한 쿼리는 `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30`입니다. 
-   특히 API 리소스, VM 및 가상 머신 확장 집합을 만들거나 업데이트하는 경우 리소스 URL 자체(`provisioningState` 기반)에서 폴링을 수행하는 것보다 반환된 비동기 작업이 완료될 때까지 추적하는 것이 훨씬 더 효율적입니다.

## <a name="next-steps"></a>다음 단계

Azure의 기타 서비스의 경우 다시 시도 지침에 대한 자세한 내용은 [특정 서비스에 대한 다시 시도 지침](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)을 참조
