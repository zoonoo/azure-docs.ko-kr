---
title: 계량 서비스 API - Microsoft 상업용 마켓플레이스
description: 사용량 이벤트 API를 사용하여 Microsoft AppSource 및 Azure Marketplace에서 SaaS 제품에 대한 사용량 이벤트를 내보낼 수 있습니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/26/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: d4c1005d300a5b326ff2f41d9fa3838dbb1c7552
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92278016"
---
# <a name="marketplace-metered-billing-apis"></a>Marketplace 요금제 청구 Api

파트너가 파트너 센터에 게시할 제품에 대 한 사용자 지정 계량 차원을 만들 때 요금제 청구 Api를 사용 해야 합니다. 사용량 이벤트를 내보내는 사용자 지정 차원을 포함 하는 하나 이상의 계획이 있는 모든 구매한 제품에는 요금제 청구 Api와의 통합이 필요 합니다.

SaaS에 대 한 사용자 지정 계량 차원을 만드는 방법에 대 한 자세한 내용은 [saas 요금제](saas-metered-billing.md)를 참조 하세요.

관리 되는 앱 요금제를 사용 하 여 Azure 애플리케이션 제품에 대 한 사용자 지정 계량 차원을 만드는 방법에 대 한 자세한 내용은 [새 Azure 앱 만들기 제품의 기술 구성 섹션](create-new-azure-apps-offer.md#technical-configuration)을 참조 하세요.

## <a name="enforcing-tls-12-note"></a>TLS 1.2 메모 적용

TLS 버전 1.2 버전은 HTTPS 통신을 위한 최소 버전으로 적용 됩니다. 코드에서이 TLS 버전을 사용 하는지 확인 합니다. TLS 버전 1.0 및 1.1은 사용 되지 않으며 연결 시도는 거부 됩니다.

## <a name="metered-billing-single-usage-event"></a>요금제 청구 단일 사용량 이벤트

사용 이벤트 API는 특정 고객이 구매한 계획에 대해 활성 리소스 (구독)에 대 한 사용 이벤트를 내보내기 위해 게시자에서 호출 해야 합니다. 제품을 게시할 때 게시자가 정의한 계획의 각 사용자 지정 차원에 대해 사용 이벤트를 개별적으로 내보냅니다.

일정 날짜의 각 시간에 대해 하나의 사용 이벤트만 내보낼 수 있습니다. 예를 들어 오늘 오전 8 시 15 분에 한 사용 이벤트를 내보낼 수 있습니다. 이 이벤트가 수락 되 면 오늘 오전 9:00에 다음 사용 이벤트가 수락 됩니다. 현재 8:15과 8:59:59 사이에 추가 이벤트를 보내는 경우에는 중복으로 거부 됩니다. 한 시간에 사용 된 모든 단위를 누적 한 다음 단일 이벤트로 내보내야 합니다.

기간별 각 시간에 대해 하나의 사용 이벤트만 내보낼 수 있습니다. 한 시간에 두 개 이상의 단위를 사용 하는 경우에는 해당 시간에 사용 된 모든 단위를 누적 한 다음 단일 이벤트로 내보냅니다. 최근 24 시간 동안에만 사용 이벤트를 내보낼 수 있습니다. 언제 든 지 8:00과 8:59:59 사이의 사용 이벤트를 내보내고이를 허용 하 고 8:00과 8:59:59 사이에 같은 날에 대 한 추가 이벤트를 보내는 경우에는 중복으로 거부 됩니다.

**POST**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*쿼리 매개 변수:*

| 변수 | 권장          |
| ---------- | ---------------------- |
| `ApiVersion` | 2018-08-31을 사용 합니다. |
| | |

*요청 헤더:*

| Content-type       | `application/json` 사용  |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `x-ms-correlationid` | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `authorization`   | 이 API 호출을 수행 하는 ISV를 식별 하는 고유한 액세스 토큰입니다. 형식은 `"Bearer <access_token>"` 에 설명 된 대로 게시자가 토큰 값을 검색 하는 경우입니다. <br> <ul> <li> 에서 SaaS는 [HTTP POST를 사용 하 여 토큰을 가져옵니다](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> [인증 전략](./marketplace-metering-service-authentication.md)의 관리 되는 응용 프로그램 </li> </ul> |
| | |

*요청 본문 예:*

```json
{
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. 
  "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, from now and until 24 hours back
  "planId": "plan1", // id of the plan purchased for the offer
}
```

>[!NOTE]
>`resourceId` SaaS 앱과 관리 되는 앱 내보내기 사용자 지정 측정기에 대 한 의미가 다릅니다. 

Azure 애플리케이션 관리 앱 계획의 경우 `resourceId`는 관리 앱 메타데이터 개체의 `billingDetails` 아래에 있는 `resourceUsageId`입니다. 이를 인출하는 예제 스크립트는 [Azure 관리 ID 토큰 사용](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)에서 찾을 수 있습니다. 

SaaS 제품의 경우 `resourceId`는 SaaS 구독 ID입니다. SaaS 구독에 대한 자세한 내용은 [구독 나열](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions)을 참조하세요.

### <a name="responses"></a>응답

코드: 200<br>
OK. 추가 처리 및 청구를 위해 사용 현황 내보내기가 Microsoft 쪽에 승인 되 고 기록 되었습니다.

응답 페이로드 예: 

```json
{
  "usageEventId": <guid>, // unique identifier associated with the usage event in Microsoft records
  "status": "Accepted" // this is the only value in case of single usage event
  "messageTime": "2020-01-12T13:19:35.3458658Z", // time in UTC this event was accepted
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. For SaaS it's the subscriptionId.
  "quantity": 5.0, // amount of emitted units as recorded by Microsoft
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, as sent by the ISV
  "planId": "plan1", // id of the plan purchased for the offer
}
```

코드: 400 <br>
잘못된 요청입니다.

* 제공 된 요청 데이터가 없거나 잘못 되었습니다.
* `effectiveStartTime` 는 지난 24 시간 이상입니다. 이벤트가 만료 되었습니다.
* SaaS 구독이 구독 상태에 있지 않습니다.

응답 페이로드 예: 

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

코드: 403<br>

사용할 수 없습니다. 권한 부여 토큰이 제공 되지 않았거나 잘못 되었거나 만료 되었습니다.  또는 권한 부여 토큰을 만드는 데 사용 된 것과 다른 Azure AD 앱 ID로 게시 된 제품에 대 한 구독 액세스를 요청 하는 중입니다.

코드: 409<br>
충돌. 지정 된 리소스 ID, 유효 사용 날짜 및 시간에 대 한 사용 이벤트가 이미 보고 되었습니다.

응답 페이로드 예: 

```json
{
  "additionalInfo": {
    "acceptedMessage": {
      "usageEventId": "<guid>", //unique identifier associated with the usage event in Microsoft records
      "status": "Duplicate",
      "messageTime": "2020-01-12T13:19:35.3458658Z",
      "resourceId": "<guid>", //unique identifier of the resource against which usage is emitted.
      "quantity": 1.0,
      "dimension": "dim1",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "plan1"
    }
  },
  "message": "This usage event already exist.",
  "code": "Conflict"
}
```

## <a name="metered-billing-batch-usage-event"></a>요금제 청구 일괄 처리 사용 이벤트

Batch 사용 이벤트 API를 사용 하면 둘 이상의 구매한 리소스에 대 한 사용 이벤트를 한 번에 내보낼 수 있습니다. 또한 서로 다른 일정 시간에 대해 동일한 리소스에 대 한 여러 사용 이벤트를 내보낼 수 있습니다. 단일 일괄 처리에서 최대 이벤트 수는 25 개입니다.

**POST:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*쿼리 매개 변수:*

| 매개 변수  | 권장     |
| ---------- | -------------------- |
| `ApiVersion` | 2018-08-31을 사용 합니다. |

*요청 헤더:*

| Content-type       | `application/json` 사용       |
| ------------------ | ------ |
| `x-ms-requestid`     | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `x-ms-correlationid` | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `authorization`      | 이 API 호출을 수행 하는 ISV를 식별 하는 고유한 액세스 토큰입니다. 형식은 `Bearer <access_token>` 에 설명 된 대로 게시자가 토큰 값을 검색 하는 경우입니다. <br> <ul> <li> 에서 SaaS는 [HTTP POST를 사용 하 여 토큰을 가져옵니다](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> [인증 전략](./marketplace-metering-service-authentication.md)의 관리 되는 응용 프로그램 </li> </ul> |
| | |


*요청 본문 예:*

```json
{
  "request": [ // list of usage events for the same or different resources of the publisher
    { // first event
      "resourceId": "<guid1>", // Unique identifier of the resource against which usage is emitted. 
      "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
      "dimension": "dim1", //Custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",//Time in UTC when the usage event occurred, from now and until 24 hours back
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // next event
      "resourceId": "<guid2>", 
      "quantity": 39.0, 
      "dimension": "email", 
      "effectiveStartTime": "2018-11-01T23:33:10
      "planId": "gold", // id of the plan purchased for the offer
    }
  ]
}
```

>[!NOTE]
>`resourceId` SaaS 앱과 관리 되는 앱 내보내기 사용자 지정 측정기에 대 한 의미가 다릅니다. 

Azure 애플리케이션 관리 앱 계획의 경우 `resourceId`는 관리 앱 메타데이터 개체의 `billingDetails` 아래에 있는 `resourceUsageId`입니다. 이를 인출하는 예제 스크립트는 [Azure 관리 ID 토큰 사용](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)에서 찾을 수 있습니다. 

SaaS 제품의 경우 `resourceId`는 SaaS 구독 ID입니다. SaaS 구독에 대한 자세한 내용은 [구독 나열](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions)을 참조하세요.

### <a name="responses"></a>응답

코드: 200<br>
OK. 일괄 처리 사용 현황 내보내기가 추가 처리 및 청구를 위해 Microsoft 쪽에 승인 되 고 기록 되었습니다. 응답 목록은 일괄 처리의 각 개별 이벤트에 대 한 상태와 함께 반환 됩니다. 일괄 처리 이벤트의 일부로 전송 된 각 개별 사용 이벤트에 대 한 응답을 이해 하려면 응답 페이로드를 반복 해야 합니다.

응답 페이로드 예: 

```json
{
  "count": 2, // number of records in the response
  "result": [
    { // first response
      "usageEventId": "<guid>", // unique identifier associated with the usage event in Microsoft records
      "status": "Accepted" // see list of possible statuses below,
      "messageTime": "2020-01-12T13:19:35.3458658Z", // Time in UTC this event was accepted by Microsoft,
      "resourceId": "<guid1>", // unique identifier of the resource against which usage is emitted.
      "quantity": 5.0, // amount of emitted units as recorded by Microsoft 
      "dimension": "dim1", // custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",// time in UTC when the usage event occurred, as sent by the ISV
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // second response
      "status": "Duplicate",
      "messageTime": "0001-01-01T00:00:00",
      "error": {
        "additionalInfo": {
          "acceptedMessage": {
            "usageEventId": "<guid>",
            "status": "Duplicate",
            "messageTime": "2020-01-12T13:19:35.3458658Z",
            "resourceId": "<guid2>",
            "quantity": 1.0,
            "dimension": "email",
            "effectiveStartTime": "2020-01-12T11:03:28.14Z",
            "planId": "gold"
          }
        },
        "message": "This usage event already exist.",
        "code": "Conflict"
      },
      "resourceId": "<guid2>",
      "quantity": 1.0,
      "dimension": "email",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "gold"
    }
  ]
}
```

`BatchUsageEvent` API 응답에서 참조되는 상태 코드에 대한 설명:

| 상태 코드  | Description |
| ---------- | -------------------- |
| `Accepted` | 수락됨 |
| `Expired` | 만료된 사용 |
| `Duplicate` | 중복된 사용량이 제공되었습니다. |
| `Error` | 오류 코드 |
| `ResourceNotFound` | 제공된 사용량 리소스가 잘못되었습니다. |
| `ResourceNotAuthorized` | 이 리소스에 대한 사용량을 제공할 수 있는 권한이 없습니다. |
| `InvalidDimension` | 사용량이 전달된 차원이 이 제품/계획에 적합하지 않습니다. |
| `InvalidQuantity` | 전달 된 수량이 0 보다 작거나 같습니다. |
| `BadArgument` | 입력이 없거나 형식이 잘못되었습니다. |

코드: 400<br>
잘못된 요청입니다. 일괄 처리에 사용 이벤트가 25 개 이상 포함 되어 있습니다.

코드: 403<br>
사용할 수 없습니다. 권한 부여 토큰이 제공 되지 않았거나 잘못 되었거나 만료 되었습니다.  또는 권한 부여 토큰을 만드는 데 사용 된 것과 다른 Azure AD 앱 ID로 게시 된 제품에 대 한 구독 액세스를 요청 하는 중입니다.

## <a name="development-and-testing-best-practices"></a>개발 및 테스트 모범 사례

사용자 지정 측정기 내보내기를 테스트 하려면 계량 API와의 통합을 구현 하 고, 단위당 가격이 0 인 정의 된 사용자 지정 차원이 포함 된 게시 된 SaaS 제품에 대 한 계획을 만듭니다. 그리고 제한 된 사용자만 통합에 액세스 하 고 테스트할 수 있도록이 제품을 미리 보기로 게시 합니다.

또한 기존 라이브 제품에 대해 비공개 요금제를 사용 하 여 테스트 중에이 계획에 대 한 액세스를 제한 된 대상으로 제한할 수 있습니다.

## <a name="get-support"></a>지원 받기

[파트너 센터에서 상업용 marketplace 프로그램 지원](../support.md) 의 지침에 따라 게시자 지원 옵션을 이해 하 고 Microsoft에서 지원 티켓을 엽니다.

## <a name="next-steps"></a>다음 단계

계량 서비스 Api에 대 한 자세한 내용은 [Marketplace 계량 서비스 API FAQ](./marketplace-metering-service-apis-faq.md)를 참조 하세요.
