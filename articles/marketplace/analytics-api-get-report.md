---
title: 보고서 API 가져오기
description: 이 API를 사용 하 여 파트너 센터에서 예약 된 분석 보고서를 가져옵니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 3383af447f40ea984bce9cbc956f22ee6c5af200
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584050"
---
# <a name="get-report-api"></a>보고서 API 가져오기

이 API는 예약 된 모든 보고서를 가져옵니다.

**요청 구문**

| **방법** | **요청 URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport?reportId={Report ID}&reportName={Report Name}&queryId={Query ID} ` |

**요청 헤더**

| **Header** | **Type** | **설명** |
| --- | --- | --- |
| 권한 부여 | 문자열 | 필수 요소. 양식의 Azure AD (Azure Active Directory) 액세스 토큰 `Bearer <token>` |
| 콘텐츠 형식 | 문자열 | `Application/JSON` |

**Path 매개 변수**

없음

**쿼리 매개 변수**

| **매개 변수 이름** | **필수** | **Type** | **설명** |
| --- | --- | --- | --- |
| `reportId` | 예 | 문자열 | 이 인수에 지정 된를 사용 하 여 보고서의 세부 정보만 가져오도록 필터링 합니다. `reportId` |
| `reportName` | 예 | 문자열 | 이 인수에 지정 된를 사용 하 여 보고서의 세부 정보만 가져오도록 필터링 합니다. `reportName` |
| `queryId` | No | boolean | 응답에 미리 정의 된 시스템 쿼리 포함 |

**용어 설명**

없음

**응답**

응답 페이로드는 다음과 같이 JSON 형식으로 구조화 됩니다.

응답 코드: 200, 400, 401, 403, 404, 500

응답 페이로드:

```json
{
  "Value": [
    {
      "ReportId": "string",
      "ReportName": "string",
      "Description": "string",
      "QueryId": "string",
      "Query": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string",
      "StartTime": "string",
      "ReportStatus": "string",
      "RecurrenceInterval": 0,
      " RecurrenceCount": 0,
      "CallbackUrl": "string",
      "Format": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**용어 설명**

다음 표에서는 응답의 요소에 대 한 주요 정의를 보여 줍니다.

| **매개 변수** | **설명** |
| --- | --- |
| `ReportId` | 만든 보고서의 고유 UUID |
| `ReportName` | 요청 페이로드의 보고서에 지정 된 이름입니다. |
| `Description` | 보고서를 만들 때 지정 된 설명입니다. |
| `QueryId` | 보고서를 만들 때 전달 된 쿼리 ID입니다. |
| `Query` | 이 보고서에 대해 실행 되는 쿼리 텍스트 |
| `User` | 보고서를 만드는 데 사용 되는 사용자 ID |
| `CreatedTime` | 보고서를 만든 시간입니다. 시간 형식은 yyyy-MM-Yyyy-mm-ddthh: MM: ssZ입니다. |
| `ModifiedTime` | 보고서를 마지막으로 수정한 시간입니다. 시간 형식은 yyyy-MM-Yyyy-mm-ddthh: MM: ssZ입니다. |
| `StartTime` | 시간 실행이 시작 됩니다. 시간 형식은 yyyy-MM-Yyyy-mm-ddthh: MM: ssZ입니다. |
| `ReportStatus` | 보고서 실행의 상태입니다. 가능한 값은 일시 중지, 활성 및 비활성입니다. |
| `RecurrenceInterval` | 보고서를 만드는 동안 제공 된 되풀이 간격 |
| `RecurrenceCount` | 보고서를 만드는 동안 제공 된 되풀이 횟수 |
| `CallbackUrl` | 요청에 제공 된 콜백 URL |
| `Format` | 보고서 파일 형식 |
