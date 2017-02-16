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
ms.sourcegitcommit: d88fa57c2f343636d7529780dc1b27ccb604ee02
ms.openlocfilehash: 0de8128b01f760340e85078b433707c566fa2e4f


---
# <a name="custom-tracking-schemas"></a>사용자 지정 추적 스키마
기업 간(B2B) 트랜잭션을 모니터링하기 위해 Azure 통합 계정에서 사용자 지정 추적 스키마를 사용할 수 있습니다.

## <a name="custom-tracking-schema"></a>사용자 지정 추적 스키마
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

| 속성 | 형식 | 설명 |
| --- | --- | --- |
| sourceType |   | 실행 원본의 유형입니다. 허용되는 값은** Microsoft.Logic/workflows** 및 **custom**입니다. (필수) |
| 원본 |   | 소스 형식이 **Microsoft.Logic/workflows**이면 소스 정보는 이 스키마를 따라야 합니다. 소스 형식이 **custom**이면 스키마는 JToken입니다. (필수) |
| systemId | 문자열 | 논리 앱 시스템 ID입니다. (필수) |
| runId | string | 논리 앱 실행 ID입니다. (필수) |
| operationName | 문자열 | 작업의 이름(예: action 또는 trigger)입니다. (필수) |
| repeatItemScopeName | string | 작업이 `foreach`/`until` 루프에 있으면 아이템 이름을 반복합니다. (필수) |
| repeatItemIndex | Integer | 작업이 `foreach`/`until` 루프에 있는지의 여부입니다. 반복된 항목 인덱스를 나타냅니다. (필수) |
| trackingId | string | 메시지에 상호 연결할 추적 ID입니다. (선택 사항) |
| CorrelationId | 문자열 | 메시지에 상호 연결할 상관 관계 ID입니다. (선택 사항) |
| clientRequestId | string | 클라이언트는 메시지에 상호 연결하기 위해 이 항목을 채울 수 있습니다. (선택 사항) |
| eventLevel |   | 이벤트의 수준입니다. (필수) |
| eventTime |   | UTC 형식 YYYY-MM-DDTHH:MM:SS.00000Z의 이벤트 시간입니다. (필수) |
| recordType |   | 트랙 레코드의 유형입니다. 허용되는 값은 **custom**입니다. (필수) |
| record |   | 사용자 지정 레코드 유형입니다. 허용된 형식은 JToken입니다. (필수) |

## <a name="b2b-protocol-tracking-schemas"></a>B2B 프로토콜 추적 스키마
스키마를 추적하는 B2B 프로토콜에 대한 정보는 다음을 참조하세요.
* [AS2 추적 스키마](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [X12 추적 스키마](app-service-logic-track-integration-account-x12-tracking-shemas.md)

## <a name="next-steps"></a>다음 단계
* [B2B 메시지 모니터링](app-service-logic-monitor-b2b-message.md)에 대해 자세히 알아봅니다.   
* [Operations Management Suite에서 B2B 메시지 추적하기](app-service-logic-track-b2b-messages-omsportal.md)에 대해 알아봅니다.
* [엔터프라이즈 통합 팩](app-service-logic-enterprise-integration-overview.md)에 대해 자세히 알아봅니다.



<!--HONumber=Dec16_HO3-->


