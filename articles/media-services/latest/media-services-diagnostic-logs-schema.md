---
title: Azure Media Services에 진단 로그 스키마-Azure
description: 이 문서에서는 Azure Media Services 진단 로그 스키마를 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 394370738bc7996a221300540e68404986d91310
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322253"
---
# <a name="diagnostic-logs-schemas"></a>진단 로그 스키마

[Azure Monitor](../../azure-monitor/overview.md) 응용 프로그램을 수행 하는 방법을 이해할 수 있도록 진단 로그 및 메트릭 모니터링 하는 사용 하도록 설정 합니다. Media Services에 대 한 진단 정보를 모니터링 하 고 수집 된 메트릭 및 로그에 대 한 알림과 경고를 만들 수 있습니다. 로그를 보낼 수 있습니다 [Azure Storage](https://azure.microsoft.com/services/storage/)로 스트리밍하 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)를 내보낼 [Log Analytics](https://azure.microsoft.com/services/log-analytics/), 또는 타사 서비스를 사용 합니다.

자세한 내용은 [Azure Monitor 메트릭](../../azure-monitor/platform/data-platform.md) 하 고 [Azure Monitor 진단 로그](../../azure-monitor/platform/diagnostic-logs-overview.md)합니다.

이 문서에서는 Media Services 진단 로그 스키마를 설명 합니다.

## <a name="top-level-diagnostic-logs-schema"></a>최상위 수준 진단 로그 스키마

최상위 수준 진단 로그 스키마의 자세한 설명을 참조 하세요 [Azure 진단 로그에 대 한 서비스, 스키마 및 범주를 지 원하는](../../azure-monitor/platform/tutorial-dashboards.md)합니다.

## <a name="key-delivery-log-schema"></a>키 배달 로그 스키마

### <a name="properties"></a>properties

이러한 속성은 키 배달 로그 스키마와 관련이 있습니다.

|Name|설명|
|---|---|
|keyId|요청된 된 키의 ID입니다.|
|keyType|다음 값 중 하나일 수 있습니다. 암호화 되지 않은 "지우기", "FairPlay", "PlayReady" 또는 "Widevine"|
|policyName|Azure Resource Manager 이름 정책입니다.|
|tokenType|토큰 형식입니다.|
|statusMessage|상태 메시지입니다.|

### <a name="examples"></a>예

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

## <a name="next-steps"></a>다음 단계

[Media Services 메트릭 및 진단 로그를 모니터링 합니다.](media-services-metrics-diagnostic-logs.md)
