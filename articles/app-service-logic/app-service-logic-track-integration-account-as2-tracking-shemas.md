---
title: "as2 추적 스키마 | Microsoft Docs"
description: "as2 추적 스키마에 대한 자세한 정보"
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 1fd5bd1038be6c35cf5fc05c9cfa000d818c5e70


---

# <a name="as2-tracking-schemas"></a>AS2 추적 스키마
지원되는 AS2 추적 스키마

* AS2 메시지 추적 스키마
* AS2 MDN 추적 스키마

## <a name="as2-message-tracking-schema"></a>AS2 메시지 추적 스키마
````java

    {
       "agreementProperties": {  
            "senderPartnerName": "",  
            "receiverPartnerName": "",  
            "as2To": "",  
            "as2From": "",  
            "agreementName": ""  
        },  
        "messageProperties": {
            "direction": "",
            "messageId": "",
            "dispositionType": "",
            "fileName": "",
            "isMessageFailed": "",
            "isMessageSigned": "",
            "isMessageEncrypted": "",
            "isMessageCompressed": "",
            "correlationMessageId": "",
            "incomingHeaders": {
            },
            "outgoingHeaders": {
            },
        "isNrrEnabled": "",
        "isMdnExpected": "",
        "mdnType": ""
        }
    }
````

| 속성 | 설명 |
| --- | --- |
| senderPartnerName |선택적 속성. string  AS2 메시지 보낸 사람의 파트너 이름을 나타냅니다. |
| receiverPartnerName |선택적 속성. string  AS2 메시지 받는 사람의 파트너 이름을 나타냅니다. |
| as2To |필수 속성. string  AS2 메시지의 헤더에 포함된 AS2 메시지 받는 사람의 이름을 나타냅니다. |
| as2From |필수 속성. string AS2 메시지의 헤더에 포함된 AS2 메시지 보낸 사람의 이름을 나타냅니다. |
| agreementName |선택적 속성. string  메시지가 확인되는 AS2 규약의 이름 |
| direction |필수 속성. string  메시지 흐름의 방향을 receive 또는 send로 나타냅니다. |
| messageId |선택적 속성. string  AS2 메시지의 헤더에 포함된 AS2 메시지 ID를 나타냅니다. |
| dispositionType |선택적 속성. string MDN 처리 형식 값을 나타냅니다. |
| fileName |선택적 속성. string  AS2 메시지의 헤더에 포함된 파일 이름을 나타냅니다. |
| isMessageFailed |필수 속성. boolean  AS2 메시지의 실패 여부를 나타냅니다. |
| isMessageSigned |필수 속성. boolean  AS2 메시지의 서명 여부를 나타냅니다. |
| isMessageEncrypted |필수 속성. boolean AS2 메시지의 암호화 여부를 나타냅니다. |
| isMessageCompressed |필수 속성. boolean AS2 메시지의 압축 여부를 나타냅니다. |
| correlationMessageId |선택적 속성. string MDN과 메시지와 상호 연결할 AS2messageid를 나타냅니다. |
| incomingHeaders |선택적 속성. JToken의 사전입니다.  들어오는 AS2 메시지 헤더 정보를 나타냅니다. |
| outgoingHeaders |선택적 속성. JToken의 사전입니다.  나가는 AS2 메시지 헤더 정보를 나타냅니다. |
| isNrrEnabled |필수 속성. boolean  값을 알 수 없는 경우 기본값을 사용합니다. |
| isMdnExpected |필수 속성. boolean 값을 알 수 없는 경우 기본값을 사용합니다. |
| mdnType |필수 열거형입니다. 허용되는 값은 NotConfigured, Sync 또는 Async입니다. |

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN 추적 스키마
````java

    {
        "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "as2To": "",
                "as2From": "",
                "agreementName": "g"
            },
            "messageProperties": {
                "direction": "",
                "messageId": "",
                "originalMessageId": "",
                "dispositionType": "",
                "isMessageFailed": "",
                "isMessageSigned": "",
                "isNrrEnabled": "",
                "statusCode": "",
                "micVerificationStatus": "",
                "correlationMessageId": "",
                "incomingHeaders": { 
                },
                "outgoingHeaders": { 
                }
            }
    }
````

| 속성 | 설명 |
| --- | --- |
| senderPartnerName |선택적 속성. string AS2 메시지 보낸 사람의 파트너 이름을 나타냅니다. |
| receiverPartnerName |선택적 속성. string AS2 메시지 받는 사람의 파트너 이름을 나타냅니다. |
| as2To |필수 속성. string 해당 AS2 메시지를 받는 파트너 이름을 나타냅니다. |
| as2From |필수 속성. string 해당 AS2 메시지를 보내는 파트너 이름을 나타냅니다. |
| agreementName |선택적 속성. string 메시지가 확인되는 AS2 규약의 이름 |
| direction |필수 속성. string 메시지 흐름의 방향을 receive 또는 send로 나타냅니다. |
| messageId |선택적 속성. string AS2 메시지 ID를 나타냅니다. |
| OriginalMessageId |선택적 속성. string AS2 원본 메시지 ID를 나타냅니다. |
| dispositionType |선택적 속성. string MDN 처리 형식 값을 나타냅니다. |
| isMessageFailed |필수 속성. boolean AS2 메시지의 실패 여부를 나타냅니다. |
| isMessageSigned |필수 속성. boolean AS2 메시지의 서명 여부를 나타냅니다. |
| isNrrEnabled |필수 속성. boolean  값을 알 수 없는 경우 기본값을 사용합니다. |
| statusCode |필수 열거형입니다.  허용되는 값은 Accepted, Rejected 또는 AcceptedWithErrros입니다. |
| micVerificationStatus |필수 열거형입니다.  허용되는 값은 NotApplicable, Succeeded 또는 Failed입니다. |
| correlationMessageId |선택적 속성. string  상관 관계 ID를 나타냅니다.  상관 관계 ID는 원본 메시지 ID(MDN이 구성된 메시지의 메시지 ID)입니다. |
| incomingHeaders |선택적 속성. JToken의 사전입니다.  들어오는 메시지 헤더 정보를 나타냅니다. |
| outgoingHeaders |선택적 속성. JToken의 사전입니다.  나가는 메시지 헤더 정보를 나타냅니다. |

## <a name="next-steps"></a>다음 단계
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")    
[B2B 메시지 모니터링에 대해 자세히 알아보기](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[OMS 포털에서 B2B 메시지 추적](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[B2B 사용자 지정 추적 스키마에 대해 자세히 알아보기](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Schema")   
[X12 추적 스키마에 대해 자세히 알아보기](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")   
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")  




<!--HONumber=Nov16_HO3-->


