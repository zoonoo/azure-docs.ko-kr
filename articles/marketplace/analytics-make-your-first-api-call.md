---
title: 상용 marketplace 분석 데이터에 액세스 하기 위한 첫 번째 API 호출 만들기
description: 상용 marketplace 분석 데이터에 액세스 하기 위해 API를 사용 하는 방법에 대 한 예입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 2d0c0e7322ecb92fd371f5bf7924a370dd29fe85
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583981"
---
# <a name="make-your-first-api-call-to-access-commercial-marketplace-analytics-data"></a>상용 marketplace 분석 데이터에 액세스 하기 위한 첫 번째 API 호출 만들기

상용 marketplace 분석 데이터에 액세스 하기 위한 Api 목록은 [상업용 marketplace 분석 데이터에 액세스 하기 위한 api](analytics-available-apis.md)를 참조 하세요. 첫 번째 API 호출을 수행 하기 전에 [사전 요구](analytics-prerequisites.md) 사항을 충족 하 여 상업적 marketplace 분석 데이터에 프로그래밍 방식으로 액세스 해야 합니다.

## <a name="token-generation"></a>토큰 생성

메서드를 호출 하기 전에 먼저 Azure Active Directory (Azure AD) 액세스 토큰을 가져와야 합니다. API에서 각 메서드의 인증 헤더에 Azure AD 액세스 토큰을 전달 해야 합니다. 액세스 토큰을 가져온 후 만료 되기 전에 사용 하는 데 60 분이 있습니다. 토큰이 만료 되 면 토큰을 새로 고치고 API에 대 한 추가 호출에 계속 사용할 수 있습니다.

토큰을 생성 하는 방법에 대 한 예제 요청은 아래를 참조 하세요. 토큰을 생성 하는 데 필요한 세 가지 값은 `clientId` , `clientSecret` 및 `tenantId` 입니다. `resource`매개 변수는로 설정 해야 합니다 `https://graph.windows.net` .

***요청 예제***:

```json
curl --location --request POST 'https://login.microsoftonline.com/{TenantId}/oauth2/token' \
--header 'return-client-request-id: true' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'resource=https://graph.windows.net' \
--data-urlencode 'client_id={client_id}' \
--data-urlencode 'client_secret={client_secret}' \
--data-urlencode 'grant_type=client_credentials'
```

***응답 예제***:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1612794445",
    "not_before": "1612790545",
    "resource": "https://graph.windows.net",
    "access_token": {Token}
}
```

응용 프로그램에 대 한 Azure AD 토큰을 얻는 방법에 대 한 자세한 내용은 [스토어 서비스를 사용 하 여 분석 데이터 액세스](https://docs.microsoft.com/windows/uwp/monetize/access-analytics-data-using-windows-store-services#step-2-obtain-an-azure-ad-access-token)를 참조 하세요.

## <a name="programmatic-api-call"></a>프로그래밍 방식 API 호출

이전 섹션에 설명 된 대로 Azure AD 토큰을 가져온 후에는 다음 단계에 따라 첫 번째 프로그래밍 방식 액세스 보고서를 만듭니다.

다음 데이터 집합 (datasetName)에서 데이터를 다운로드할 수 있습니다.

- ISVCustomer
- ISVMarketplaceInsights
- ISVUsage
- ISVOrder

다음 섹션에서는 `OrderId` ISVOrder 데이터 집합에서 프로그래밍 방식으로 액세스 하는 방법에 대 한 예제를 제공 합니다.

### <a name="step-1-make-a-rest-call-using-the-get-datasets-api"></a>1 단계: 데이터 집합 가져오기 API를 사용 하 여 REST 호출

API 응답은 보고서를 다운로드할 수 있는 데이터 집합 이름을 제공 합니다. 특정 데이터 집합의 경우 API 응답은 사용자 지정 보고서 템플릿에 사용할 수 있는 선택 가능한 열 목록도 제공 합니다. 다음 표를 참조 하 여 열의 이름을 가져올 수도 있습니다.

- [주문 정보 테이블](orders-dashboard.md#orders-details-table)
- [사용 정보 테이블](usage-dashboard.md#usage-details-table)
- [고객 정보 표](customer-dashboard.md#customer-details-table)
- [Marketplace insights 정보 테이블](insights-dashboard.md#marketplace-insights-details-table)

***요청 예제***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset ' \ 
--header 'Authorization: Bearer <AzureADToken>'
```

***응답 예제***:

```json
{
    "value": [
        {
            "datasetName": "ISVOrder",
            "selectableColumns": [
                "MarketplaceSubscriptionId",
                "MonthStartDate",
                "OfferType",
                "AzureLicenseType",
                "Sku",
                "CustomerCountry",
                "IsPreviewSKU",
                "OrderId",
                "OrderQuantity",
                "CloudInstanceName",
                "IsNewCustomer",
                "OrderStatus",
                "OrderCancelDate",
                "CustomerCompanyName",
                "CustomerName",
                "OrderPurchaseDate",
                "OfferName",
                "TrialEndDate",
                "CustomerId",
                "BillingAccountId"
            ],
            "availableMetrics": [],
            "availableDateRanges": [
                "LAST_MONTH",
                "LAST_3_MONTHS",
                "LAST_6_MONTHS",
                "LIFETIME"
            ]
        },
    ],
    "totalCount": 1,
    "message": "Dataset fetched successfully",
    "statusCode": 200
}
```

