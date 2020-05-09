---
title: 계량 서비스 Api-Microsoft 상업적 marketplace
description: 사용 이벤트 API를 사용 하 여 Microsoft AppSource 및 Azure Marketplace에서 SaaS 제품에 대 한 사용 이벤트를 내보낼 수 있습니다.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 159d2c60fc1fc5ad1f21f2b948208eaae0d06208
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857859"
---
# <a name="marketplace-metering-service-apis"></a>Marketplace 계량 서비스 API

사용 이벤트 API를 사용 하면 구매한 특정 엔터티에 대 한 사용 이벤트를 내보낼 수 있습니다. 사용 이벤트 요청은 제품을 게시할 때 게시자에서 정의한 계량 서비스 차원을 참조 합니다.

## <a name="usage-event"></a>사용 이벤트

**POST**:`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | 이 요청에 사용할 작업의 버전입니다. 최신 API 버전은 2018-08-31입니다. |

*요청 헤더:*

| Content-type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | 클라이언트의 요청을 추적 하기 위한 고유 문자열 값 (가급적 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `x-ms-correlationid` | 클라이언트에서 작업에 대 한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상관 관계를 설정 합니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다. |
| `authorization`   | [JWT (JSON web token) 전달자 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 참고: HTTP 요청을 만들 때 참조 된 `Bearer` 링크에서 가져온 토큰에 접두사를 붙입니다. |

*요구*

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
제공 되거나 만료 된 잘못 된 요청, 누락 또는 잘못 된 데이터

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
제공 되거나 만료 된 잘못 된 요청, 누락 또는 잘못 된 데이터

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

코드: 409<br>
충돌, 사용 리소스 ID에 대 한 사용 호출 및 이미 존재 하는 유효 사용을 받게 됩니다. 응답에는 허용 `additionalInfo` 된 메시지에 대 한 정보가 포함 된 필드가 포함 됩니다.

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

## <a name="batch-usage-event"></a>일괄 처리 사용 이벤트

Batch 사용 이벤트 API를 사용 하면 둘 이상의 구매한 엔터티에 대 한 사용 이벤트를 한 번에 내보낼 수 있습니다. Batch 사용 이벤트 요청은 제품을 게시할 때 게시자가 정의한 계량 서비스 차원을 참조 합니다.

>[!Note]
>Microsoft의 상용 marketplace에서 여러 SaaS 제품을 등록할 수 있습니다. 등록 된 각 SaaS 제품에는 인증 및 권한 부여를 위해 등록 된 고유한 Azure AD 응용 프로그램이 있습니다. Batch에서 내보낸 이벤트는 제품을 등록할 때 동일한 Azure AD 응용 프로그램을 사용 하는 제품에 속해야 합니다.

**POST:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | 이 요청에 사용할 작업의 버전입니다. 최신 API 버전은 2018-08-31입니다. |

*요청 헤더:*

| Content-type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | 클라이언트의 요청을 추적 하기 위한 고유 문자열 값 (가급적 GUID)입니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다. |
| `x-ms-correlationid` | 클라이언트에서 작업에 대 한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상관 관계를 설정 합니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다. |
| `authorization`      | [JWT (JSON web token) 전달자 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 참고: HTTP 요청을 만들 때 참조 된 `Bearer` 링크에서 가져온 토큰에 접두사를 붙입니다.  |

*요구*
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

API 응답에서 참조 되는 `BatchUsageEvent` 상태 코드에 대 한 설명:

| 상태 코드  | Description |
| ---------- | -------------------- |
| `Accepted` | 허용 된 코드입니다. |
| `Expired` | 만료 된 사용입니다. |
| `Duplicate` | 중복 된 사용법이 제공 되었습니다. |
| `Error` | 오류 코드 |
| `ResourceNotFound` | 제공 된 사용 리소스가 잘못 되었습니다. |
| `ResourceNotAuthorized` | 이 리소스에 대 한 사용량을 제공할 수 있는 권한이 없습니다. |
| `InvalidDimension` | 사용량이 전달 된 차원이이 제안/계획에 적합 하지 않습니다. |
| `InvalidQuantity` | 전달 된 수량은 0 <입니다. |
| `BadArgument` | 입력이 없거나 형식이 잘못 되었습니다. |

코드: 400<br>
제공 되거나 만료 된 잘못 된 요청, 누락 또는 잘못 된 데이터

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
사용자가이 호출을 수행할 권한이 없습니다.

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [SaaS 요금제](./saas-metered-billing.md)를 참조 하세요.
