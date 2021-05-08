---
title: 보고서 실행 API 다시 시작
description: 이 API를 사용하여 일시 중지된 상업용 Marketplace 분석 보고서의 예약된 실행을 계속합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4a11783b28352cb62c5a3c0d38e45dcdc47a8d86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583935"
---
# <a name="resume-report-executions-api"></a>보고서 실행 API 다시 시작

실행 시 이 API는 일시 중지된 상업용 Marketplace 분석 보고서의 예약된 실행을 계속합니다.

**요청 구문**

| 방법 | 요청 URI |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/resume/{reportId}` |
|||

**요청 헤더**

| 헤더 | 유형 | Description |
| ------------ | ------------- | ------------- |
| 권한 부여 | 문자열 | 필수 요소. `Bearer <token>` 형식의 Azure AD(Azure Active Directory) 액세스 토큰 |
| 콘텐츠 형식 | 문자열 | `Application/JSON` |
||||

**경로 매개 변수**

없음

**쿼리 매개 변수**

| 매개 변수 이름 | 필수 | Type | 설명 |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | 예 | 문자열 | 수정하는 보고서의 ID |
|||||

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
| `ReportId` | 계속되는 보고서의 UUID(범용 고유 식별자) |
| `ReportName` | 만드는 동안 보고서에 지정된 이름 |
| `Description` | 보고서를 만드는 동안 제공된 설명 |
| `QueryId` | 보고서를 만들 때 전달된 쿼리 ID |
| `Query` | 이 보고서에 대해 실행되는 쿼리 텍스트 |
| `User` | 보고서 생성에 사용되는 사용자 ID |
| `CreatedTime` | 보고서를 만든 시간 시간 형식은 yyyy-MM-ddTHH:mm:ssZ입니다. |
| `ModifiedTime` | 보고서를 마지막으로 수정한 시간입니다. 시간 형식은 yyyy-MM-ddTHH:mm:ssZ입니다. |
| `StartTime` | 보고서 실행이 시작되는 시간입니다. 시간 형식은 yyyy-MM-ddTHH:mm:ssZ입니다. |
| `ReportStatus` | 보고서 실행 상태입니다. 가능한 값은 일시 중지, 활성, 비활성입니다. |
| `RecurrenceInterval` | 보고서를 만드는 동안 제공된 되풀이 간격입니다. |
| `RecurrenceCount` | 보고서를 만드는 동안 제공된 되풀이 수입니다. |
| `CallbackUrl` | 요청에 제공된 콜백 URL |
| `Format` | 보고서 파일 형식 |
|||
