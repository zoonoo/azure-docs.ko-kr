---
title: 프로그래밍 방식 액세스 페러다임
description: 프로그래밍 분석에 대 한 API 호출 패턴의 상위 수준 흐름을 이해 합니다. Microsoft 상업적 marketplace의 분석 보고서에 액세스 하는 Api에 대해서도 설명 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8e0b94a46e96dd8ba16040e16b421520eb67de19
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584090"
---
# <a name="programmatic-access-paradigm"></a>프로그래밍 방식 액세스 페러다임

이 다이어그램에서는 새 보고서 템플릿을 만들고, 사용자 지정 보고서를 예약 하 고, 오류 데이터를 검색 하는 데 사용 되는 API 호출 패턴을 보여 줍니다.

[ ![ 새 보고서 템플릿을 만들고, 사용자 지정 보고서를 예약 하 고, 오류 데이터를 검색 하는 데 사용 되는 API 호출 패턴을 보여 줍니다.](./media/analytics-programmatic-access/analytics-high-level-flow.png)](./media/analytics-programmatic-access/analytics-high-level-flow.png#lightbox) 
 ***그림 1: API 호출 패턴의 상위 수준 흐름***

이 목록에는 그림 1에 대 한 자세한 내용이 나와 있습니다.

1. 클라이언트 응용 프로그램은 [보고서 만들기 쿼리 API](#create-report-query-api)를 호출 하 여 사용자 지정 보고서 스키마/템플릿을 정의할 수 있습니다. 또는 `QueryId` [시스템 쿼리 목록](analytics-system-queries.md)에서 보고서 템플릿 ()을 사용할 수 있습니다.
1. 성공 시 보고서 템플릿 만들기 API는를 반환 합니다 `QueryId` .
1. 그런 다음 클라이언트 응용 프로그램은 [](#create-report-api) `QueryID` 보고서 시작 날짜, 반복 간격, 되풀이 및 선택적 콜백 URI와 함께를 사용 하 여 보고서 만들기 API를 호출 합니다.
1. 성공 시 [보고서 만들기 API](#create-report-api) 는를 반환 합니다 `ReportID` .
1. 클라이언트 응용 프로그램은 보고서 데이터를 다운로드할 준비가 되는 즉시 콜백 URI에서 알림 메시지를 받습니다.
1. 그러면 클라이언트 응용 프로그램에서는 [보고서 실행 가져오기 API](#get-report-executions-api) 를 사용 하 여 및 날짜 범위를 사용 하 여 보고서 상태를 쿼리 합니다 `Report ID` .
1. 성공 하면 보고서 다운로드 링크가 반환 되 고 응용 프로그램에서 데이터 다운로드를 시작할 수 있습니다.

## <a name="report-query-language-specification"></a>보고서 쿼리 언어 사양

보고서를 만드는 데 사용할 수 있는 [시스템 쿼리](analytics-system-queries.md) 를 제공 하는 반면 비즈니스 요구에 따라 고유한 쿼리를 만들 수도 있습니다. 사용자 지정 쿼리에 대 한 자세한 내용은 [사용자 지정 쿼리 사양](analytics-custom-query-specification.md)을 참조 하세요.

## <a name="create-report-query-api"></a>보고서 쿼리 API 만들기

이 API는 열과 메트릭을 내보내야 하는 데이터 집합을 정의 하는 사용자 지정 쿼리를 만드는 데 도움이 됩니다. API는 비즈니스 요구에 따라 새 보고 템플릿을 만들 수 있는 유연성을 제공 합니다.

사용자가 제공 하는 [시스템 쿼리](analytics-system-queries.md) 를 사용할 수도 있습니다. 사용자 지정 보고서 템플릿이 필요 하지 않은 경우에는 사용자가 제공 하는 시스템 쿼리의 [Queryids](analytics-system-queries.md) 를 사용 하 여 [보고서 만들기 API](#create-report-api) 를 직접 호출할 수 있습니다.

다음 예에서는 지난 달의 [Isvusage](analytics-make-your-first-api-call.md#programmatic-api-call) 데이터 집합에서 _유료 sku에 대 한 정규화 된 사용량 및 예상 재무 요금_ 을 가져오는 사용자 지정 쿼리를 만드는 방법을 보여 줍니다.

*요청 구문*

| 방법 | 요청 URI |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries` |
|||

*요청 헤더*

| 헤더 | Type | Description |
| ------------- | ------------- | ------------- |
| 권한 부여 | 문자열 | 필수 요소. Azure Active Directory (Azure AD) 액세스 토큰입니다. 형식은 `Bearer <token>`입니다. |
| 콘텐츠 형식 | `string` | `application/JSON` |
||||

*Path 매개 변수*

없음

*쿼리 매개 변수*

없음

*요청 페이로드 예*

```json
{
    "Name": "ISVUsageQuery",
    "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
    "Query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"
}
```

*용어 설명*

이 표에서는 요청 페이로드에 있는 요소의 키 정의를 제공 합니다.

| 매개 변수 | 필수 | Description | 허용되는 값 |
| ------------ | ------------- | ------------- | ------------- |
| `Name` | 예 | 쿼리의 이름 | 문자열 |
| `Description` | No | 쿼리가 반환 하는 내용에 대 한 설명 | 문자열 |
| `Query` | 예 | 보고서 쿼리 문자열 | 데이터 형식: 문자열<br>비즈니스 요구를 기반으로 하는 [사용자 지정 쿼리](analytics-sample-queries.md) |
|||||

> [!NOTE]
> 사용자 지정 쿼리 예제는 [샘플 쿼리 예제](analytics-sample-queries.md)를 참조 하세요.

*샘플 응답*

응답 페이로드는 다음과 같이 구성 됩니다.

응답 코드: 200, 400, 401, 403, 500

응답 페이로드 예:

```json
{
  "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": " ISVUsageQuery",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs”,
            "query": " SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34Z"
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

*용어 설명*

이 표에서는 응답의 요소에 대 한 주요 정의를 제공 합니다.

| 매개 변수 | 설명 |
| ------------ | ------------- |
| `QueryId` | 만든 쿼리의 UUID (범용 고유 식별자) |
| `Name` | 요청 페이로드의 쿼리에 지정 된 이름입니다. |
| `Description` | 쿼리를 만드는 동안 제공 된 설명입니다. |
| `Query` | 쿼리를 만드는 동안 입력으로 전달 된 보고서 쿼리 |
| `Type` | `userDefined` |
| `User` | 쿼리를 만드는 데 사용 되는 사용자 ID |
| `CreatedTime` | 다음 형식으로 쿼리를 만든 UTC 시간: yyyy-mm-dd: mm-Yyyy-mm-ddthh: MM: ssZ |
| `TotalCount` | 값 배열의 데이터 집합 수 |
| `StatusCode` | 결과 코드<br>가능한 값은 200, 400, 401, 403, 500입니다. |
| `message` | API 실행의 상태 메시지 |
|||

## <a name="create-report-api"></a>보고서 API 만들기

`QueryID` [보고서 작성 쿼리](#create-report-query-api) 응답의 일부로 사용자 지정 보고서 템플릿을 만들고를 받으면이 API를 호출 하 여 정기적으로 실행 되도록 쿼리를 예약할 수 있습니다. 보고서가 배달 되는 빈도 및 일정을 설정할 수 있습니다. 제공 하는 시스템 쿼리의 경우 [QueryId](analytics-sample-queries.md)를 사용 하 여 보고서 만들기 API를 호출할 수도 있습니다.

*요청 구문*

| 방법 | 요청 URI |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport` |
|||

*요청 헤더*

| 헤더 | Type | Description |
| ------ | ---- | ----------- |
| 권한 부여 | 문자열 | 필수 요소. Azure Active Directory (Azure AD) 액세스 토큰입니다. 형식은 `Bearer <token>`입니다. |
| 콘텐츠 유형 | 문자열 | `application/JSON` |
||||

*Path 매개 변수*

없음

*쿼리 매개 변수*

없음

*요청 페이로드 예*

```json
{
  "ReportName": "ISVUsageReport",
  "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
  "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c ",
  "StartTime": "2021-01-06T19:00:00Z ",
  "RecurrenceInterval": 48,
  "RecurrenceCount": 20,
  "Format": "csv",
  "CallbackUrl": "https://<SampleCallbackUrl>"
}
```

*용어 설명*

이 표에서는 요청 페이로드에 있는 요소의 키 정의를 제공 합니다.

| 매개 변수 | 필수 | Description | 허용되는 값 |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | 예 | 보고서에 할당 된 이름 | 문자열 |
| `Description` | No | 만든 보고서에 대 한 설명 | 문자열 |
| `QueryId` | 예 | 보고서 쿼리 ID | 문자열 |
| `StartTime` | 예 | 보고서 생성이 시작 되는 UTC 타임 스탬프입니다.<br>형식: yyyy-mm-dd: mm-Yyyy-mm-ddthh: MM: ssZ | 문자열 |
| `RecurrenceInterval` | 예 | 보고서를 생성 해야 하는 빈도입니다.<br>최소값은 4이 고 최대값은 90입니다. | 정수 |
| `RecurrenceCount` | 아니요 | 생성할 보고서 수입니다. | 정수 |
| `Format` | 아니요 | 내보낸 파일의 파일 형식입니다.<br>기본 형식은입니다. CSV. | CSV/TSV |
| `CallbackUrl` | 아니요 | 필요에 따라 콜백 대상으로 구성할 수 있는 공개적으로 연결할 수 있는 URL입니다. | 문자열 (http URL) |
| `ExecuteNow` | 아니요 | 이 매개 변수는 한 번만 실행 되는 보고서를 만드는 데 사용 해야 합니다. `StartTime``RecurrenceInterval` `RecurrenceCount` 로 설정 된 경우, 및는 무시 됩니다 `true` . 보고서는 비동기 방식으로 즉시 실행 됩니다. | true/false |
| `QueryStartTime` | 아니요 | 필요에 따라 데이터를 추출 하는 쿼리의 시작 시간을 지정 합니다. 이 매개 변수는가로 설정 된 일회성 실행 보고서에만 적용 됩니다 `ExecuteNow` `true` . 서식 지정은 yyyy-mm-dd: mm: Yyyy-mm-ddthh: MM: ssZ | 문자열로 된 타임 스탬프 |
| `QueryEndTime` | 아니요 | 필요에 따라 데이터를 추출 하는 쿼리의 종료 시간을 지정 합니다. 이 매개 변수는가로 설정 된 일회성 실행 보고서에만 적용 됩니다 `ExecuteNow` `true` . 서식 지정은 yyyy-mm-dd: mm: Yyyy-mm-ddthh: MM: ssZ | 문자열로 된 타임 스탬프 |
|||||

*샘플 응답*

응답 페이로드는 다음과 같이 구성 됩니다.

응답 코드: 200, 400, 401, 403, 404, 500

응답 페이로드:

```json
{
  "Value": [
    {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVUsageReport",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": "https://<SampleCallbackUrl>",
            "format": "csv"    
    }
  ],
  "TotalCount": 1,
  "Message": "Report created successfully",
  "StatusCode": 200
}
```

*용어 설명*

이 표에서는 응답의 요소에 대 한 주요 정의를 제공 합니다.

| 매개 변수 | 설명 |
| ------------ | ------------- |
| `ReportId` | 만든 보고서의 UUID (범용 고유 식별자) |
| `ReportName` | 요청 페이로드의 보고서에 지정 된 이름입니다. |
| `Description` | 보고서를 만드는 동안 제공 된 설명입니다. |
| `QueryId` | 보고서를 만들 때 전달 되는 쿼리 ID입니다. |
| `Query` | 이 보고서에 대해 실행 되는 쿼리 텍스트 |
| `User` | 보고서를 만드는 데 사용 되는 사용자 ID |
| `CreatedTime` | 보고서를 만든 UTC 시간 (yyyy-MM-Yyyy-mm-ddthh: MM: ssZ 형식) |
| `ModifiedTime` | 보고서를 마지막으로 수정한 UTC 시간 (yyyy-MM-Yyyy-mm-ddthh: MM: ssZ) |
| `StartTime` | 보고서 실행이 시작 되는 UTC 시간 (yyyy-MM-Yyyy-mm-ddthh: MM: ssZ 형식) |
| `ReportStatus` | 보고서 실행의 상태입니다. 가능한 값은 **일시 중지**, **활성** 및 **비활성** 입니다. |
| `RecurrenceInterval` | 보고서를 만드는 동안 제공 된 되풀이 간격 |
| `RecurrenceCount` | 보고서를 만드는 동안 제공 된 되풀이 횟수 |
| `CallbackUrl` | 요청에 제공 된 콜백 URL |
| `Format` | 보고서 파일의 형식입니다. 가능한 값은 CSV 또는 TSV입니다. |
| `TotalCount` | 값 배열의 데이터 집합 수 |
| `StatusCode` | 결과 코드<br>가능한 값은 200, 400, 401, 403, 500입니다. |
| `message` | API 실행의 상태 메시지 |
|||

## <a name="get-report-executions-api"></a>보고서 실행 API 가져오기

이 메서드를 사용 하 여 `ReportId` [보고서 만들기 API](#create-report-api)에서 받은를 사용 하 여 보고서 실행 상태를 쿼리할 수 있습니다. 이 메서드는 보고서를 다운로드할 준비가 된 경우 보고서 다운로드 링크를 반환 합니다. 그렇지 않으면 메서드는 상태를 반환 합니다. 이 API를 사용 하 여 지정 된 보고서에 대해 발생 한 모든 실행을 가져올 수도 있습니다.

> [!IMPORTANT]
> 이 API에는 및에 대해 설정 된 기본 쿼리 매개 변수가 있습니다 `executionStatus=Completed`  `getLatestExecution=true` . 따라서 보고서를 처음으로 실행 하기 전에 API를 호출 하면 404이 반환 됩니다. 보류 중인 실행은를 설정 하 여 가져올 수 있습니다 `executionStatus=Pending` .

*요청 구문*

| 방법 | 요청 URI |
| ------------ | ------------- |
| 가져오기 | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/{reportId}?executionId={executionId}&executionStatus={executionStatus}&getLatestExecution={getLatestExecution}` |
|||

*요청 헤더*

| 헤더 | Type | Description |
| ------ | ------ | ------ |
| 권한 부여 | 문자열 | 필수 요소. Azure Active Directory (Azure AD) 액세스 토큰입니다. 형식은 `Bearer <token>`입니다. |
| 내용 유형 | 문자열 | `application/json` |
||||

*Path 매개 변수*

없음

*쿼리 매개 변수*

| 매개 변수 이름 | 필수 | Type | 설명 |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | 예 | 문자열 | 이 인수에 지정 된를 사용 하는 보고서에 대 한 실행 세부 정보를 가져오려면 필터를 사용 `reportId` 합니다. Multiple은 `reportIds` 세미콜론 ";"으로 구분 하 여 지정할 수 있습니다. |
| `executionId` | 예 | 문자열 | 이 인수에 지정 된를 사용 하 여 보고서의 세부 정보만 가져오도록 필터링 `executionId` 합니다. Multiple은 `executionIds` 세미콜론 ";"으로 구분 하 여 지정할 수 있습니다. |
| `executionStatus` | 아니요 | 문자열/열거형 | 이 인수에 지정 된를 사용 하 여 보고서의 세부 정보만 가져오도록 필터링 `executionStatus` 합니다.<br>유효한 값은 `Pending` ,, `Running` `Paused` 및입니다. `Completed` <br>기본값은 `Completed`입니다. 여러 상태를 세미콜론 (";")으로 구분 하 여 지정할 수 있습니다. |
| `getLatestExecution` | 아니요 | boolean | API는 최신 보고서 실행에 대 한 세부 정보를 반환 합니다.<br>이 매개 변수는 기본적으로 `true`로 설정됩니다. 이 매개 변수의 값을로 전달 하도록 선택 하는 경우 `false` API는 최근 90 일 실행 인스턴스를 반환 합니다. |
|||||

*요청 페이로드*

없음

*샘플 응답*

응답 페이로드는 다음과 같이 구성 됩니다.

응답 코드: 200, 400, 401, 403, 404, 500

응답 페이로드 예:

```json
{
    "value": [
        {
            "executionId": "a0bd78ad-1a05-40fa-8847-8968b718d00f",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 4,
            "recurrenceCount": 10,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Completed",
            "reportAccessSecureLink": "https://<path to report for download>",
            "reportExpiryTime": null,
            "reportGeneratedTime": "2021-01-13T14:40:46Z"
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

보고서 실행이 완료 되 면 실행 상태가 `Completed` 표시 됩니다. 에서 URL을 선택 하 여 보고서를 다운로드할 수 있습니다 `reportAccessSecureLink` .

*용어 설명*

응답에 있는 요소의 키 정의입니다.

| 매개 변수 | 설명 |
| ------------ | ------------- |
| `ExecutionId` | 실행 인스턴스의 UUID (범용 고유 식별자) |
| `ReportId` | 실행 인스턴스와 연결 된 보고서 ID |
| `RecurrenceInterval` | 보고서를 만드는 동안 제공 된 되풀이 간격 |
| `RecurrenceCount` | 보고서를 만드는 동안 제공 된 되풀이 횟수 |
| `CallbackUrl` | 실행 인스턴스와 연결 된 콜백 URL |
| `Format` | 실행이 끝날 때 생성 된 파일의 형식입니다. |
| `ExecutionStatus` | 보고서 실행 인스턴스의 상태입니다.<br>유효한 값은 `Pending` ,, `Running` `Paused` 및입니다. `Completed` |
| `ReportAccessSecureLink` | 보고서를 안전 하 게 액세스할 수 있는 링크 |
| `ReportExpiryTime` | 보고서 링크가 만료 될 때 까지의 UTC 시간 (yyyy-MM-Yyyy-mm-ddthh: MM: ssZ 형식) |
| `ReportGeneratedTime` | 보고서가 생성 된 UTC 시간 (yyyy-MM-Yyyy-mm-ddthh: MM: ssZ) |
| `TotalCount` | 값 배열의 데이터 집합 수 |
| `StatusCode` | 결과 코드 <br>가능한 값은 200, 400, 401, 403, 404 및 500입니다. |
| `message` | API 실행의 상태 메시지 |
|||

## <a name="next-steps"></a>다음 단계
- [SWAGGER API URL](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html) 을 통해 api를 사용해 볼 수 있습니다.
- 분석 데이터에 대 한 프로그래밍 방식 액세스 시작
