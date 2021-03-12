---
title: 보고서 쿼리 API 가져오기
description: 이 API를 사용 하 여 상업적 marketplace 분석 보고서에서 사용할 수 있는 모든 쿼리를 가져올 수 있습니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: e2be43e8402e5179fb62d810fe7b9f41e704c49d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584091"
---
# <a name="get-report-queries-api"></a>보고서 쿼리 API 가져오기

보고서 쿼리 가져오기 API는 보고서에서 사용할 수 있는 모든 쿼리를 가져옵니다. 기본적으로 모든 시스템 및 사용자 정의 쿼리를 가져옵니다.

**요청 구문**

| **방법** | **요청 URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries?queryId={QueryID}&queryName={QueryName}&includeSystemQueries={include_system_queries}&includeOnlySystemQueries={include_only_system_queries}` |
|||

**요청 헤더**

| **Header** | **Type** | **설명** |
| --- | --- | --- |
| 권한 부여 | 문자열 | 필수 요소. 양식의 Azure AD (Azure Active Directory) 액세스 토큰 `Bearer <token>` |
| 콘텐츠 형식 | 문자열 | `Application/JSON` |
||||

**Path 매개 변수**

없음

**쿼리 매개 변수**

| **매개 변수 이름** | **유형** | **필수** | **설명** |
| --- | --- | --- | --- |
| `queryId` | 문자열 | No | 필터를 사용 하 여 인수에 지정 된 ID를 가진 쿼리만 세부 정보를 가져옵니다. |
| `queryName` | 문자열 | No | 인수에 지정 된 이름의 쿼리만 자세히 가져오도록 필터링 합니다. |
| `IncludeSystemQueries` | boolean | No | 응답에 미리 정의 된 시스템 쿼리 포함 |
| `IncludeOnlySystemQueries` | boolean | No | 응답에 시스템 쿼리만 포함 |
|||||

**요청 페이로드**

없음

**용어 설명**

없음

**응답**

응답 페이로드는 다음과 같이 구성 됩니다.

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

다음 표에서는 응답의 요소에 대 한 키 정의에 대해 설명 합니다.

| **매개 변수** | **설명** |
| --- | --- |
| `QueryId` | 쿼리의 고유 UUID |
| `Name` | 쿼리를 만들 때 쿼리에 지정 된 이름입니다. |
| `Description` | 쿼리를 만드는 동안 제공 된 설명입니다. |
| `Query` | 보고서 쿼리 문자열 |
| `Type` | `userDefined`사용자가 만든 쿼리 및 `system` 미리 정의 된 시스템 쿼리에 대해로 설정 합니다. |
| `User` | 쿼리를 만든 사용자 ID |
| `CreatedTime` | 쿼리를 만든 시간 |
| `TotalCount` | 값 배열의 데이터 집합 수 |
| `StatusCode` | 결과 코드입니다. 가능한 값은 200, 400, 401, 403, 500입니다. |
|||
