---
title: 보고서 실행 일시 중지 API
description: 이 API를 사용 하 여 상업적 marketplace 분석 보고서의 예약 된 실행을 일시 중지 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 39b535278fef42818f572631cfa1cb1f923930a6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584031"
---
# <a name="pause-report-executions-api"></a>보고서 실행 일시 중지 API

실행 시이 API는 보고서의 예약 된 실행을 일시 중지 합니다.

**요청 구문**

| 방법 | 요청 URI |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/pause/{Report ID}` |
|||

**요청 헤더**

| header | 유형 | Description |
| ------------ | ------------- | ------------- |
| 권한 부여 | 문자열 | 필수 요소. 양식의 Azure AD (Azure Active Directory) 액세스 토큰 `Bearer <token>` |
| 콘텐츠 형식 | 문자열 | `Application/JSON` |
||||

**Path 매개 변수**

없음

**쿼리 매개 변수**

| 매개 변수 이름 | 필수 | Type | 설명 |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | 예 | 문자열 | 수정할 보고서의 ID입니다. |
|||||

**용어 설명**

없음

**응답**

응답 페이로드는 다음과 같이 JSON 형식으로 구성 됩니다.

응답 코드: 200, 400, 401, 403, 404, 500 응답 페이로드:

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
      "RecurrenceCount": 0,
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

| 매개 변수 | 설명 |
| ------------ | ------------- |
| `ReportId` | 삭제 된 보고서의 UUID (범용 고유 식별자) |
| `ReportName` | 만드는 동안 보고서에 지정 된 이름입니다. |
| `Description` | 보고서를 만드는 동안 제공 된 설명입니다. |
| `QueryId` | 보고서를 만들 때 전달 된 쿼리 ID입니다. |
| `Query` | 이 보고서에 대해 실행 되는 쿼리 텍스트 |
| `User` | 보고서 생성에 사용 되는 사용자 ID |
| `CreatedTime` | 보고서를 만든 시간입니다. 시간 형식은 yyyy-MM-Yyyy-mm-ddthh: MM: ssZ입니다. |
| `ModifiedTime` | 보고서를 마지막으로 수정한 시간입니다. 시간 형식은 yyyy-MM-Yyyy-mm-ddthh: MM: ssZ입니다. |
| `StartTime` | 보고서 실행이 시작 되는 시간입니다. 시간 형식은 yyyy-MM-Yyyy-mm-ddthh: MM: ssZ입니다. |
| `ReportStatus` | 보고서 실행의 상태입니다. 가능한 값은 일시 중지, 활성 및 비활성입니다. |
| `RecurrenceInterval` | 보고서를 만드는 동안 제공 된 되풀이 간격 |
| `RecurrenceCount` | 보고서를 만드는 동안 제공 된 되풀이 횟수 |
| `CallbackUrl` | 요청에 제공 된 콜백 URL |
| `Format` | 보고서 파일 형식 |
|||
