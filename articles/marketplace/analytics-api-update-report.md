---
title: 보고서 API 업데이트
description: 이 API를 사용 하 여 상업적 marketplace 분석 보고서에 대 한 매개 변수를 보고 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 38680eb291417ded4c2f93539e8d1ae091b1d560
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583925"
---
# <a name="update-report-api"></a>보고서 API 업데이트

이 API는 보고서 매개 변수를 수정 하는 데 도움이 됩니다.

**요청 구문**

| 방법 | 요청 URI |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
|||

**요청 헤더**

| 헤더 | Type | Description |
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

**요청 페이로드**

```json
{
  "ReportName": "string",
  "Description": "string",
  "StartTime": "string",
  "RecurrenceInterval": 0,
  "RecurrenceCount": 0,
  "Format": "string",
  "CallbackUrl": "string"
}
```

**용어 설명**

다음 표에서는 요청 페이로드에 있는 요소의 키 정의를 보여 줍니다.

| 매개 변수 | 필수 | Description | 허용되는 값 |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | 예 | 보고서에 할당할 이름입니다. | 문자열 |
| `Description` | No | 만든 보고서에 대 한 설명 | 문자열 |
| `StartTime` | 예 | 보고서 생성이 시작 되는 타임 스탬프 | 문자열 |
| `RecurrenceInterval` | No | 보고서를 몇 시간 내에 생성 해야 하는 빈도입니다. 최소값은 4입니다. | 정수 |
| `RecurrenceCount` | 아니요 | 생성할 보고서 수입니다. 기본값은 무한 | 정수 |
| `Format` | 예 | 내보낸 파일의 파일 형식입니다. 기본값은 CSV입니다. | CSV/TSV |
| `CallbackUrl` | 예 | 보고서 생성 시 호출할 https 콜백 URL입니다. | 문자열 |
|||||

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
| `ReportId` | 업데이트 되는 보고서의 UUID (범용 고유 식별자) |
| `ReportName` | 요청 페이로드의 보고서에 지정 된 이름입니다. |
| `Description` | 보고서를 만드는 동안 제공 된 설명입니다. |
| `QueryId` | 보고서를 만들 때 전달 된 쿼리 ID입니다. |
| `Query` | 이 보고서에 대해 실행 되는 쿼리 텍스트 |
| `User` | 보고서를 만드는 데 사용 되는 사용자 ID |
| `CreatedTime` | 보고서를 만든 시간입니다. 시간 형식은 yyyy-MM-Yyyy-mm-ddthh: MM: ssZ입니다. |
| `ModifiedTime` | 보고서를 마지막으로 수정한 시간입니다. 시간 형식은 yyyy-MM-Yyyy-mm-ddthh: MM: ssZ입니다. |
| `StartTime` | 보고서 실행이 시작 되는 시간입니다. 시간 형식은 yyyy-MM-Yyyy-mm-ddthh: MM: ssZ입니다. |
| `ReportStatus` | 보고서 실행의 상태입니다. 가능한 값은 일시 중지, 활성 및 비활성입니다. |
| `RecurrenceInterval` | 보고서를 만드는 동안 제공 된 되풀이 간격 |
| `RecurrenceCount` | 보고서를 만드는 동안 제공 된 되풀이 횟수 |
| `CallbackUrl` | 요청에 제공 된 콜백 URL |
| `Format` | 보고서 파일 형식 |
|||
