---
title: 계량 서비스 API - Microsoft 상업용 마켓플레이스
description: 사용량 이벤트 API를 사용하여 Microsoft AppSource 및 Azure Marketplace에서 SaaS 제품에 대한 사용량 이벤트를 내보낼 수 있습니다.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 95eba648219413923ce27d433a5236877c4953f3
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725468"
---
# <a name="marketplace-metering-service-apis"></a>Marketplace 계량 서비스 API

사용량 이벤트 API를 사용하면 구매한 특정 엔터티에 대한 사용량 이벤트를 내보낼 수 있습니다. 사용량 이벤트 요청은 제품을 게시할 때 게시자에서 정의한 계량 서비스 차원을 참조합니다.

## <a name="usage-event"></a>사용량 이벤트

**POST**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | 이 요청에 사용할 작업의 버전입니다. 최신 API 버전은 2018-08-31입니다. |

*요청 헤더:*

| Content-type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `x-ms-correlationid` | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `authorization`   | [JWT(JSON Web Token) 전달자 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 참고: HTTP 요청을 만들 때 참조된 링크에서 가져온 토큰에 `Bearer` 접두사를 붙입니다. |

>[!Note]
>Azure 애플리케이션 관리 앱 계획의 경우 `resourceId`는 관리 앱 메타데이터 개체의 `billingDetails` 아래에 있는 `resourceUsageId`입니다.  이를 인출하는 예제 스크립트는 [Azure 관리 ID 토큰 사용](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)에서 찾을 수 있습니다.  SaaS 제품의 경우 `resourceId`는 SaaS 구독 ID입니다.  SaaS 구독에 대한 자세한 내용은 [구독 나열](./pc-saas-fulfillment-api-v2.md#list-subscriptions)을 참조하세요.

*요청:*

```json
{
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

### <a name="responses"></a>응답

코드: 200<br>
확인 

```json
{
  "usageEventId": "Unique identifier associated with the usage event",
  "status": "Accepted",
  "messageTime": "Time this message was created in UTC",
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

코드: 400 <br>
제공되거나 만료된 잘못된 요청, 누락 또는 잘못된 데이터

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
제공되거나 만료된 잘못된 요청, 누락 또는 잘못된 데이터

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

코드: 409<br>
충돌, 사용량 리소스 ID에 대한 사용량 호출 및 이미 존재하는 유효 사용량을 받는 경우 응답에는 허용된 메시지에 대한 정보를 포함하는 `additionalInfo` 필드가 포함됩니다.

```json
{
  "code": "Conflict",
  "additionalInfo": {
    "usageEventId": "Unique identifier associated with the usage event",
    "status": "Accepted|NotProcessed|Expired",
    "messageTime": "Time this message was created in UTC",
    "resourceId": "Identifier of the resource against which usage is emitted",
    "quantity": 5.0,
    "dimension": "Dimension identifier",
    "effectiveStartTime": "Time in UTC when the usage event occurred",
    "planId": "Plan associated with the purchased offer"
  }
}
```

## <a name="batch-usage-event"></a>일괄 처리 사용량 이벤트

일괄 처리 사용량 이벤트 API를 사용하면 둘 이상의 구매한 특정 엔터티에 대한 사용량 이벤트를 한 번에 내보낼 수 있습니다. 일괄 처리 사용량 이벤트 요청은 제품을 게시할 때 게시자에서 정의한 계량 서비스 차원을 참조합니다.

>[!Note]
>Microsoft의 상업용 마켓플레이스에서 여러 SaaS 제품을 등록할 수 있습니다. 등록된 각 SaaS 제품에는 인증 및 권한 부여를 위해 등록된 고유한 Azure AD 애플리케이션이 있습니다. 일괄 처리에서 내보낸 이벤트는 제품을 등록할 때 동일한 Azure AD 애플리케이션을 사용하는 제품에 속해야 합니다.

**POST:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | 이 요청에 사용할 작업의 버전입니다. 최신 API 버전은 2018-08-31입니다. |

*요청 헤더:*

| Content-type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `x-ms-correlationid` | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `authorization`      | [JWT(JSON Web Token) 전달자 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 참고: HTTP 요청을 만들 때 참조된 링크에서 가져온 토큰에 `Bearer` 접두사를 붙입니다.  |

*요청:*
```json
{
  "request": [
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    },
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    }
  ]
}
```
### <a name="responses"></a>응답

코드: 200<br>
확인

```json
{
  "count": 2,
  "result": [
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    },
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    }
  ]
}
```

`BatchUsageEvent` API 응답에서 참조되는 상태 코드에 대한 설명:

| 상태 코드  | Description |
| ---------- | -------------------- |
| `Accepted` | 승인된 코드 |
| `Expired` | 만료된 사용 |
| `Duplicate` | 중복된 사용량이 제공되었습니다. |
| `Error` | 오류 코드 |
| `ResourceNotFound` | 제공된 사용량 리소스가 잘못되었습니다. |
| `ResourceNotAuthorized` | 이 리소스에 대한 사용량을 제공할 수 있는 권한이 없습니다. |
| `InvalidDimension` | 사용량이 전달된 차원이 이 제품/계획에 적합하지 않습니다. |
| `InvalidQuantity` | 전달된 수량은 <0입니다. |
| `BadArgument` | 입력이 없거나 형식이 잘못되었습니다. |

코드: 400<br>
제공되거나 만료된 잘못된 요청, 누락 또는 잘못된 데이터

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "Invalid data format.",
      "target": "usageEventRequest",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```
코드: 403<br>
사용자가 이 호출을 수행할 권한이 없습니다.

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [SaaS 요금 청구](./saas-metered-billing.md)를 참조하세요.
