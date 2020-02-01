---
title: B2B 메시지에 대 한 사용자 지정 추적 스키마
description: Azure Logic Apps에서 B2B 메시지를 모니터링 하는 사용자 지정 추적 스키마 만들기
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76903055"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Azure 논리 A에서 종단 간 워크플로를 모니터링 하는 사용자 지정 추적 스키마 만들기

Azure Logic Apps에는 워크플로의 일부에 대해 사용 하도록 설정할 수 있는 기본 제공 추적이 있습니다. 그러나 논리 앱, BizTalk Server, SQL Server 또는 다른 계층을 포함 하는 워크플로와 같이 워크플로 시작부터 끝까지 이벤트를 기록 하는 사용자 지정 추적을 설정할 수 있습니다. 이 문서에서는 논리 앱 외부의 계층에서 사용할 수 있는 사용자 지정 코드를 제공합니다.

## <a name="custom-tracking-schema"></a>사용자 지정 추적 스키마

```json
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
         "repeatItemIndex": ,
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
         "record": {}
      }
   ]
}
```

| 속성 | 필수 | 유형 | Description |
|----------|----------|------|-------------|
| sourceType | 예 | String | 허용 되는 값을 포함 하는 실행 원본의 형식: `Microsoft.Logic/workflows`, `custom` |
| source | 예 | 문자열 또는 JToken | 원본 형식이 `Microsoft.Logic/workflows`경우 원본 정보는이 스키마를 따라야 합니다. 원본 유형이 `custom`이면 스키마는 JToken입니다. |
| systemId | 예 | String | 논리 앱 시스템 ID |
| runId | 예 | String | 논리 앱 실행 ID |
| operationName | 예 | String | 작업 이름 (예: action 또는 trigger)입니다. |
| repeatItemScopeName | 예 | String | 작업이 `foreach`또는 `until` 루프 내에 있으면 항목 이름을 반복 합니다. |
| repeatItemIndex | 예 | 정수 | 동작이 `foreach` 또는 `until` 루프 내에 있으며 반복 된 항목 인덱스 번호 임을 나타냅니다. |
| trackingId | 아닙니다. | String | 메시지의 상관 관계를 위한 추적 ID |
| correlationId | 아닙니다. | String | 메시지와 상관 관계를 연결 하는 상관 관계 ID |
| clientRequestId | 아닙니다. | String | 클라이언트는이 속성을 채워서 메시지의 상관 관계를 지정할 수 있습니다. |
| eventLevel | 예 | String | 이벤트 수준 |
| eventTime | 예 | DateTime | UTC 형식의 이벤트 시간: *yyyy-mm-dd: mm: yyyy-mm-ddthh: MM: SS. 00000Z* |
| recordType | 예 | String | 허용 되는 값만 포함 된 트랙 레코드의 유형: `custom` |
| 레코드(record) | 예 | JToken | JToken 형식만 포함 된 사용자 지정 레코드 형식 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 프로토콜 추적 스키마

스키마를 추적하는 B2B 프로토콜에 대한 정보는 다음을 참조하세요.

* [AS2 추적 스키마](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 추적 스키마](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>다음 단계

* [Azure Monitor 로그를 사용 하 여 B2B 메시지 모니터링](../logic-apps/monitor-b2b-messages-log-analytics.md) 에 대해 자세히 알아보기