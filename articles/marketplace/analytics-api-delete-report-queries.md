---
title: 보고서 쿼리 API 삭제
description: 이 API를 사용하여 상업용 마켓플레이스 분석에 대한 사용자 정의 쿼리를 삭제합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4fc3479f1e35970a97684396a7a2e0c0c2582128
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584161"
---
# <a name="delete-report-queries-api"></a>보고서 쿼리 API 삭제

이 API는 사용자 정의 쿼리를 삭제합니다.

**요청 구문**

| **방법** | **요청 URI** |
| --- | --- |
| Delete | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/{queryId}` |

**요청 헤더**

| **Header** | **형식** | **설명** |
| --- | --- | --- |
| 권한 부여 | 문자열 | 필수 요소. `Bearer <token>` 양식의 Azure AD(Azure Active Directory) 액세스 토큰 |
| 콘텐츠 형식 | 문자열 | `Application/JSON` |

**경로 매개 변수**

| **매개 변수 이름** | **형식** | **설명** |
| --- | --- | --- |
| `queryId` | string | 이 인수에 지정된 ID가 있는 쿼리의 세부 정보만 가져오는 필터 |

**쿼리 매개 변수**

없음

**요청 페이로드**

없음

**용어 설명**

없음

**응답**

응답 페이로드는 다음과 같이 JSON 형식으로 구성됩니다.

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

이 표에는 응답에 있는 요소의 주요 정의가 나열되어 있습니다.

| **매개 변수** | **설명** |
| --- | --- |
| `QueryId` | 삭제된 쿼리의 고유 UUID입니다. |
| `Name` | 삭제된 쿼리의 이름입니다 |
| `Description` | 삭제 된 쿼리에 대한 설명입니다 |
| `Query` | 삭제 된 쿼리의 보고서 쿼리 문자열입니다 |
| `Type` | userDefined |
| `User` | 쿼리를 만든 사용자 ID |
| `CreatedTime` | 쿼리를 만든 시간입니다 |
| `ModifiedTime` | Null |
| `TotalCount` | 값 배열의 데이터 집합 수 |
| `StatusCode` | 결과 코드입니다. 가능한 값은 200, 400, 401, 403, 500입니다 |
