---
title: 마켓플레이스 계량 서비스 API | Azure 마켓플레이스
description: Azure 마켓플레이스에서 제공하는 SaaS용 사용 이벤트입니다.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 315f36e5aed9dee0a89e1f9f504b18a6bed806e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275750"
---
# <a name="marketplace-metering-service-apis"></a>Marketplace 계량 서비스 API

사용 이벤트 API를 사용하면 구매한 특정 엔터티에 대한 사용 이벤트를 내보사할 수 있습니다. 사용 이벤트 요청은 퍼블리셔가 쿠폰을 게시할 때 정의한 계량 서비스 차원을 참조합니다.

## <a name="usage-event"></a>이용 이벤트

**게시물**:`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | 이 요청에 사용할 작업의 버전입니다. 최신 API 버전은 2018-08-31입니다. |

*헤더 요청:*

| Content-type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | 클라이언트에서 요청을 추적하기 위한 고유 문자열 값(가급적 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `x-ms-correlationid` | 클라이언트에서 작업에 대한 고유 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트와 서버 측의 이벤트의 상관 관계를 지정합니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다. |
| `authorization`   | [JSON 웹 토큰 (JWT) 베어러 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 참고: HTTP 요청을 할 때 `Bearer` 참조된 링크에서 얻은 토큰접두사를 만듭니다. |

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
잘못된 요청, 누락되었거나 잘못된 데이터 제공 또는 만료됨

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
잘못된 요청, 누락되었거나 잘못된 데이터 제공 또는 만료됨

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

코드: 409<br>
충돌, 사용 리소스 ID에 대 한 사용 호출을 받을 때, 그리고 효과적인 사용, 이미 존재 하는. 응답에는 수락된 메시지에 대한 정보가 포함된 필드가 포함됩니다. `additionalInfo`

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

## <a name="batch-usage-event"></a>일괄 사용 이벤트

일괄 처리 사용 이벤트 API를 사용하면 두 개 이상의 구매한 엔터티에 대해 한 번에 사용 이벤트를 내보사할 수 있습니다. 일괄 처리 사용 이벤트 요청은 퍼블리셔가 오퍼를 게시할 때 정의한 계량 서비스 차원을 참조합니다.

>[!Note]
>Microsoft의 상용 시장에서 여러 SaaS 오퍼를 등록할 수 있습니다. 등록된 각 SaaS 제품에는 인증 및 권한 부여목적으로 등록된 고유한 Azure AD 응용 프로그램이 있습니다. 일괄 처리로 내보낸 이벤트는 오퍼를 등록할 때 동일한 Azure AD 응용 프로그램을 사용하여 제공에 속해야 합니다.

**게시물:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | 이 요청에 사용할 작업의 버전입니다. 최신 API 버전은 2018-08-31입니다. |

*헤더 요청:*

| Content-type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | 클라이언트에서 요청을 추적하기 위한 고유 문자열 값(가급적 GUID)입니다. 이 값을 제공하지 않으면 응답 헤더에 생성되고 제공됩니다. |
| `x-ms-correlationid` | 클라이언트에서 작업에 대한 고유 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트와 서버 측의 이벤트의 상관 관계를 지정합니다. 이 값이 제공되지 않으면 하나가 생성되고 응답 헤더에 제공됩니다. |
| `authorization`      | [JSON 웹 토큰 (JWT) 베어러 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 참고: HTTP 요청을 할 때 `Bearer` 참조된 링크에서 얻은 토큰접두사를 만듭니다.  |

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

API 응답에서 참조되는 `BatchUsageEvent` 상태 코드에 대한 설명:

| 상태 코드  | 설명 |
| ---------- | -------------------- |
| `Accepted` | 허용된 코드입니다. |
| `Expired` | 사용이 만료되었습니다. |
| `Duplicate` | 중복 사용제공. |
| `Error` | 오류 코드 |
| `ResourceNotFound` | 제공된 사용 리소스가 잘못되었습니다. |
| `ResourceNotAuthorized` | 이 리소스에 대한 사용을 제공할 권한이 없습니다. |
| `InvalidDimension` | 사용이 전달되는 차원은 이 오퍼/플랜에 대해 유효하지 않습니다. |
| `InvalidQuantity` | 전달된 수량은 < 0입니다. |
| `BadArgument` | 입력이 없거나 잘못된 형식입니다. |

코드: 400<br>
잘못된 요청, 누락되었거나 잘못된 데이터 제공 또는 만료됨

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
사용자가 이 전화를 걸 수 있는 권한이 없습니다.

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [SaaS 계량 청구를](./saas-metered-billing.md)참조하십시오.
