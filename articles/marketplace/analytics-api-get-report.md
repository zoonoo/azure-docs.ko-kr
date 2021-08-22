---
title: 보고서 API 가져오기
description: 이 API를 사용하여 파트너 센터에서 예약된 분석 보고서를 가져옵니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: d9248559f785ffb3b63e1f3d275e33a205cb682f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529282"
---
# <a name="get-report-api"></a>보고서 API 가져오기

이 API는 예약된 모든 보고서를 가져옵니다.

**요청 구문**

| **방법** | **요청 URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport?reportId={Report ID}&reportName={Report Name}&queryId={Query ID} ` |

**요청 헤더**

| **Header** | **형식** | **설명** |
| --- | --- | --- |
| 권한 부여 | 문자열 | 필수 요소. `Bearer <token>` 양식의 Azure AD(Azure Active Directory) 액세스 토큰 |
| 콘텐츠 형식 | 문자열 | `Application/JSON` |

**경로 매개 변수**

없음

**쿼리 매개 변수**

| **매개 변수 이름** | **필수** | **형식** | **설명** |
| --- | --- | --- | --- |
| `reportId` | 예 | 문자열 | 이 인수에 지정된 `reportId`를 사용하여 보고서의 세부 정보만 가져오도록 필터링합니다 |
| `reportName` | 예 | 문자열 | 이 인수에 지정된 `reportName`를 사용하여 보고서의 세부 정보만 가져오도록 필터링합니다 |
| `queryId` | 예 | boolean | 응답에 미리 정의된 시스템 쿼리 포함 |

**용어 설명**

없음

**응답**

응답 페이로드는 다음과 같이 JSON 형식으로 구조화됩니다.

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

이 표에는 응답에 있는 요소의 주요 정의가 나열되어 있습니다.

| **매개 변수** | **설명** |
| --- | --- |
| `ReportId` | 생성된 보고서의 고유 UUID |
| `ReportName` | 요청 페이로드의 보고서에 지정된 이름 |
| `Description` | 보고서를 만들 때 지정된 설명 |
| `QueryId` | 보고서를 만들 때 전달된 쿼리 ID |
| `Query` | 이 보고서에 대해 실행되는 쿼리 텍스트 |
| `User` | 보고서를 만드는 데 사용되는 사용자 ID |
| `CreatedTime` | 보고서가 생성된 시간입니다. 시간 형식은 yyyy-MM-ddTHH:mm:ssZ입니다 |
| `ModifiedTime` | 보고서를 마지막으로 수정한 시간입니다. 시간 형식은 yyyy-MM-ddTHH:mm:ssZ입니다 |
| `StartTime` | 시간 실행이 시작됩니다. 시간 형식은 yyyy-MM-ddTHH:mm:ssZ입니다 |
| `ReportStatus` | 보고서 실행 상태입니다. 가능한 값은 일시 중지, 활성, 비활성입니다. |
| `RecurrenceInterval` | 보고서를 만드는 동안 제공된 되풀이 간격 |
| `RecurrenceCount` | 보고서를 만드는 동안 제공된 되풀이 횟수 |
| `CallbackUrl` | 요청에 제공된 콜백 URL |
| `Format` | 보고서 파일 형식 |
