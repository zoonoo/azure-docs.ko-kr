---
title: "x12 추적 스키마 | Microsoft Docs"
description: "X12 추적 스키마에 대해 자세히 알아보기"
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 48dfc8327b7e2dfb16b0392fd6d44e83bdfb1177


---
# <a name="x12-tracking-schemas"></a>X12 추적 스키마
지원되는 X12 추적 스키마

* X12 트랜잭션 집합 추적 스키마
* X12 트랜잭션 집합 승인 추적 스키마
* X12 교환 추적 스키마
* X12 교환 승인 추적 스키마
* X12 기능 그룹 추적 스키마
* X12 기능 그룹 승인 추적 스키마

## <a name="x12-transaction-set-tracking-schema"></a>X12 트랜잭션 집합 추적 스키마
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "transactionSetControlNumber": "",
                "CorrelationMessageId": "", 
                "messageType": "",
                "isMessageFailed": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "needAk2LoopForValidMessages":  "",
                "segmentsCount": ""
            }
    }
````

| 속성 | 설명 |
| --- | --- |
| senderPartnerName |선택적 속성. string  X12 메시지 전송 파트너 이름을 나타냅니다. |
| receiverPartnerName |선택적 속성. string  X12 메시지 수신 파트너 이름을 나타냅니다. |
| senderQualifier |필수 속성. string  송신 파트너 한정자를 나타냅니다. |
| senderIdentifier |필수 속성. string  송신 파트너 식별자를 나타냅니다. |
| receiverQualifier |필수 속성. string  수신 파트너 한정자를 나타냅니다. |
| receiverQualifier |필수 속성. string  수신 파트너 식별자를 나타냅니다. |
| agreementName |선택적 속성. string  메시지가 확인되는 X12 규약의 이름 |
| direction |필수 열거형입니다.  메시지 흐름의 방향을 나타냅니다.  허용되는 값은 receive 또는 send입니다. |
| interchangeControlNumber |선택적 속성. string  교환 컨트롤 번호를 나타냅니다. |
| functionalGroupControlNumber |선택적 속성. string  기능 컨트롤 번호를 나타냅니다. |
| transactionSetControlNumber |선택적 속성. string  트랜잭션 집합 컨트롤 번호를 나타냅니다. |
| CorrelationMessageId |선택적 속성. string  상관 관계 메시지 ID를 나타냅니다.  상관 관계 ID는 {AgreementName}*{GroupControlNumber}*{TransactionSetControlNumber} 조합입니다. |
| messageType |선택적 속성. string 트랜잭션 집합 또는 문서 형식을 나타냅니다. |
| isMessageFailed |필수 속성. boolean  X12 메시지가 성공인지 또는 실패인지를 나타냅니다. |
| isTechnicalAcknowledgmentExpected |필수 속성. boolean  X12 규약에서 기술 승인이 구성되었는지 여부를 나타냅니다. |
| isFunctionalAcknowledgmentExpected |필수 속성. boolean  X12 규약에서 기능 승인이 구성되었는지 여부를 나타냅니다. |
| needAk2LoopForValidMessages |필수 속성. boolean  - 유효한 메시지에 AK2 루프가 필요한지 여부 - 형식 boolean |
| segmentsCount |선택적 정수입니다.  X12 트랜잭션 집합의 세그먼트의 수를 나타냅니다. |

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 트랜잭션 집합 승인 추적 스키마
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "respondingtransactionSetControlNumber": "",
                "respondingTransactionSetId": "",
                "statusCode": "",
                "processingStatus": "",
                "CorrelationMessageId": ""
                "isMessageFailed": "",
                "ak2Segment": "",
                "ak3Segment": "",
                "ak5Segment": ""
            }
    }
````