### <a name="step-2-create-the-custom-query"></a>2 단계: 사용자 지정 쿼리 만들기

이 단계에서는 주문 보고서의 주문 ID를 사용 하 여 원하는 보고서에 대 한 사용자 지정 쿼리를 만듭니다. `timespan`쿼리에 지정 되지 않은 경우 기본값은 6 개월입니다.

***요청 예제***:

```json
curl 
--location 
--request POST ' https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries' \ 
--header ' Authorization: Bearer <AzureAD_Token>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
            '{ 
                "Query": "SELECT OrderId from ISVOrder", 
                "Name": "ISVOrderQuery1", 
                "Description": "Get a list of all Order IDs" 
             }'
```

***응답 예제***:

```json
{
    "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": "ISVOrderQuery1",
            "description": "Get a list of all Order IDs”,
            "query": "SELECT OrderId from ISVOrder",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34",
            "modifiedTime": null
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

쿼리가 성공적으로 실행 되 면 보고서를 `queryId` 생성 하는 데 사용 해야 하는이 생성 됩니다.

### <a name="step-3-execute-test-query-api"></a>3 단계: 테스트 쿼리 API 실행

이 단계에서는 테스트 쿼리 API를 사용 하 여 생성 된 쿼리에 대 한 상위 100 행을 가져옵니다.

***요청 예제***:

```json
curl 
--location 
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery=SELECT%20OrderId%20from%20ISVOrder' \ 
--header ' Authorization: Bearer <AzureADToken>'
```

***응답 예제***:

```json
{
    "value": [
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2ba8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bb8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bc8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bd8"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2be8"
        },
               .
               .
               .

        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf0"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf1"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf2"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf3"
        },
        {
            "OrderId": "086365c6-9c38-4fba-904a-6228f6cb2bf4"
        }
    ],
    "totalCount": 100,
    "message": null,
    "statusCode": 200
}
```

### <a name="step-4-create-the-report"></a>4 단계: 보고서 만들기

이 단계에서는 이전에 생성 된를 사용 `QueryId` 하 여 보고서를 만듭니다.

***요청 예제***:

```json
curl 
--location 
--request POST 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport' \ 
--header ' Authorization: Bearer <AzureADToken>' \ 
--header 'Content-Type: application/json' \ 
--data-raw 
                 '{
                   “ReportName": "ISVReport1",
                   "Description": "Report for getting list of Order Ids",
                   "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
                   "StartTime": "2021-01-06T19:00:00Z”,
                   "RecurrenceInterval": 48,
                   "RecurrenceCount": 20,
                    "Format": "csv"
                  }'
```

<br>

_**표 1:이 요청 예제에 사용 된 매개 변수에 대 한 설명**_

| 매개 변수 | 설명 |
| ------------ | ------------- |
| `Description` | 생성 되는 보고서에 대 한 간략 한 설명을 제공 합니다. |
| `QueryId` | `queryId`2 단계: 사용자 지정 쿼리 만들기에서 쿼리를 만들 때 생성 된입니다. |
| `StartTime` | 첫 번째 보고서 실행이 시작 된 시간입니다. |
| `RecurrenceInterval` | 보고서를 만드는 동안 제공 된 되풀이 간격입니다. |
| `RecurrenceCount` | 보고서를 만드는 동안 제공 된 되풀이 횟수입니다. |
| `Format` | CSV 및 TSV 파일 형식이 지원 됩니다. |
|||

***응답 예제***:

```json
{
    "value": [
        {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVReport1",
            "description": "Report for getting list of Order Ids",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT OrderId from ISVOrder",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv"
        }
    ],
    "totalCount": 1,
    "message": "Report created successfully",
    "statusCode": 200
}
```

성공적으로 실행 되 면 `reportId` 보고서의 다운로드를 예약 하는 데 사용 해야 하는이 생성 됩니다.

### <a name="step-5-execute-report-executions-api"></a>5 단계: 보고서 실행 API 실행

이제 보고서의 보안 위치 (URL)를 가져오기 위해 보고서 실행 API를 실행 합니다.

***요청 예제***:

```json
Curl
--location
--request GET 'https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/72fa95ab-35f5-4d44-a1ee-503abbc88003' \
--header ' Authorization: Bearer <AzureADToken>' \
```

***응답 예제***:

```json
{
    "value": [
        {
            "executionId": "1f18b53b-df30-4d98-85ee-e6c7e687aeed",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Pending",
            "reportAccessSecureLink": null,
            "reportExpiryTime": null,
            "reportGeneratedTime": null
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

## <a name="next-steps"></a>다음 단계

- [SWAGGER API URL](https://partneranalytics-api.azure-api.net/analytics/cmp/swagger/index.html) 을 통해 api를 사용해 볼 수 있습니다.
- [프로그래밍 방식 액세스 패러다임](analytics-programmatic-access.md)
