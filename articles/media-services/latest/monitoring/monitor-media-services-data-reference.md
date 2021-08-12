---
title: Media Services 모니터링 참조 데이터
description: Media Services를 모니터링할 때 필요한 중요한 참조 자료입니다.
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 04/21/2021
ms.openlocfilehash: a9bc17528e1314a033cf2e45fee4b112eb088cc3
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109715577"
---
# <a name="monitoring-media-services-data-reference"></a>Media Services 모니터링 참조 데이터

이 문서에서는 Media Services 모니터링에 유용한 데이터를 다룹니다. Azure Monitor에서 지원되는 모든 플랫폼 메트릭에 대한 자세한 내용은 [Azure Monitor에서 지원되는 메트릭](../../../azure-monitor/essentials/metrics-supported.md)을 검토하세요.

## <a name="metrics"></a>metrics

메트릭은 값이 변경되었는지 여부와 상관 없이 정기적으로 수집됩니다. 메트릭은 자주 샘플링할 수 있고 경고는 비교적 간단한 논리를 사용하여 신속하게 발생시킬 수 있기 때문에, 메트릭은 경고에 유용합니다.


Media Services는 다음 리소스에 대한 모니터링 메트릭을 지원합니다.

|메트릭 유형 | 리소스 공급자/형식 네임스페이스<br/> 및 개별 메트릭 링크 |
|-------|-----|
| Media Services 일반 | [일반](../../../azure-monitor/essentials/metrics-supported.md#microsoftmediamediaservices) |
| 라이브 이벤트 | [Microsoft.Media/mediaservices/liveEvents](../../../azure-monitor/essentials/metrics-supported.md#microsoftmediamediaservicesliveevents) 
| 스트리밍 엔드포인트 | [스트리밍 엔드포인트 REST API](/rest/api/media/streamingendpoints)와 관련된 [Microsoft.Media/mediaservices/streamingEndpoints](../../../azure-monitor/essentials/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints). 


[계정 할당량 및 제한](../limits-quotas-constraints-reference.md)도 검토해야 합니다.


## <a name="metric-dimensions"></a>메트릭 차원

메트릭 차원에 대한 자세한 내용은 [다차원 메트릭](../../../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)을 참조하세요.

미디어 서비스에는 다음과 같은 메트릭 차원이 있습니다.  지원되는 메트릭을 기반으로 하는 자체 설명입니다.  자세한 내용은 위의 [메트릭 링크](#metrics)를 참조하세요.   
- OutputFormat
- HttpStatusCode 
- 오류 코드 
- TrackName 

## <a name="resource-logs"></a>리소스 로그

리소스 로그는 Azure 리소스 작업에 관련된 다양하고 빈번한 데이터를 제공합니다. 자세한 내용은 [Azure 리소스에서 로그 데이터를 수집하고 사용하는 방법](../../../azure-monitor/essentials/platform-logs-overview.md)을 참조하세요.

Media Services는 [Microsoft.Media/mediaservices](../../../azure-monitor/essentials/resource-logs-categories.md#microsoftmediamediaservices) 리소스 로그를 지원합니다.

## <a name="schemas"></a>스키마

최상위 수준 진단 로그 스키마에 대한 자세한 설명은 [Azure 진단 로그에 지원되는 서비스, 스키마 및 범주](../../../azure-monitor/essentials/resource-logs-schema.md)를 참조하세요.

### <a name="key-delivery"></a>키 배달 

이 속성은 키 배달 로그 스키마에만 적용됩니다.

|Name|Description|
|---|---|
|keyId|요청된 키의 ID입니다.|
|keyType|“Clear”(암호화 없음), “FairPlay”, “PlayReady” 또는 “Widevine” 값 중 하나일 수 있습니다.|
|policyName|정책의 Azure Resource Manager 이름입니다.|
|tokenType|토큰 유형입니다.|
|statusMessage|상태 메시지입니다.|

### <a name="example"></a>예

키 배달 요청 스키마의 속성입니다.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

>[!NOTE]
> Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
