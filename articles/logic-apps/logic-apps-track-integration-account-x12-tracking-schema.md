---
title: B2B 메시지에 대 한 X12 추적 스키마
description: 추적 스키마를 만들어 Azure Logic Apps의 X12 메시지 모니터링
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905292"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Azure Logic Apps에서 X12 메시지 추적을 위한 스키마 만들기

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
| senderPartnerName | 아니요 | 문자열 | X12 메시지 보낸 사람의 파트너 이름 |
| receiverPartnerName | 아니요 | 문자열 | X12 메시지 받는 사람의 파트너 이름 |
| senderQualifier | 예 | String | 송신 파트너 한정자 |
| senderIdentifier | 예 | String | 송신 파트너 식별자 |
| receiverQualifier | 예 | String | 수신 파트너 한정자 |
| receiverQualifier | 예 | String | 수신 파트너 식별자 |
| agreementName | 아니요 | 문자열 | 메시지가 확인되는 X12 규약의 이름 |
| direction | 예 | 열거형 | 메시지 흐름의 방향으로, `receive` 또는입니다.`send` |
| interchangeControlNumber | 아니요 | 문자열 | 교환 컨트롤 번호 |
| functionalGroupControlNumber | 아니요 | 문자열 | 기능 제어 번호 |
| transactionSetControlNumber | 아니요 | 문자열 | 트랜잭션 집합 컨트롤 번호입니다. |
| CorrelationMessageId | 아니요 | 문자열 | 상관 관계 메시지 ID는 {AgreementName} {*Groupcontrolnumber*} {Transactionsetcontrolnumber}의 조합입니다. |
| messageType | 아니요 | 문자열 | 트랜잭션 집합 또는 문서 유형 |
| isMessageFailed | 예 | 부울 | X12 메시지의 실패 여부 |
| isTechnicalAcknowledgmentExpected | 예 | 부울 | X12 규약에서 기술 승인이 구성 되었는지 여부 |
| isFunctionalAcknowledgmentExpected | 예 | 부울 | X12 규약에서 기능 승인이 구성 되었는지 여부 |
| needAk2LoopForValidMessages | 예 | 부울 | 유효한 메시지에 AK2 루프가 필요한 지 여부 |
| segmentsCount | 아니요 | 정수 | X12 트랜잭션 집합의 세그먼트 수 |
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
| senderPartnerName | 아니요 | 문자열 | X12 메시지 보낸 사람의 파트너 이름 |
| receiverPartnerName | 아니요 | 문자열 | X12 메시지 받는 사람의 파트너 이름 |
| senderQualifier | 예 | String | 송신 파트너 한정자 |
| senderIdentifier | 예 | String | 송신 파트너 식별자 |
| receiverQualifier | 예 | String | 수신 파트너 한정자 |
| receiverQualifier | 예 | String | 수신 파트너 식별자 |
| agreementName | 아니요 | 문자열 | 메시지가 확인되는 X12 규약의 이름 |
| direction | 예 | 열거형 | 메시지 흐름의 방향으로, `receive` 또는입니다.`send` |
| interchangeControlNumber | 아니요 | 문자열 | 기능 승인의 교환 컨트롤 번호입니다. 이 값은 파트너로 전송 된 메시지에 대해 기능 승인이 수신 되는 송신 측에 대해서만 채워집니다. |
| functionalGroupControlNumber | 아니요 | 문자열 | 기능 승인의 기능 그룹 컨트롤 번호입니다. 이 값은 파트너로 전송 된 메시지에 대해 기능 승인이 수신 되는 송신 측에 대해서만 채워집니다. |
| isaSegment | 아니요 | 문자열 | 메시지의 ISA 세그먼트. 이 값은 파트너로 전송 된 메시지에 대해 기능 승인이 수신 되는 송신 측에 대해서만 채워집니다. |
| gsSegment | 아니요 | 문자열 | 메시지의 GS 세그먼트. 이 값은 파트너로 전송 된 메시지에 대해 기능 승인이 수신 되는 송신 측에 대해서만 채워집니다. |
| respondingfunctionalGroupControlNumber | 아니요 | 문자열 | 응답 교환 컨트롤 번호 |
| respondingFunctionalGroupId | 아니요 | 문자열 | 응답의 A K 101에 매핑되는 응답 기능 그룹 ID입니다. |
| respondingtransactionSetControlNumber | 아니요 | 문자열 | 응답 하는 트랜잭션 집합 컨트롤 번호 |
| respondingTransactionSetId | 아니요 | 문자열 | 응답의 A K 201에 매핑되는 응답 트랜잭션 집합 ID입니다. |
| statusCode | 예 | 부울 | 트랜잭션 집합 승인 상태 코드 |
| segmentsCount | 예 | 열거형 | 허용 되는 값을 가진 승인 상태 `Accepted`코드 `Rejected`:, 및`AcceptedWithErrors` |
| processingStatus | 예 | 열거형 | 허용 되는 값을 가진 승인의 처리 상태 `Received`: `Generated`, 및`Sent` |
| CorrelationMessageId | 아니요 | 문자열 | 상관 관계 메시지 ID는 {AgreementName} {*Groupcontrolnumber*} {Transactionsetcontrolnumber}의 조합입니다. |
| isMessageFailed | 예 | 부울 | X12 메시지의 실패 여부 |
| ak2Segment | 아니요 | 문자열 | 받은 기능 그룹 내의 트랜잭션 집합에 대 한 승인 |
| ak3Segment | 아니요 | 문자열 | 데이터 세그먼트의 오류를 보고 합니다. |
| ak5Segment | 아니요 | 문자열 | AK2 세그먼트에 식별 된 트랜잭션 집합이 허용 되는지 또는 거부 되는지와 그 이유를 보고 합니다. |
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
| senderPartnerName | 아니요 | 문자열 | X12 메시지 보낸 사람의 파트너 이름 |
| receiverPartnerName | 아니요 | 문자열 | X12 메시지 받는 사람의 파트너 이름 |
| senderQualifier | 예 | String | 송신 파트너 한정자 |
| senderIdentifier | 예 | String | 송신 파트너 식별자 |
| receiverQualifier | 예 | String | 수신 파트너 한정자 |
| receiverQualifier | 예 | String | 수신 파트너 식별자 |
| agreementName | 아니요 | 문자열 | 메시지가 확인되는 X12 규약의 이름 |
| direction | 예 | 열거형 | 메시지 흐름의 방향으로, `receive` 또는입니다.`send` |
| interchangeControlNumber | 아니요 | 문자열 | 교환 컨트롤 번호 |
| isaSegment | 아니요 | 문자열 | 메시지 ISA 세그먼트 |
| isTechnicalAcknowledgmentExpected | 부울 | X12 규약에서 기술 승인이 구성 되었는지 여부  |
| isMessageFailed | 예 | 부울 | X12 메시지의 실패 여부 |
| isa09 | 아니요 | 문자열 | X12 문서 교환 날짜 |
| isa10 | 아니요 | 문자열 | X12 문서 교환 시간 |
| isa11 | 아니요 | 문자열 | X12 교환 컨트롤 표준 식별자 |
| isa12 | 아니요 | 문자열 | X12 교환 컨트롤 버전 번호 |
| isa14 | 아니요 | 문자열 | X12 승인이 요청 되었습니다. |
| isa15 | 아니요 | 문자열 | 테스트 또는 프로덕션에 대 한 표시기 |
| isa16 | 아니요 | 문자열 | 요소 구분 기호 |
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
| senderPartnerName | 아니요 | 문자열 | X12 메시지 보낸 사람의 파트너 이름 |
| receiverPartnerName | 아니요 | 문자열 | X12 메시지 받는 사람의 파트너 이름 |
| senderQualifier | 예 | String | 송신 파트너 한정자 |
| senderIdentifier | 예 | String | 송신 파트너 식별자 |
| receiverQualifier | 예 | String | 수신 파트너 한정자 |
| receiverQualifier | 예 | String | 수신 파트너 식별자 |
| agreementName | 아니요 | 문자열 | 메시지가 확인되는 X12 규약의 이름 |
| direction | 예 | 열거형 | 메시지 흐름의 방향으로, `receive` 또는입니다.`send` |
| interchangeControlNumber | 아니요 | 문자열 | 파트너 로부터 받은 기술 승인의 교환 컨트롤 번호 |
| isaSegment | 아니요 | 문자열 | 파트너 로부터 받은 기술 승인에 대 한 ISA 세그먼트 |
| respondingInterchangeControlNumber | 아니요 | 문자열 | 파트너 로부터 받은 기술 승인에 대 한 교환 컨트롤 번호 |
| isMessageFailed | 예 | 부울 | X12 메시지의 실패 여부 |
| statusCode | 예 | 열거형 | 허용 되는 값을 가진 교환 승인 상태 `Accepted`코드 `Rejected`:, 및`AcceptedWithErrors` |
| processingStatus | 예 | 열거형 | 허용 되는 값을 가진 승인 `Received`상태 `Generated`:, 및`Sent` |
| ta102 | 아니요 | 문자열 | 교환 날짜 |
| ta103 | 아니요 | 문자열 | 교환 시간 |
| ta105 | 아니요 | 문자열 | 교환 노트 코드 |
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
| senderPartnerName | 아니요 | 문자열 | X12 메시지 보낸 사람의 파트너 이름 |
| receiverPartnerName | 아니요 | 문자열 | X12 메시지 받는 사람의 파트너 이름 |
| senderQualifier | 예 | String | 송신 파트너 한정자 |
| senderIdentifier | 예 | String | 송신 파트너 식별자 |
| receiverQualifier | 예 | String | 수신 파트너 한정자 |
| receiverQualifier | 예 | String | 수신 파트너 식별자 |
| agreementName | 아니요 | 문자열 | 메시지가 확인 되는 X12 규약의 이름 |
| direction | 예 | 열거형 | 메시지 흐름 방향 (수신 또는 송신) |
| interchangeControlNumber | 아니요 | 문자열 | 교환 컨트롤 번호 |
| functionalGroupControlNumber | 아니요 | 문자열 | 기능 제어 번호 |
| gsSegment | 아니요 | 문자열 | 메시지 GS 세그먼트 |
| isTechnicalAcknowledgmentExpected | 예 | 부울 | X12 규약에서 기술 승인이 구성 되었는지 여부 |
| isFunctionalAcknowledgmentExpected | 예 | 부울 | X12 규약에서 기능 승인이 구성 되었는지 여부 |
| isMessageFailed | 예 | 부울 | X12 메시지의 실패 여부 |
| gs01 | 아니요 | 문자열 | 함수 식별자 코드 |
| gs02 | 아니요 | 문자열 | 응용 프로그램 보낸 사람 코드 |
| gs03 | 아니요 | 문자열 | 응용 프로그램 받는 사람 코드 |
| gs04 | 아니요 | 문자열 | 기능 그룹 날짜 |
| gs05 | 아니요 | 문자열 | 기능 그룹 시간 |
| gs07 | 아니요 | 문자열 | 담당 에이전시 코드 |
| gs08 | 아니요 | 문자열 | 버전, 릴리스 또는 업계에 대 한 식별자 코드 |
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
| senderPartnerName | 아니요 | 문자열 | X12 메시지 보낸 사람의 파트너 이름 |
| receiverPartnerName | 아니요 | 문자열 | X12 메시지 받는 사람의 파트너 이름 |
| senderQualifier | 예 | String | 송신 파트너 한정자 |
| senderIdentifier | 예 | String | 송신 파트너 식별자 |
| receiverQualifier | 예 | String | 수신 파트너 한정자 |
| receiverQualifier | 예 | String | 수신 파트너 식별자 |
| agreementName | 아니요 | 문자열 | 메시지가 확인되는 X12 규약의 이름 |
| direction | 예 | 열거형 | 메시지 흐름의 방향으로, `receive` 또는입니다.`send` |
| interchangeControlNumber | 아니요 | 문자열 | 기술 승인이 파트너 로부터 수신 될 때 송신 측에 대해 채워지는 교환 컨트롤 번호 |
| functionalGroupControlNumber | 아니요 | 문자열 | 기술 승인이 파트너 로부터 수신 될 때 송신 측에 대해 채워지는 기능 그룹 컨트롤 번호입니다. |
| isaSegment | 아니요 | 문자열 | 교환 컨트롤 번호와 동일 하지만 특정 경우에만 채워집니다. |
| gsSegment | 아니요 | 문자열 | 기능 그룹 컨트롤 번호와 동일 하지만 특정 경우에만 채워집니다. |
| respondingfunctionalGroupControlNumber | 아니요 | 문자열 | 원래 기능 그룹의 컨트롤 번호 |
| respondingFunctionalGroupId | 아니요 | 문자열 | 승인 기능 그룹 ID의 A K 101에 매핑됩니다. |
| isMessageFailed | 부울 | X12 메시지의 실패 여부 |
| statusCode | 예 | 열거형 | 허용 되는 값을 가진 승인 상태 `Accepted`코드 `Rejected`:, 및`AcceptedWithErrors` |
| processingStatus | 예 | 열거형 | 허용 되는 값을 가진 승인의 처리 상태 `Received`: `Generated`, 및`Sent` |
| ak903 | 아니요 | 문자열 | 받은 트랜잭션 집합 수 |
| ak904 | 아니요 | 문자열 | 식별 된 기능 그룹에서 수락 된 트랜잭션 집합 수 |
| ak9Segment | 아니요 | 문자열 | AK1 세그먼트에서 식별 된 기능 그룹이 허용 되는지 아니면 거부 되는지와 그 이유 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 프로토콜 추적 스키마

스키마를 추적하는 B2B 프로토콜에 대한 정보는 다음을 참조하세요.

* [AS2 추적 스키마](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [B2B 사용자 지정 추적 스키마](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>다음 단계

* [Azure Monitor 로그를 사용하여 B2B 메시지 모니터링](../logic-apps/monitor-b2b-messages-log-analytics.md)