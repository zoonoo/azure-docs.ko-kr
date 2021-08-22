---
title: 프로그래밍 방식 액세스 페러다임
description: 프로그래밍 분석을 위한 API 호출 패턴의 대략적인 흐름을 이해합니다. Microsoft 상업용 Marketplace의 분석 보고서에 액세스하는 데 사용되는 API에 대해서도 설명합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 6ed2be363a9e50184d79c4f9870942030fd485eb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529275"
---
# <a name="programmatic-access-paradigm"></a>프로그래밍 방식 액세스 페러다임

이 다이어그램에서는 새 보고서 템플릿을 만들고, 사용자 지정 보고서를 예약하고, 오류 데이터를 검색하는 데 사용되는 API 호출 패턴을 보여 줍니다.

[![새 보고서 템플릿을 만들고, 사용자 지정 보고서를 예약하고, 오류 데이터를 검색하는 데 사용되는 API 호출 패턴을 보여 줍니다.](./media/analytics-programmatic-access/analytics-high-level-flow.png)](./media/analytics-programmatic-access/analytics-high-level-flow.png#lightbox)
***그림 1: API 호출 패턴의 대략적인 흐름***

이 목록에는 그림 1에 대한 자세한 내용이 나와 있습니다.

1. 클라이언트 애플리케이션은 [보고서 쿼리 만들기 API](#create-report-query-api)를 호출하여 사용자 지정 보고서 스키마/템플릿을 정의할 수 있습니다. 또는 [시스템 쿼리 목록](analytics-system-queries.md)의 보고서 템플릿(`QueryId`)을 사용할 수 있습니다.
1. 성공 시 보고서 템플릿 만들기 API가 `QueryId`를 반환합니다.
1. 그러면 클라이언트 애플리케이션이 보고서 시작 날짜, 반복 간격, 되풀이 및 선택적 콜백 URI와 함께 `QueryID`를 사용하여 보고서 [만들기 API](#create-report-api)를 호출합니다.
1. 성공 시 [보고서 만들기 API](#create-report-api)가 `ReportID`를 반환합니다.
1. 클라이언트 애플리케이션은 보고서 데이터를 다운로드할 준비가 되는 즉시 콜백 URI에서 알림을 받습니다.
1. 그러면 클라이언트 애플리케이션이 [보고서 실행 가져오기 API](#get-report-executions-api)를 사용하여 `Report ID` 및 날짜 범위로 보고서 상태를 쿼리합니다.
1. 성공 시 보고서 다운로드 링크가 반환되고 애플리케이션에서 데이터 다운로드를 시작할 수 있습니다.

## <a name="report-query-language-specification"></a>보고서 쿼리 언어 사양

보고서를 만드는 데 사용할 수 있는 [시스템 쿼리](analytics-system-queries.md)가 제공되지만, 비즈니스 요구 사항에 따라 고유한 쿼리를 만들 수도 있습니다. 사용자 지정 쿼리에 대해 자세히 알아보려면 [사용자 지정 쿼리 사양](analytics-custom-query-specification.md)을 참조하세요.

## <a name="create-report-query-api"></a>보고서 쿼리 만들기 API

이 API는 열과 메트릭을 내보내야 하는 데이터 세트를 정의하는 사용자 지정 쿼리를 만드는 데 도움이 됩니다. 이 API는 비즈니스 요구에 따라 새 보고 템플릿을 만들 수 있는 유연성을 제공합니다.

기본 제공되는 [시스템 쿼리](analytics-system-queries.md)를 사용할 수도 있습니다. 사용자 지정 보고서 템플릿이 필요하지 않은 경우에는 기본 제공되는 시스템 쿼리의 [QueryIds](analytics-system-queries.md)를 사용하여 [보고서 만들기 API](#create-report-api)를 직접 호출할 수 있습니다.

다음 예에서는 지난달의 [ISVUsage](analytics-make-your-first-api-call.md#programmatic-api-call) 데이터 세트에서 _유료 SKU의 정규화된 사용량 및 예상 재정 부담_ 을 가져오는 사용자 지정 쿼리를 만드는 방법을 보여 줍니다.

*요청 구문*

| 방법 | 요청 URI |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries` |
|||

*요청 헤더*

| 헤더 | 형식 | Description |
| ------------- | ------------- | ------------- |
| 권한 부여 | 문자열 | 필수 요소. Azure AD(Azure Active Directory) 액세스 토큰입니다. 형식은 `Bearer <token>`입니다. |
| 콘텐츠 형식 | `string` | `application/JSON` |
||||

*경로 매개 변수*

None

*쿼리 매개 변수*

None

*요청 페이로드 예제*

```json
{
    "Name": "ISVUsageQuery",
    "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
    "Query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"
}
```

*용어 설명*

이 표에는 요청 페이로드에 있는 요소의 주요 정의가 나열되어 있습니다.

| 매개 변수 | 필수 | Description | 허용되는 값 |
| ------------ | ------------- | ------------- | ------------- |
| `Name` | Yes | 쿼리의 식별 이름 | 문자열 |
| `Description` | No | 쿼리가 반환하는 내용에 대한 설명 | 문자열 |
| `Query` | Yes | 보고서 쿼리 문자열 | 데이터 형식: 문자열<br>비즈니스 요구 사항에 따른 [사용자 지정 쿼리](analytics-sample-queries.md) |
|||||

> [!NOTE]
> 사용자 지정 쿼리 샘플은 [샘플 쿼리 예제](analytics-sample-queries.md)를 참조하세요.

*샘플 응답*

응답 페이로드는 다음과 같이 구성됩니다.

응답 코드: 200, 400, 401, 403, 500

응답 페이로드 예제:

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

이 표에는 응답에 있는 요소의 주요 정의가 나와 있습니다.

| 매개 변수 | 설명 |
| ------------ | ------------- |
| `QueryId` | 만든 쿼리의 UUID(범용 고유 식별자) |
| `Name` | 요청 페이로드의 쿼리에 지정된 식별 이름 |
| `Description` | 쿼리를 만드는 동안 제공된 설명 |
| `Query` | 쿼리를 만드는 동안 입력으로 전달된 보고서 쿼리 |
| `Type` | `userDefined` |
| `User` | 쿼리를 만드는 데 사용되는 사용자 ID |
| `CreatedTime` | 쿼리가 생성된 UTC 시간(yyyy-MM-ddTHH:mm:ssZ 형식) |
| `TotalCount` | 값 배열의 데이터 세트 수 |
| `StatusCode` | 결과 코드<br>가능한 값은 200, 400, 401, 403, 500입니다. |
| `message` | API 실행의 상태 메시지 |
|||

## <a name="create-report-api"></a>보고서 만들기 API

사용자 지정 보고서 템플릿을 성공적으로 만들고 [보고서 쿼리 만들기](#create-report-query-api) 응답의 일부로 `QueryID`를 받으면 이 API를 호출하여 쿼리를 정기적으로 실행하도록 예약할 수 있습니다. 보고서가 배달되는 빈도 및 일정을 설정할 수 있습니다. 기본 제공되는 시스템 쿼리의 경우 [QueryId](analytics-sample-queries.md)를 사용하여 보고서 만들기 API를 호출할 수도 있습니다.

*요청 구문*

| 방법 | 요청 URI |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport` |
|||

*요청 헤더*

| 헤더 | 형식 | Description |
| ------ | ---- | ----------- |
| 권한 부여 | 문자열 | 필수 요소. Azure AD(Azure Active Directory) 액세스 토큰입니다. 형식은 `Bearer <token>`입니다. |
| 콘텐츠 유형 | 문자열 | `application/JSON` |
||||

*경로 매개 변수*

None

*쿼리 매개 변수*

None

*요청 페이로드 예제*

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

이 표에는 요청 페이로드에 있는 요소의 주요 정의가 나열되어 있습니다.

| 매개 변수 | 필수 | Description | 허용되는 값 |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Yes | 보고서에 할당된 이름 | 문자열 |
| `Description` | No | 생성된 보고서에 대한 설명 | 문자열 |
| `QueryId` | Yes | 보고서 쿼리 ID | 문자열 |
| `StartTime` | Yes | 보고서 생성이 시작되는 UTC 타임스탬프입니다.<br>yyyy-MM-ddTHH:mm:ssZ 형식이어야 합니다. | 문자열 |
| `RecurrenceInterval` | Yes | 보고서가 생성되어야 하는 빈도(시간)입니다.<br>최솟값은 4이고 최댓값은 90입니다. | 정수 |
| `RecurrenceCount` | 아니요 | 생성할 보고서 수입니다. | 정수 |
| `Format` | 아니요 | 내보낸 파일의 파일 형식입니다.<br>기본 형식은 .CSV입니다. | CSV/TSV |
| `CallbackUrl` | 아니요 | 필요에 따라 콜백 대상으로 구성할 수 있는 공개적으로 연결할 수 있는 URL입니다. | 문자열(http URL) |
| `ExecuteNow` | 아니요 | 이 매개 변수는 한 번만 실행되는 보고서를 만드는 데 사용해야 합니다. 이 매개 변수가 `true`로 설정된 경우, `StartTime`, `RecurrenceInterval` 및 `RecurrenceCount`는 무시됩니다. 보고서는 비동기 방식으로 즉시 실행됩니다. | true/false |
| `QueryStartTime` | 아니요 | 필요에 따라 데이터를 추출하는 쿼리의 시작 시간을 지정합니다. 이 매개 변수는 `ExecuteNow`가 `true`로 설정된 일회성 실행 보고서에만 적용됩니다. yyyy-MM-ddTHH:mm:ssZ 형식이어야 합니다. | 문자열 타임스탬프 |
| `QueryEndTime` | 아니요 | 필요에 따라 데이터를 추출하는 쿼리의 종료 시간을 지정합니다. 이 매개 변수는 `ExecuteNow`가 `true`로 설정된 일회성 실행 보고서에만 적용됩니다. yyyy-MM-ddTHH:mm:ssZ 형식이어야 합니다. | 문자열 타임스탬프 |
|||||

*샘플 응답*

응답 페이로드는 다음과 같이 구성됩니다.

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

이 표에는 응답에 있는 요소의 주요 정의가 나와 있습니다.

| 매개 변수 | 설명 |
| ------------ | ------------- |
| `ReportId` | 만든 보고서의 UUID(범용 고유 식별자) |
| `ReportName` | 요청 페이로드의 보고서에 지정된 이름 |
| `Description` | 보고서를 만드는 동안 제공된 설명 |
| `QueryId` | 보고서를 만들 때 전달된 쿼리 ID |
| `Query` | 이 보고서에 대해 실행되는 쿼리 텍스트 |
| `User` | 보고서를 만드는 데 사용되는 사용자 ID |
| `CreatedTime` | 보고서가 생성된 UTC 시간(yyyy-MM-ddTHH:mm:ssZ 형식) |
| `ModifiedTime` | 보고서가 마지막으로 수정된 UTC 시간(yyyy-MM-ddTHH:mm:ssZ 형식) |
| `StartTime` | 보고서 실행이 시작되는 UTC 시간(yyyy-MM-ddTHH:mm:ssZ 형식) |
| `ReportStatus` | 보고서 실행 상태입니다. 가능한 값은 **일시 중지**, **활성** 및 **비활성** 입니다. |
| `RecurrenceInterval` | 보고서를 만드는 동안 제공된 되풀이 간격 |
| `RecurrenceCount` | 보고서를 만드는 동안 제공된 되풀이 횟수 |
| `CallbackUrl` | 요청에 제공된 콜백 URL |
| `Format` | 보고서 파일의 형식입니다. 가능한 값은 CSV 또는 TSV입니다. |
| `TotalCount` | 값 배열의 데이터 세트 수 |
| `StatusCode` | 결과 코드<br>가능한 값은 200, 400, 401, 403, 500입니다. |
| `message` | API 실행의 상태 메시지 |
|||

## <a name="get-report-executions-api"></a>보고서 실행 가져오기 API

이 메서드를 사용하여 [보고서 만들기 API](#create-report-api)에서 수신된 `ReportId`로 보고서 실행 상태를 쿼리할 수 있습니다. 이 메서드는 보고서를 다운로드할 준비가 된 경우 보고서 다운로드 링크를 반환합니다. 그렇지 않으면 메서드가 상태를 반환합니다. 또한 이 API를 사용하여 특정 보고서에 대해 발생한 모든 실행을 가져올 수도 있습니다.

> [!IMPORTANT]
> 이 API는 `executionStatus=Completed` 및 `getLatestExecution=true`에 기본 쿼리 매개 변수가 설정되어 있습니다. 따라서 보고서 실행이 처음으로 성공하기 전에 API를 호출하면 404가 반환됩니다. `executionStatus=Pending`을 설정하여 보류 중인 실행을 가져올 수 있습니다.

*요청 구문*

| 방법 | 요청 URI |
| ------------ | ------------- |
| 가져오기 | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/{reportId}?executionId={executionId}&executionStatus={executionStatus}&getLatestExecution={getLatestExecution}` |
|||

*요청 헤더*

| 헤더 | 형식 | Description |
| ------ | ------ | ------ |
| 권한 부여 | 문자열 | 필수 요소. Azure AD(Azure Active Directory) 액세스 토큰입니다. 형식은 `Bearer <token>`입니다. |
| 내용 유형 | 문자열 | `application/json` |
||||

*경로 매개 변수*

None

*쿼리 매개 변수*

| 매개 변수 이름 | 필수 | Type | 설명 |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | 예 | 문자열 | 이 인수에 `reportId`가 지정된 보고서의 실행 세부 정보만 가져오도록 필터링합니다. 여러 개의 `reportIds`는 세미콜론 ";"으로 구분하여 지정할 수 있습니다. |
| `executionId` | 예 | 문자열 | 이 인수에 `executionId`가 지정된 보고서의 세부 정보만 가져오도록 필터링합니다. 여러 개의 `executionIds`는 세미콜론 ";"으로 구분하여 지정할 수 있습니다. |
| `executionStatus` | 아니요 | 문자열/열거형 | 이 인수에 `executionStatus`가 지정된 보고서의 세부 정보만 가져오도록 필터링합니다.<br>유효한 값은 `Pending`, `Running`, `Paused` 및 `Completed`입니다. <br>기본값은 `Completed`입니다. 여러 개의 상태는 세미콜론 ";"으로 구분하여 지정할 수 있습니다. |
| `getLatestExecution` | 아니요 | boolean | API가 최신 보고서 실행에 대한 세부 정보를 반환합니다.<br>이 매개 변수는 기본적으로 `true`로 설정됩니다. 이 매개 변수의 값을 `false`로 전달하도록 선택하면 API가 최근 90일 동안의 실행 인스턴스를 반환합니다. |
|||||

*요청 페이로드*

None

*샘플 응답*

응답 페이로드는 다음과 같이 구성됩니다.

응답 코드: 200, 400, 401, 403, 404, 500

응답 페이로드 예제:

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

보고서 실행이 완료되면 실행 상태 `Completed`가 표시됩니다. `reportAccessSecureLink`에서 URL을 선택하여 보고서를 다운로드할 수 있습니다.

*용어 설명*

응답에 있는 요소의 주요 정의입니다.

| 매개 변수 | 설명 |
| ------------ | ------------- |
| `ExecutionId` | 실행 인스턴스의 UUID(범용 고유 식별자) |
| `ReportId` | 실행 인스턴스와 연결된 보고서 ID |
| `RecurrenceInterval` | 보고서를 만드는 동안 제공된 되풀이 간격 |
| `RecurrenceCount` | 보고서를 만드는 동안 제공된 되풀이 수 |
| `CallbackUrl` | 실행 인스턴스와 연결된 콜백 URL |
| `Format` | 실행이 끝날 때 생성된 파일의 형식 |
| `ExecutionStatus` | 보고서 실행 인스턴스의 상태입니다.<br>유효한 값은 `Pending`, `Running`, `Paused` 및 `Completed`입니다. |
| `ReportAccessSecureLink` | 보고서에 안전하게 액세스하는 데 사용할 수 있는 링크 |
| `ReportExpiryTime` | 보고서 링크가 만료되는 UTC 시간(yyyy-MM-ddTHH:mm:ssZ 형식) |
| `ReportGeneratedTime` | 보고서가 생성된 UTC 시간(yyyy-MM-ddTHH:mm:ssZ 형식) |
| `TotalCount` | 값 배열의 데이터 세트 수 |
| `StatusCode` | 결과 코드 <br>가능한 값은 200, 400, 401, 403, 404 및 500입니다. |
| `message` | API 실행의 상태 메시지 |
|||

## <a name="next-steps"></a>다음 단계
- [Swagger API URL](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html)을 통해 API를 사용해 볼 수 있음
- [분석 데이터에 대한 프로그래매틱 액세스 시작](analytics-get-started.md)
