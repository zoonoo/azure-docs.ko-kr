---
title: Azure 미디어 서비스 진단 로그 스키마 - Azure
description: 이 문서에서는 Azure 미디어 서비스 진단 로그 스키마를 보여 줍니다.
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
ms.openlocfilehash: 37baed076ca074c1d558af36649e90959a0034c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750879"
---
# <a name="diagnostic-logs-schemas"></a>진단 로그 스키마

[Azure Monitor를](../../azure-monitor/overview.md) 사용하면 응용 프로그램의 성능을 이해하는 데 도움이 되는 메트릭 및 진단 로그를 모니터링할 수 있습니다. Media Services 진단 로그를 모니터링하고 수집된 메트릭 및 로그에 대한 경고 및 알림을 만들 수 있습니다. Azure [Storage에](https://azure.microsoft.com/services/storage/)로그를 보내고, [Azure Event Hubs로](https://azure.microsoft.com/services/event-hubs/)스트리밍하고, [로그 애널리틱스로](https://azure.microsoft.com/services/log-analytics/)내보내거나, 제3자 서비스를 사용할 수 있습니다.

자세한 내용은 [Azure 모니터 메트릭](../../azure-monitor/platform/data-platform.md) 및 Azure 모니터 진단 [로그를](../../azure-monitor/platform/platform-logs-overview.md)참조하십시오.

이 문서에서는 미디어 서비스 진단 로그 스키마에 대해 설명합니다.

## <a name="top-level-diagnostic-logs-schema"></a>최상위 수준 진단 로그 스키마

최상위 진단 로그 스키마에 대한 자세한 설명은 [지원되는 서비스, 스키마 및 Azure 진단 로그에 대한 범주를](../../azure-monitor/platform/tutorial-dashboards.md)참조하십시오.

## <a name="key-delivery-log-schema"></a>키 배달 로그 스키마

### <a name="properties"></a>속성

이러한 속성은 키 배달 로그 스키마에 만해당합니다.

|이름|설명|
|---|---|
|keyId|요청된 키의 ID입니다.|
|keyType|"지우기"(암호화 없음), "페어플레이", "플레이레디" 또는 "와이드바인"의 값 중 하나가 될 수 있습니다.|
|policyName|정책의 Azure 리소스 관리자 이름입니다.|
|tokenType|토큰 유형입니다.|
|상태 메시지|상태 메시지입니다.|

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

## <a name="additional-notes"></a>추가적인 참고 사항

* Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="next-steps"></a>다음 단계

[미디어 서비스 메트릭 및 진단 로그 모니터링](media-services-metrics-diagnostic-logs.md)