| 속성 | 설명 |
| --- | --- |
| senderPartnerName |선택적 속성. string  X12 메시지 보낸 사람의 파트너 이름을 나타냅니다. |
| receiverPartnerName |선택적 속성. string  X12 메시지 받는 사람의 파트너 이름을 나타냅니다. |
| senderQualifier |필수 속성. string  송신 파트너 한정자를 나타냅니다. |
| senderIdentifier |필수 속성. string  송신 파트너 식별자를 나타냅니다. |
| receiverQualifier |필수 속성. string  수신 파트너 한정자를 나타냅니다. |
| receiverQualifier |필수 속성. string  수신 파트너 식별자를 나타냅니다. |
| agreementName |선택적 속성. string  메시지가 확인되는 X12 규약의 이름 |
| direction |필수 열거형입니다.  메시지 흐름의 방향을 나타냅니다.  허용되는 값은 receive 또는 send입니다. |
| interchangeControlNumber |선택적 속성. string  기능 승인의 교환 컨트롤 번호를 나타냅니다. 이 값은 파트너로 전송된 메시지에 대해 기능 승인이 수신된 전송 쪽에서만 채워집니다. |
| functionalGroupControlNumber |선택적 속성. string  기능 승인의 기능 그룹 컨트롤 번호를 나타냅니다. 이 값은 파트너로 전송된 메시지에 대해 기능 승인이 수신된 전송 쪽에서만 채워집니다. |
| isaSegment |선택적 속성. string  메시지의 ISA 세그먼트를 나타냅니다. 이 값은 파트너로 전송된 메시지에 대해 기능 승인이 수신된 전송 쪽에서만 채워집니다. |
| gsSegment |선택적 속성. string  메시지의 GS 세그먼트를 나타냅니다. 이 값은 파트너로 전송된 메시지에 대해 기능 승인이 수신된 전송 쪽에서만 채워집니다. |
| respondingfunctionalGroupControlNumber |선택적 속성. string  응답 교환 컨트롤 번호를 나타냅니다. |
| respondingFunctionalGroupId |선택적 속성. string 승인에서 AK101에 매핑되는 응답 기능 그룹 ID를 나타냅니다. |
| respondingtransactionSetControlNumber |선택적 속성. string  응답 트랜잭션 집합 컨트롤 번호를 나타냅니다. |
| respondingTransactionSetId |선택적 속성. string  승인에서 AK201에 매핑되는 응답 트랜잭션 집합 ID를 나타냅니다. |
| statusCode |필수 속성. boolean  트랜잭션 집합 승인 상태 코드를 나타냅니다. |
| segmentsCount |필수 열거형입니다.  승인 상태 코드를 나타냅니다.  허용되는 값은 Accepted, Rejected 또는 AccpetedWithErrros입니다. |
| processingStatus |필수 열거형입니다.  승인의 처리 상태를 나타냅니다.  허용되는 값은 Received, Generated 또는 Sent입니다. |
| CorrelationMessageId |선택적 속성. string  상관 관계 메시지 ID를 나타냅니다.  상관 관계 ID는 {AgreementName}*{GroupControlNumber}*{TransactionSetControlNumber} 조합입니다. |
| isMessageFailed |필수 속성. boolean  X12 메시지가 성공인지 또는 실패인지를 나타냅니다. |
| ak2Segment |선택적 속성. string ak2 세그먼트를 나타냅니다. ak2 세그먼트는 수신된 기능 그룹 내의 트랜잭션 집합에 대한 승인을 나타냅니다. |
| ak3Segment |선택적 속성. string ak3 세그먼트를 나타냅니다.  ak3 세그먼트는 데이터 세그먼트의 오류를 보고합니다. |
| ak5Segment |선택적 속성. string ak5 세그먼트를 나타냅니다.  ak5 세그먼트는 AK2 세그먼트에 식별된 트랜잭션 집합이 허용되는지 또는 거부되는지와 그 이유를 보고합니다. |

## <a name="x12-interchange-tracking-schema"></a>X12 교환 추적 스키마
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "isa09": "",
                "isa10": "",
                "isa11": "",
                "isa12": "",
                "isa14": "",
                "isa15": "",
                "isa16": ""
            }
    }
````

| 속성 | 설명 |
| --- | --- |
| senderPartnerName |선택적 속성. string  X12 메시지 보낸 사람의 파트너 이름을 나타냅니다. |
| receiverPartnerName |선택적 속성. string  X12 메시지 받는 사람의 파트너 이름을 나타냅니다. |
| senderQualifier |필수 속성. string  송신 파트너 한정자를 나타냅니다. |
| senderIdentifier |필수 속성. string  송신 파트너 식별자를 나타냅니다. |
| receiverQualifier |필수 속성. string  수신 파트너 한정자를 나타냅니다. |
| receiverQualifier |필수 속성. string  수신 파트너 식별자를 나타냅니다. |
| agreementName |선택적 속성. string  메시지가 확인되는 X12 규약의 이름 |
| direction |필수 열거형입니다.  메시지 흐름의 방향을 나타냅니다.  허용되는 값은 receive 또는 send입니다. |
| interchangeControlNumber |선택적 속성. string  교환 컨트롤 번호를 나타냅니다. |
| isaSegment |선택적 속성. string  메시지 ISA 세그먼트를 나타냅니다. |
| isTechnicalAcknowledgmentExpected |필수 속성. boolean  X12 규약에서 기술 승인이 구성되었는지 여부를 나타냅니다. |
| isMessageFailed |필수 속성. boolean  X12 메시지가 성공인지 또는 실패인지를 나타냅니다. |
| isa09 |선택적 속성. string  X12 문서 교환 날짜를 나타냅니다. |
| isa10 |선택적 속성. string X12 문서 교환 시간을 나타냅니다. |
| isa11 |선택적 속성. string X12 교환 컨트롤 표준 식별자를 나타냅니다. |
| isa12 |선택적 속성. string  X12 교환 컨트롤 버전 번호를 나타냅니다. |
| isa14 |선택적 속성. string  X12 승인이 요청됨을 나타냅니다. |
| isa15 |선택적 속성. string  테스트 또는 프로덕션에 대한 표시기를 나타냅니다. |
| isa16 |선택적 속성. string 요소 구분 기호를 나타냅니다. |

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 교환 승인 추적 스키마
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "respondingInterchangeControlNumber": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ta102": "",
                "ta103": "",
                "ta105": ""
            }
    }
