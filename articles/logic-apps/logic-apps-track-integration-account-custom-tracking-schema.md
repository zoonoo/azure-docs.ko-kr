---
title: B2B 메시지에 대한 사용자 지정 추적 스키마
description: Azure 논리 앱에서 B2B 메시지를 모니터링하는 사용자 지정 추적 스키마 만들기
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76903055"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Azure Logic A에서 종단 간 워크플로를 모니터링하는 사용자 지정 추적 스키마 만들기

Azure Logic Apps에는 워크플로의 일부에 대해 활성화할 수 있는 기본 제공 추적기능이 있습니다. 그러나 로직 앱, BizTalk Server, SQL Server 또는 기타 계층을 포함하는 워크플로와 같은 워크플로의 처음부터 끝까지 이벤트를 기록하는 사용자 지정 추적을 설정할 수 있습니다. 이 문서에서는 논리 앱 외부의 계층에서 사용할 수 있는 사용자 지정 코드를 제공합니다.

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

| 속성 | 필수 | Type | Description |
|----------|----------|------|-------------|
| sourceType | yes | String | 이러한 허용된 값을 가진 실행 `Microsoft.Logic/workflows`소스의 유형:`custom` |
| source | yes | 문자열 또는 J토큰 | 원본 유형이 `Microsoft.Logic/workflows`있는 경우 원본 정보는 이 스키마를 따라야 합니다. 원본 형식이 `custom`있는 경우 스키마는 JToken입니다. |
| systemId | yes | String | 로직 앱 시스템 ID |
| runId | yes | String | 논리 앱 실행 ID |
| operationName | yes | String | 작업 이름(예: 작업 또는 트리거) |
| repeatItemScopeName | yes | String | 작업이 `foreach`또는 `until` 루프 내에 있는 경우 항목 이름 반복 |
| repeatItemIndex | yes | 정수 | 작업이 `foreach` 또는 `until` 루프 내에 있고 반복되는 항목 인덱스 번호임을 나타냅니다. |
| trackingId | 예 | String | 메시지의 상관 관계를 지정하는 추적 ID |
| correlationId | 예 | String | 메시지의 상관 관계를 상관관계 ID로 지정 |
| clientRequestId | 예 | String | 클라이언트는 메시지를 상호 연관시키기 위해 이 속성을 채울 수 있습니다. |
| eventLevel | yes | String | 이벤트 수준 |
| eventTime | yes | DateTime | UTC 형식의 이벤트 시간: *YYYY-MM-DDTHH:MM:SS.00000Z* |
| recordType | yes | String | 이 허용된 값만 있는 트랙 레코드의 유형:`custom` |
| 레코드(record) | yes | J토큰 | JToken 형식만 있는 사용자 지정 레코드 형식 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 프로토콜 추적 스키마

스키마를 추적하는 B2B 프로토콜에 대한 정보는 다음을 참조하세요.

* [AS2 추적 스키마](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 추적 스키마](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>다음 단계

* [Azure 모니터 로그를 통해 B2B 메시지 모니터링에](../logic-apps/monitor-b2b-messages-log-analytics.md) 대해 자세히 알아보기