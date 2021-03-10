---
title: 보고서 쿼리 API 삭제
description: 이 API를 사용 하 여 상업적 marketplace 분석에 대 한 사용자 정의 쿼리를 삭제 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4fc3479f1e35970a97684396a7a2e0c0c2582128
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584161"
---
# <a name="delete-report-queries-api"></a>보고서 쿼리 API 삭제

이 API는 사용자 정의 쿼리를 삭제 합니다.

**요청 구문**

| **방법** | **요청 URI** |
| --- | --- |
| Delete | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/{queryId}` |

**요청 헤더**

| **Header** | **Type** | **설명** |
| --- | --- | --- |
| 권한 부여 | 문자열 | 필수 요소. 양식의 Azure AD (Azure Active Directory) 액세스 토큰 `Bearer <token>` |
| 콘텐츠 형식 | 문자열 | `Application/JSON` |

**Path 매개 변수**

| **매개 변수 이름** | **Type** | **설명** |
| --- | --- | --- |
| `queryId` | string | 이 인수에 지정 된 ID를 가진 쿼리만 자세히 가져오도록 필터링 합니다. |

**쿼리 매개 변수**

없음

**요청 페이로드**

없음

**용어 설명**

없음

**응답**

응답 페이로드는 JSON 형식에서 다음과 같이 구성 됩니다.

응답 코드: 200, 400, 401, 403, 404, 500

응답 페이로드:

```json
{
  "Value": [
    {
      "QueryId": "string",
      "Name": "string",
      "Description": "string",
      "Query": "string",
      "Type": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string"
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
| `QueryId` | 삭제 된 쿼리의 고유 UUID입니다. |
| `Name` | 삭제 된 쿼리의 이름 |
| `Description` | 삭제 된 쿼리에 대 한 설명 |
| `Query` | 삭제 된 쿼리의 보고서 쿼리 문자열 |
| `Type` | 정의 |
| `User` | 쿼리를 만든 사용자 ID |
| `CreatedTime` | 쿼리를 만든 시간 |
| `ModifiedTime` | Null |
| `TotalCount` | 값 배열의 데이터 집합 수 |
| `StatusCode` | 결과 코드입니다. 가능한 값은 200, 400, 401, 403, 500입니다. |