````

| 속성 | 설명 |
| --- | --- |
| senderPartnerName |선택적 속성. string  X12 메시지 보낸 사람의 파트너 이름을 나타냅니다. |
| receiverPartnerName |선택적 속성. string  X12 메시지 받는 사람의 파트너 이름을 나타냅니다. |
| senderQualifier |필수 속성. string  송신 파트너 한정자를 나타냅니다. |
| senderIdentifier |필수 속성. string  송신 파트너 식별자를 나타냅니다. |
| receiverQualifier |필수 속성. string  수신 파트너 한정자를 나타냅니다. |
| receiverQualifier |필수 속성. string  수신 파트너 식별자를 나타냅니다. |
| agreementName |선택적 속성. string  메시지가 확인되는 X12 규약의 이름 |
| direction |필수 열거형입니다.  메시지 흐름의 방향을 나타냅니다.  허용되는 값은 receive 또는 send입니다. |
| interchangeControlNumber |선택적 속성. string  기술 승인의 교환 컨트롤 번호를 나타냅니다. 파트너로부터 수신되는 기술 승인은 이 값을 갖습니다. |
| isaSegment |선택적 속성. string  기술 승인에 대한 ISA 세그먼트를 나타냅니다. 파트너로부터 수신되는 기술 승인은 이 값을 갖습니다. |
| respondingInterchangeControlNumber |선택적 속성. string  기술 승인에 대한 교환 컨트롤 번호를 나타냅니다. 파트너로부터 수신되는 기술 승인은 이 값을 갖습니다. |
| isMessageFailed |필수 속성. boolean  X12 메시지가 성공인지 또는 실패인지를 나타냅니다. |
| statusCode |필수 열거형입니다.  교환 승인 상태 코드를 나타냅니다.  허용되는 값은 Accepted/Rejected/AccpetedWithErrros입니다. |
| processingStatus |필수 열거형입니다.  승인 상태를 나타냅니다.  허용되는 값은 Received/Generated/Sent입니다. |
| ta102 |선택적 속성. string 교환 날짜를 나타냅니다. |
| ta103 |선택적 속성. string 교환 시간을 나타냅니다. |
| ta105 |선택적 속성. string 교환 노트 코드를 나타냅니다. |

## <a name="x12-functional-group-tracking-schema"></a>X12 기능 그룹 추적 스키마
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "gsSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "gs01": "",
                "gs02": "",
                "gs03": "",
                "gs04": "",
                "gs05": "",
                "gs07": "",
                "gs08": ""
            }
    }
````

