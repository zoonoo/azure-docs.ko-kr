---
title: 보고서 API 삭제
description: 이 API를 사용하여 상업용 Marketplace 분석 보고서에 대한 모든 보고서 및 보고서 실행 레코드를 삭제할 수 있습니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 9cb8f12061e53a89fa0380d717c0ad947515ab5f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536715"
---
# <a name="delete-report-api"></a>보고서 API 삭제

실행 시 이 API는 모든 보고서 및 보고서 실행 레코드를 삭제합니다.

**요청 구문**

| 방법 | 요청 URI |
| ------------ | ------------- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
|||

**요청 헤더**

| 헤더 | 형식 | Description |
| ------------ | ------------- | ------------- |
| 권한 부여 | 문자열 | 필수 요소. `Bearer <token>` 형식의 Azure AD 액세스 토큰 |
| 콘텐츠 유형 | 문자열 | `Application/JSON` |
||||

**경로 매개 변수**

없음

**쿼리 매개 변수**

| 매개 변수 이름 | 필수 | 문자열 | 설명 |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | 예 | 문자열 | 수정 중인 보고서의 ID |
|||||

**용어 설명**

없음

**응답**

응답 페이로드는 다음과 같이 구성됩니다.

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
| `ReportId` | 삭제된 보고서의 고유 UUID |
| `ReportName` | 만드는 동안 보고서에 지정된 이름 |
| `Description` | 보고서를 만드는 동안 제공된 설명 |
| `QueryId` | 보고서를 만들 때 전달된 쿼리 ID |
| `Query` | 이 보고서에 대해 실행되는 쿼리 텍스트 |
| `User` | 보고서를 만드는 데 사용되는 사용자 ID |
| `CreatedTime` | 보고서가 생성된 시간입니다. 시간 형식은 yyyy-MM-ddTHH:mm:ssZ입니다 |
| `ModifiedTime` | 보고서를 마지막으로 수정한 시간입니다. 시간 형식은 yyyy-MM-ddTHH:mm:ssZ입니다. |
| `StartTime` | 보고서 실행이 시작되는 시간입니다. 시간 형식은 yyyy-MM-ddTHH:mm:ssZ입니다. |
| `ReportStatus` | 보고서 실행 상태입니다. 가능한 값은 일시 중지, 활성 및 비활성입니다. |
| `RecurrenceInterval` | 보고서를 만드는 동안 제공된 되풀이 간격 |
| `RecurrenceCount` | 보고서를 만드는 동안 제공된 되풀이 횟수 |
| `CallbackUrl` | 요청에 제공된 콜백 URL |
| `Format` | 보고서 파일 형식 |
|||
