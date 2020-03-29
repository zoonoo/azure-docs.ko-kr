---
title: B2B 메시지에 대한 X12 추적 스키마
description: Azure 논리 앱에 대한 X12 메시지를 모니터링하는 추적 스키마 만들기
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905292"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Azure 논리 앱에서 X12 메시지를 추적하기 위한 스키마 만들기

기업 간(B2B) 트랜잭션의 성공, 오류 및 메시지 속성을 모니터링하려면 통합 계정에 X12 추적 스키마를 사용하면 됩니다.

* X12 트랜잭션 집합 추적 스키마
* X12 트랜잭션 집합 승인 추적 스키마
* X12 교환 추적 스키마
* X12 교환 승인 추적 스키마
* X12 기능 그룹 추적 스키마
* X12 기능 그룹 승인 추적 스키마

## <a name="x12-transaction-set-tracking-schema"></a>X12 트랜잭션 집합 추적 스키마

```json
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
      "needAk2LoopForValidMessages": "",
      "segmentsCount": ""
   }
}
```

| 속성 | 필수 | Type | Description |
|----------|----------|------|-------------|
| senderPartnerName | 예 | String | X12 메시지 발신자의 파트너 이름 |
| receiverPartnerName | 예 | String | X12 메시지 수신자 파트너 이름 |
| senderQualifier | yes | String | 파트너 한정자 보내기 |
| senderIdentifier | yes | String | 파트너 식별자 보내기 |
| receiverQualifier | yes | String | 파트너 한정자 받기 |
| receiverQualifier | yes | String | 파트너 식별자 받기 |
| agreementName | 예 | String | 메시지가 확인되는 X12 규약의 이름 |
| direction | yes | 열거형 | 메시지 흐름의 방향(또는 `receive``send` |
| interchangeControlNumber | 예 | String | 교환 컨트롤 번호 |
| functionalGroupControlNumber | 예 | String | 기능 제어 번호 |
| transactionSetControlNumber | 예 | String | 트랜잭션 집합 컨트롤 번호입니다. |
| CorrelationMessageId | 예 | String | {AgreementName}{ 그룹컨트롤넘버}{트랜잭션집합컨트롤넘번}의 조합인 상관 관계 메시지 ID}*GroupControlNumber* |
| messageType | 예 | String | 트랜잭션 집합 또는 문서 유형 |
| isMessageFailed | yes | 부울 | X12 메시지가 실패했는지 여부 |
| isTechnicalAcknowledgmentExpected | yes | 부울 | 기술 승인이 X12 계약에 구성되었는지 여부 |
| isFunctionalAcknowledgmentExpected | yes | 부울 | X12 규약에서 기능 승인이 구성되었는지 여부 |
| needAk2LoopForValidMessages | yes | 부울 | 유효한 메시지에 AK2 루프가 필요한지 여부 |
| segmentsCount | 예 | 정수 | X12 트랜잭션 집합의 세그먼트 수 |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>X12 트랜잭션 집합 승인 추적 스키마

