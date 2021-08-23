---
title: 보고서 실행 API 일시 중지
description: 이 API를 사용하여 상업용 마켓플레이스 분석 보고서의 예약된 실행을 일시 중지합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 80a7238e76b7152f13dd157aa0a1b7fc9937867a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567109"
---
# <a name="pause-report-executions-api"></a>보고서 실행 API 일시 중지

실행 시 이 API는 보고서의 예약된 실행을 일시 중지합니다.

**요청 구문**

| 방법 | 요청 URI |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/pause/{Report ID}` |
|||

**요청 헤더**

| 헤더 | 형식 | Description |
| ------------ | ------------- | ------------- |
| 권한 부여 | 문자열 | 필수 요소. `Bearer <token>` 양식의 Azure AD(Azure Active Directory) 액세스 토큰 |
| 콘텐츠 형식 | 문자열 | `Application/JSON` |
||||

**경로 매개 변수**

None

**쿼리 매개 변수**

| 매개 변수 이름 | 필수 | Type | 설명 |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | 예 | 문자열 | 수정하는 보고서의 ID |
|||||

**용어 설명**

없음

**응답**

응답 페이로드는 다음과 같이 JSON 형식으로 구성됩니다.

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

| 매개 변수 | Description |
| ------------ | ------------- |
| `ReportId` | 삭제 보고서의 UUID(범용 고유 식별자) |
| `ReportName` | 만드는 동안 보고서에 지정된 이름 |
| `Description` | 보고서를 만드는 동안 제공된 설명 |
| `QueryId` | 보고서를 만들 때 전달된 쿼리 ID |
| `Query` | 이 보고서에 대해 실행되는 쿼리 텍스트 |
| `User` | 보고서 생성에 사용되는 사용자 ID |
| `CreatedTime` | 보고서를 만든 시간입니다. 시간 형식은 yyyy-MM-ddTHH:mm:ssZ입니다 |
| `ModifiedTime` | 보고서를 마지막으로 수정한 시간입니다. 시간 형식은 yyyy-MM-ddTHH:mm:ssZ입니다. |
| `StartTime` | 보고서 실행이 시작되는 시간입니다. 시간 형식은 yyyy-MM-ddTHH:mm:ssZ입니다. |
| `ReportStatus` | 보고서 실행 상태입니다. 가능한 값은 일시 중지, 활성, 비활성입니다. |
| `RecurrenceInterval` | 보고서를 만드는 동안 제공된 되풀이 간격 |
| `RecurrenceCount` | 보고서를 만드는 동안 제공된 되풀이 횟수 |
| `CallbackUrl` | 요청에 제공된 콜백 URL |
| `Format` | 보고서 파일 형식 |
|||
