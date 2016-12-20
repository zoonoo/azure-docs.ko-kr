---
title: "사용자 지정 추적 스키마 | Microsoft Docs"
description: "사용자 지정 추적 스키마에 대해 자세히 알아보기"
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: f846f23bef61e8e772920196b9c81d059546d8a6


---
# <a name="custom-tracking-schema"></a>사용자 지정 추적 스키마
## <a name="supported-custom-tracking-schema"></a>지원되는 사용자 지정 추적 스키마
````java

        {
            "sourceType": "",
            "source": { 

            "workflow": {
                "systemId": ""
            },
            "runInstance": {
                "runId": ""
            },
            "operation": {
                "operationName": "",
                "repeatItemScopeName": "",
                "repeatItemIndex": "",
                "trackingId": "",
                "correlationId": "",
                "clientRequestId": ""
                }
            },
            "events": [
            {
                "eventLevel": "",
                "eventTime": "",
                "recordType": "",
                "record": {                
                }
            }
         ]
      }

````

| 속성 | 설명 |
| --- | --- |
| sourceType |필수.  실행 소스의 형식을 나타냅니다. 허용되는 값은 Microsoft.Logic/workflows 또는 custom입니다. |
| 원본 |필수 속성입니다. 소스 형식이 Microsoft.Logic/workflows이면 소스 정보는 이 스키마를 따라야 합니다. 소스 형식이 'custom'이면 스키마는 JToken입니다.  |
| systemId |필수 속성. string  논리 앱 시스템 ID를 나타냅니다. |
| runId |필수 속성. string  논리 앱 실행 ID를 나타냅니다. |
| operationName |필수 속성. string  작업의 이름(예: action 또는 trigger)을 나타냅니다. |
| repeatItemScopeName |필수 속성. string 작업이 foreach/until 루프 내에 있으면 반복 항목 이름을 나타냅니다. |
| repeatItemIndex |필수 정수입니다.  작업이 foreach/until 루프 내에 있으면 반복되는 항목 인덱스를 나타냅니다.  |
| trackingId |선택적 속성. string 메시지에 상호 연결할 추적 ID를 나타냅니다. |
| CorrelationId |선택적 속성. string 메시지에 상호 연결할 상관 관계 ID를 나타냅니다. |
| clientRequestId |선택적 속성. string  클라이언트는 메시지에 상호 연결하기 위해 이 항목을 채울 수 있습니다. |
| eventLevel |필수. 이벤트의 수준을 나타냅니다. |
| eventTime |필수. UTC 형식 YYYY-MM-DDTHH:MM:SS.00000Z의 이벤트 시간을 나타냅니다. |
| recordType |필수 속성입니다. 추적 레코드의 형식을 나타냅니다. 허용되는 값은 Custom입니다. |
| record |필수.  사용자 지정 레코드 형식을 나타내며 허용되는 형식은 JToken입니다. |
|  | |

## <a name="supported-b2b-protocol-tracking-schemas"></a>지원되는 B2B 프로토콜 추적 스키마
* [AS2 추적 스키마](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [X12 추적 스키마](app-service-logic-track-integration-account-x12-tracking-shemas.md) 

## <a name="next-steps"></a>다음 단계
[B2B 메시지 모니터링에 대해 자세히 알아보기](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[OMS 포털에서 B2B 메시지 추적](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")   




<!--HONumber=Nov16_HO3-->