```json
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
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| 속성 | 필수 | Type | Description |
|----------|----------|------|-------------|
| senderPartnerName | 예 | String | X12 메시지 발신자의 파트너 이름 |
| receiverPartnerName | 예 | String | X12 메시지 수신자 파트너 이름 |
| senderQualifier | yes | String | 파트너 한정자 보내기 |
| senderIdentifier | yes | String | 파트너 식별자 보내기 |
| receiverQualifier | yes | String | 파트너 한정자 받기 |
| receiverQualifier | yes | String | 파트너 식별자 받기 |
| agreementName | 예 | String | 메시지가 확인되는 X12 규약의 이름 |
| direction | yes | 열거형 | 메시지 흐름의 방향(또는 `receive``send` |
| interchangeControlNumber | 예 | String | 기능 승인의 교환 제어 번호입니다. 이 값은 파트너에게 보낸 메시지에 대해 기능 승인이 수신되는 송신 측에만 채워집니다. |
| functionalGroupControlNumber | 예 | String | 기능 적 승인의 기능 그룹 제어 번호입니다. 파트너에게 보낸 메시지에 대한 기능 승인이 수신되는 송신 측에대해서만 값이 채워집니다. |
| isaSegment | 예 | String | 메시지의 ISA 세그먼트. 파트너에게 보낸 메시지에 대한 기능 승인이 수신되는 송신 측에대해서만 값이 채워집니다. |
| gsSegment | 예 | String | 메시지의 GS 세그먼트. 파트너에게 보낸 메시지에 대한 기능 승인이 수신되는 송신 측에대해서만 값이 채워집니다. |
| respondingfunctionalGroupControlNumber | 예 | String | 응답 교환 제어 번호 |
| respondingFunctionalGroupId | 예 | String | 승인시 AK101에 매핑되는 응답 기능 그룹 ID |
| respondingtransactionSetControlNumber | 예 | String | 응답 트랜잭션 집합 제어 번호 |
| respondingTransactionSetId | 예 | String | 승인에서 AK201에 매핑되는 응답 트랜잭션 세트 ID |
| statusCode | yes | 부울 | 트랜잭션 집합 승인 상태 코드 |
| segmentsCount | yes | 열거형 | 이러한 허용된 값을 사용하여 상태 `Accepted` `Rejected`코드를 승인합니다.`AcceptedWithErrors` |
| processingStatus | yes | 열거형 | 이러한 허용된 값으로 승인 상태를 `Received`처리합니다. `Generated``Sent` |
| CorrelationMessageId | 예 | String | {AgreementName}{ 그룹컨트롤넘버}{트랜잭션집합컨트롤넘번}의 조합인 상관 관계 메시지 ID}*GroupControlNumber* |
| isMessageFailed | yes | 부울 | X12 메시지가 실패했는지 여부 |
| ak2Segment | 예 | String | 수신된 기능 그룹 내의 트랜잭션 집합에 대한 승인 |
| ak3Segment | 예 | String | 데이터 세그먼트의 오류 보고 |
| ak5Segment | 예 | String | AK2 세그먼트에서 식별된 트랜잭션 집합이 수락 또는 거부되는지 여부와 그 이유를 보고합니다. |
|||||

## <a name="x12-interchange-tracking-schema"></a>X12 교환 추적 스키마

```json
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
```

| 속성 | 필수 | Type | Description |
|----------|----------|------|-------------|
| senderPartnerName | 예 | String | X12 메시지 발신자의 파트너 이름 |
| receiverPartnerName | 예 | String | X12 메시지 수신자 파트너 이름 |
| senderQualifier | yes | String | 파트너 한정자 보내기 |
| senderIdentifier | yes | String | 파트너 식별자 보내기 |
| receiverQualifier | yes | String | 파트너 한정자 받기 |
| receiverQualifier | yes | String | 파트너 식별자 받기 |
| agreementName | 예 | String | 메시지가 확인되는 X12 규약의 이름 |
| direction | yes | 열거형 | 메시지 흐름의 방향(또는 `receive``send` |
| interchangeControlNumber | 예 | String | 교환 컨트롤 번호 |
| isaSegment | 예 | String | 메시지 ISA 세그먼트 |
| isTechnicalAcknowledgmentExpected | 부울 | 기술 승인이 X12 계약에 구성되었는지 여부  |
| isMessageFailed | yes | 부울 | X12 메시지가 실패했는지 여부 |
| isa09 | 예 | String | X12 문서 교환 날짜 |
| isa10 | 예 | String | X12 문서 교환 시간 |
| isa11 | 예 | String | X12 교환 제어 표준 식별자 |
| isa12 | 예 | String | X12 교환 제어 버전 번호 |
| isa14 | 예 | String | X12 승인이 요청됨 |
| isa15 | 예 | String | 테스트 또는 생산 지표 |
| isa16 | 예 | String | 요소 구분 기호 |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>X12 교환 승인 추적 스키마

```json
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
```

| 속성 | 필수 | Type | Description |
|----------|----------|------|-------------|
| senderPartnerName | 예 | String | X12 메시지 발신자의 파트너 이름 |
| receiverPartnerName | 예 | String | X12 메시지 수신자 파트너 이름 |
| senderQualifier | yes | String | 파트너 한정자 보내기 |
| senderIdentifier | yes | String | 파트너 식별자 보내기 |
| receiverQualifier | yes | String | 파트너 한정자 받기 |
| receiverQualifier | yes | String | 파트너 식별자 받기 |
| agreementName | 예 | String | 메시지가 확인되는 X12 규약의 이름 |
| direction | yes | 열거형 | 메시지 흐름의 방향(또는 `receive``send` |
| interchangeControlNumber | 예 | String | 파트너로부터 받은 기술 승인의 교환 제어 번호 |
| isaSegment | 예 | String | 파트너로부터 받은 기술 승인을 위한 ISA 세그먼트 |
| respondingInterchangeControlNumber | 예 | String | 파트너로부터 받은 기술 승인에 대한 교환 제어 번호 |
| isMessageFailed | yes | 부울 | X12 메시지가 실패했는지 여부 |
| statusCode | yes | 열거형 | 이러한 허용된 값으로 승인 상태 `Accepted` `Rejected`코드를 교환합니다.`AcceptedWithErrors` |
| processingStatus | yes | 열거형 | 이러한 허용된 값으로 승인 `Received` `Generated`상태: 및`Sent` |
| ta102 | 예 | String | 교환 날짜 |
| ta103 | 예 | String | 교환 시간 |
| ta105 | 예 | String | 교환 노트 코드 |
|||||

## <a name="x12-functional-group-tracking-schema"></a>X12 기능 그룹 추적 스키마

```json
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
```

| 속성 | 필수 | Type | Description |
|----------|----------|------|-------------|
| senderPartnerName | 예 | String | X12 메시지 발신자의 파트너 이름 |
| receiverPartnerName | 예 | String | X12 메시지 수신자 파트너 이름 |
| senderQualifier | yes | String | 파트너 한정자 보내기 |
| senderIdentifier | yes | String | 파트너 식별자 보내기 |
| receiverQualifier | yes | String | 파트너 한정자 받기 |
| receiverQualifier | yes | String | 파트너 식별자 받기 |
| agreementName | 예 | String | 메시지가 확인되는 X12 계약의 이름입니다. |
| direction | yes | 열거형 | 메시지 흐름의 방향(수신 또는 전송) |
| interchangeControlNumber | 예 | String | 교환 컨트롤 번호 |
| functionalGroupControlNumber | 예 | String | 기능 제어 번호 |
| gsSegment | 예 | String | 메시지 GS 세그먼트 |
| isTechnicalAcknowledgmentExpected | yes | 부울 | 기술 승인이 X12 계약에 구성되었는지 여부 |
| isFunctionalAcknowledgmentExpected | yes | 부울 | X12 규약에서 기능 승인이 구성되었는지 여부 |
| isMessageFailed | yes | 부울 | X12 메시지가 실패했는지 여부 |
| gs01 | 예 | String | 기능 식별자 코드 |
| gs02 | 예 | String | 응용 프로그램 발신자의 코드 |
| gs03 | 예 | String | 응용 프로그램 수신기의 코드 |
| gs04 | 예 | String | 기능 그룹 날짜 |
| gs05 | 예 | String | 기능 그룹 시간 |
| gs07 | 예 | String | 책임 있는 대행사 코드 |
| gs08 | 예 | String | 버전, 릴리스 또는 산업용 식별자 코드 |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>X12 기능 그룹 승인 추적 스키마

```json
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
```

| 속성 | 필수 | Type | Description |
|----------|----------|------|-------------|
| senderPartnerName | 예 | String | X12 메시지 발신자의 파트너 이름 |
| receiverPartnerName | 예 | String | X12 메시지 수신자 파트너 이름 |
| senderQualifier | yes | String | 파트너 한정자 보내기 |
| senderIdentifier | yes | String | 파트너 식별자 보내기 |
| receiverQualifier | yes | String | 파트너 한정자 받기 |
| receiverQualifier | yes | String | 파트너 식별자 받기 |
| agreementName | 예 | String | 메시지가 확인되는 X12 규약의 이름 |
| direction | yes | 열거형 | 메시지 흐름의 방향(또는 `receive``send` |
| interchangeControlNumber | 예 | String | 파트너로부터 기술 승인을 받을 때 송신 측에 채우는 교환 제어 번호 |
| functionalGroupControlNumber | 예 | String | 파트너로부터 기술 승인을 받을 때 송신 측에 채우는 기술 승인의 기능 그룹 제어 번호 |
| isaSegment | 예 | String | 교환 제어 번호와 동일하지만 특정 경우에만 채워집니다. |
| gsSegment | 예 | String | 기능 그룹 제어 번호와 동일하지만 특정 경우에만 채워집니다. |
| respondingfunctionalGroupControlNumber | 예 | String | 원래 기능 그룹의 제어 번호 |
| respondingFunctionalGroupId | 예 | String | 승인 기능 그룹 ID에서 AK101에 대한 지도 |
| isMessageFailed | 부울 | X12 메시지가 실패했는지 여부 |
| statusCode | yes | 열거형 | 이러한 허용된 값을 사용하여 상태 `Accepted` `Rejected`코드를 승인합니다.`AcceptedWithErrors` |
| processingStatus | yes | 열거형 | 이러한 허용된 값으로 승인 상태를 `Received`처리합니다. `Generated``Sent` |
| ak903 | 예 | String | 수신된 트랜잭션 집합 수 |
| ak904 | 예 | String | 식별된 기능 그룹에서 허용되는 트랜잭션 집합 수 |
| ak9Segment | 예 | String | AK1 세그먼트에서 식별된 기능 그룹이 수락 또는 거부되는지 여부 및 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 프로토콜 추적 스키마

스키마를 추적하는 B2B 프로토콜에 대한 정보는 다음을 참조하세요.

* [AS2 추적 스키마](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [B2B 사용자 지정 추적 스키마](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>다음 단계

* [Azure Monitor 로그를 사용하여 B2B 메시지 모니터링](../logic-apps/monitor-b2b-messages-log-analytics.md)