| 속성 | 설명 |
| --- | --- |
| senderPartnerName |선택적 속성. string  X12 메시지 보낸 사람의 파트너 이름을 나타냅니다. |
| receiverPartnerName |선택적 속성. string  X12 메시지 받는 사람의 파트너 이름을 나타냅니다. |
| senderQualifier |필수 속성. string  송신 파트너 한정자를 나타냅니다. |
| senderIdentifier |필수 속성. string  송신 파트너 식별자를 나타냅니다. |
| receiverQualifier |필수 속성. string  수신 파트너 한정자를 나타냅니다. |
| receiverQualifier |필수 속성. string  수신 파트너 식별자를 나타냅니다. |
| agreementName |선택적 속성. string  메시지가 확인되는 X12 규약의 이름 |
| direction |필수 열거형입니다.  메시지 흐름의 방향을 나타냅니다.  허용되는 값은 receive 또는 send입니다. |
| interchangeControlNumber |선택적 속성. string 교환 컨트롤 번호를 나타냅니다. |
| functionalGroupControlNumber |선택적 속성 - 기능 컨트롤 번호 - 형식 문자열 |
| gsSegment |선택적 속성. string  메시지 GS 세그먼트를 나타냅니다. |
| isTechnicalAcknowledgmentExpected |필수 속성. boolean  X12 규약에서 기술 승인이 구성되었는지 여부를 나타냅니다. |
| isFunctionalAcknowledgmentExpected |필수 속성. boolean  X12 규약에서 기능 승인이 구성되었는지 여부를 나타냅니다. |
| isMessageFailed |필수 속성. boolean  X12 메시지가 성공인지 또는 실패인지를 나타냅니다. |
| gs01 |선택적 속성. string 기능 식별자 코드를 나타냅니다. |
| gs02 |선택적 속성. string 응용 프로그램 보낸 사람의 코드를 나타냅니다. |
| gs03 |선택적 속성. string 응용 프로그램 받는 사람의 코드를 나타냅니다. |
| gs04 |선택적 속성. string 기능 그룹 날짜를 나타냅니다. |
| gs05 |선택적 속성. string 기능 그룹 시간을 나타냅니다. |
| gs07 |선택적 속성. string 담당 에이전시 코드를 나타냅니다. |
| gs08 |선택적 속성. string 버전/릴리스/산업 식별자 코드를 나타냅니다. - 형식 문자열 |

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12 기능 그룹 승인 추적 스키마
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ak903": "",
                "ak904": "",
                "ak9Segment": ""
            }
    }
````

| 속성 | 설명 |
| --- | --- |
| senderPartnerName |선택적 속성. string  X12 메시지 보낸 사람의 파트너 이름을 나타냅니다. |
| receiverPartnerName |선택적 속성. string  X12 메시지 받는 사람의 파트너 이름을 나타냅니다. |
| senderQualifier |필수 속성. string  송신 파트너 한정자를 나타냅니다. |
| senderIdentifier |필수 속성. string  송신 파트너 식별자를 나타냅니다. |
| receiverQualifier |필수 속성. string  수신 파트너 한정자를 나타냅니다. |
| receiverQualifier |필수 속성. string  수신 파트너 식별자를 나타냅니다. |
| agreementName |선택적 속성. string  메시지가 확인되는 X12 규약의 이름 |
| direction |필수 열거형입니다.  메시지 흐름의 방향을 나타냅니다.  허용되는 값은 receive 또는 send입니다. |
| interchangeControlNumber |선택적 속성. string 교환 컨트롤 번호를 나타냅니다. 기술 승인이 파트너로부터 수신될 때 송신 쪽에 대해 채워집니다. |
| functionalGroupControlNumber |선택적 속성. string 기술 승인의 기능 그룹 컨트롤 번호를 나타냅니다. 기술 승인이 파트너로부터 수신될 때 송신 쪽에 대해 채워집니다. |
| isaSegment |선택적 속성 - 위와 마찬가지로 교환 컨트롤 번호는 특정 경우에만 채워집니다. -형식 string |
| gsSegment |선택적 속성 - 위와 마찬가지로 기능 그룹 컨트롤 번호는 특정 경우에만 채워집니다. -형식 string |
| respondingfunctionalGroupControlNumber |선택적 속성 - 원래 기능 그룹의 컨트롤 번호 - 형식 string |
| respondingFunctionalGroupId |선택적 속성 - ack의 AK101에 매핑 - 기능 그룹 ID - 형식 string |
| isMessageFailed |필수 속성. boolean  X12 메시지가 성공인지 또는 실패인지를 나타냅니다. |
| statusCode |필수 열거형입니다.  승인 상태 코드를 나타냅니다. 허용되는 값은 Accepted/Rejected/AccpetedWithErrros입니다. |
| processingStatus |필수 열거형입니다.  승인의 처리 상태를 나타냅니다. 허용되는 값은 Received/Generated/Sent입니다. |
| ak903 |선택적 속성. string 수신된 트랜잭션 집합의 수를 나타냅니다. |
| ak904 |선택적 속성. string 식별된 기능 그룹에서 승인된 트랜잭션 집합 수를 나타냅니다. |
| ak9Segment |선택적 속성. string  Ak9 세그먼트는 AK1 세그먼트에 식별된 기능 그룹이 허용되는지 또는 거부되는지와 그 이유를 나타냅니다. |

## <a name="next-steps"></a>다음 단계
[B2B 메시지 모니터링에 대해 자세히 알아보기](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[OMS 포털에서 B2B 메시지 추적](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[사용자 지정 추적 스키마에 대해 자세히 알아보기](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Schema")   
[AS2 추적 스키마에 대해 자세히 알아보기](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Schema")   
[엔터프라이즈 통합 팩에 대해 자세히 알아보기](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")  




<!--HONumber=Nov16_HO3-->


