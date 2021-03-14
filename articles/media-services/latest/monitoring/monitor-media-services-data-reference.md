---
title: 모니터링 Media Services 데이터 참조
description: Media Services를 모니터링할 때 필요한 중요 한 참조 자료
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.date: 03/11/2021
ms.openlocfilehash: c5545ead3e4d40c7a0642aee6273c4df241bd394
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2021
ms.locfileid: "103464497"
---
# <a name="monitoring-media-services-data-reference"></a>모니터링 Media Services 데이터 참조

이 문서에서는 Media Services 모니터링에 유용한 데이터를 다룹니다. Azure Monitor에서 지원 되는 모든 플랫폼 메트릭에 대 한 자세한 내용은 [Azure Monitor를 사용 하 여 지원 되는 메트릭](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)을 검토 하세요.

## <a name="media-services-metrics"></a>Media Services 메트릭

메트릭은 값이 변경되었는지 여부와 상관 없이 정기적으로 수집됩니다. 메트릭은 자주 샘플링할 수 있고 경고는 비교적 간단한 논리를 사용하여 신속하게 발생시킬 수 있기 때문에, 메트릭은 경고에 유용합니다.

Media Services는 다음 리소스에 대 한 모니터링 메트릭을 지원 합니다.

* 계정
* 스트리밍 엔드포인트

### <a name="account"></a>계정

다음 계정 메트릭을 모니터링할 수 있습니다.

|메트릭 이름|표시 이름|설명|
|---|---|---|
|AssetCount|자산 수|계정의 자산입니다.|
|AssetQuota|자산 할당량|계정의 자산 할당량입니다.|
|AssetQuotaUsedPercentage|사용된 자산 할당량 백분율|이미 사용 된 자산 할당량의 백분율입니다.|
|ContentKeyPolicyCount|콘텐츠 키 정책 수|계정의 콘텐츠 키 정책입니다.|
|ContentKeyPolicyQuota|콘텐츠 키 정책 할당량|계정의 콘텐츠 키 정책 할당량입니다.|
|ContentKeyPolicyQuotaUsedPercentage|사용된 콘텐츠 키 정책 할당량 백분율|이미 사용 된 콘텐츠 키 정책 할당량의 백분율입니다.|
|StreamingPolicyCount|스트리밍 정책 수|계정의 스트리밍 정책.|
|StreamingPolicyQuota|스트리밍 정책 할당량|계정의 스트리밍 정책 할당량입니다.|
|StreamingPolicyQuotaUsedPercentage|사용된 스트리밍 정책 할당량 백분율|이미 사용 된 스트리밍 정책 할당량의 백분율입니다.|

[계정 할당량 및 제한](../limits-quotas-constraints.md)도 검토 해야 합니다.

### <a name="streaming-endpoint"></a>스트리밍 엔드포인트

다음 Media Services [스트리밍 끝점](/rest/api/media/streamingendpoints) 메트릭이 지원 됩니다.

|메트릭 이름|표시 이름|설명|
|---|---|---|
|요청|요청|스트리밍 끝점에서 제공 하는 총 HTTP 요청 수를 제공 합니다.|
|송신|송신|스트리밍 끝점 당 분당 총 바이트 수입니다.|
|SuccessE2ELatency|성공 엔드투엔드 대기 시간|스트리밍 끝점이 응답의 마지막 바이트를 보낸 시점에서 요청을 받은 시간입니다.|
|CPU 사용량| | 프리미엄 스트리밍 끝점에 대 한 CPU 사용량입니다. 표준 스트리밍 끝점에는이 데이터를 사용할 수 없습니다. |
|송신 대역폭 | | 초당 송신 대역폭 (비트)입니다.|

## <a name="metric-dimensions"></a>메트릭 차원

메트릭 차원에 대한 자세한 내용은 [다차원 메트릭](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics)을 참조하세요.

차원 테이블의 **자리 표시자** 입니다.

## <a name="resource-logs"></a>리소스 로그

## <a name="media-services-diagnostic-logs"></a>진단 로그 Media Services

진단 로그는 Azure 리소스 작업에 대 한 풍부 하 고 빈번한 데이터를 제공 합니다. 자세한 내용은 [Azure 리소스에서 로그 데이터를 수집 하 고 사용 하는 방법](https://docs.microsoft.com/azure/azure-monitor/essentials/platform-logs-overview.md)을 참조 하세요.

Media Services는 다음 진단 로그를 지원 합니다.

* 키 배달

### <a name="key-delivery"></a>키 배달

|이름|설명|
|---|---|
|키 배달 서비스 요청|키 배달 서비스 요청 정보를 표시 하는 로그입니다. 자세한 내용은 [스키마](monitor-media-services-data-reference.md)를 참조 하세요.|

## <a name="schemas"></a>스키마

최상위 진단 로그 스키마에 대 한 자세한 설명은 [Azure 진단 로그에 대해 지원 되는 서비스, 스키마 및 범주](https://docs.microsoft.com/azure/azure-monitor/essentials/resource-logs-schema.md)를 참조 하세요.

## <a name="key-delivery-log-schema-properties"></a>키 배달 로그 스키마 속성

이러한 속성은 키 배달 로그 스키마에만 적용 됩니다.

|이름|설명|
|---|---|
|keyId|요청 된 키의 ID입니다.|
|keyType|"Clear" (암호화 없음), "FairPlay", "PlayReady" 또는 "Widevine" 값 중 하나일 수 있습니다.|
|policyName|정책의 Azure Resource Manager 이름입니다.|
|tokenType|토큰 유형입니다.|
|statusMessage|상태 메시지입니다.|

### <a name="example"></a>예제

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
