---
title: SaaS Fulfillment API v2 | Azure Marketplace
description: 만들기 및 연결 된 처리를 사용 하 여 Azure Marketplace 및 AppSource에 SaaS 제품을 관리 하는 방법과 v2 Api.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: da23b90e44869dcbd21acf9b2c4e04f30153ae09
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66751770"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS Fulfillment Api 버전 2 

Azure Marketplace 및 AppSource에서 독립 소프트웨어 공급 업체 (Isv) SaaS 응용 프로그램을 판매할 수 있도록 API 자세히 설명 하는 문서 합니다. 이 API는 Azure Marketplace 및 AppSource transactable SaaS에 대 한 요구 사항을 제공 합니다.

## <a name="managing-the-saas-subscription-lifecycle"></a>SaaS 구독 수명 주기 관리

Microsoft SaaS 서비스 SaaS 구독 구매의 전체 수명 주기를 관리 하 고 실제 처리를 구동 하는 메커니즘을 계획 및 ISV 사용 하 여 구독의 삭제 변경 처리 API를 사용 합니다. 고객이는 Microsoft에서 유지 관리 하는 SaaS 구독의 상태를 기반으로 하는 요금이 청구 됩니다. 다음 다이어그램에서는 상태와 상태 간의 변경 내용의 구동 하는 작업을 보여 줍니다.

![SaaS 구독 수명 주기 상태](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>SaaS 구독 상태

다음 표에서 해당 하는 경우 각각에 대 한 설명 및 시퀀스 다이어그램을 포함 하 여 SaaS 구독에 대 한 프로 비전 상태를 나열 합니다. 

#### <a name="provisioning"></a>프로비전

고객이 구매를 시작, ISV URL 매개 변수를 사용 하 여 고객 대화형 웹 페이지에서 인증 코드를이 정보를 받습니다. 예를 들어: `https://contoso.com/signup?token=..`여기서는 파트너 센터에서 방문 페이지 URL 공급자 `https://contoso.com/signup`합니다. 인증 코드의 유효성을 검사 하 고 해결 API를 호출 하 여 프로 비전 하는 데 필요한 세부 정보에 대 한 교환할 수 있습니다.  SaaS 서비스 프로 비전 완료 되 면 활성화 호출을 처리 완료 되 고 고객이 대금을 청구할 수는 신호를 보냅니다.  다음 다이어그램은 프로 비전 시나리오에 대 한 API 호출의 순서를 보여줍니다.  

![SaaS 서비스를 프로 비전을 위한 API를 호출 합니다.](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>프로비전됨

이 상태는 프로 비전 된 서비스의 정상 상태입니다.

#### <a name="provisioning-for-update"></a>업데이트에 대 한 프로 비전
(marketplace)에서 

이 상태는 기존 업데이트를 나타내는 서비스 보류 중입니다. (고객 트랜잭션 직접.)에 해당 SaaS 서비스 또는 marketplace에 고객이 이러한 업데이트를 시작할 수 있습니다. 다음 다이어그램은 marketplace에서 업데이트가 시작 되 면 작업을 보여 줍니다.

![업데이트는 marketplace에서 시작 되 면 API를 호출 합니다.](./media/saas-update-api-v2-calls-from-marketplace-a.png)

#### <a name="provisioning-for-update"></a>업데이트에 대 한 프로 비전  
SaaS 서비스에서 

다음 다이어그램은 SaaS 서비스에 의해 업데이트가 시작 되 면 작업을 보여 줍니다. (웹 후크 호출 업데이트 SaaS 서비스에 의해 시작 된 구독에 의해 대체 됩니다. 

![SaaS 서비스에서 업데이트를 시작 하는 경우 API를 호출 합니다.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspended

이 상태는 고객의 지불 수신 되지 않았습니다 나타냅니다. 정책에 의해 우리 고객 구독 unfulfilling 전에 유예 기간이 제공 합니다. 구독 중인 경우이 상태: 

- Isv에서는 저하 또는 서비스에 대 한 사용자의 액세스를 차단할 수도 있습니다. 
- 구독 설정 데이터의 손실 없이 전체 기능을 복원할 수 있는 복구 가능한 상태 유지 되어야 합니다. 
- 유예 기간이 끝날 때 처리 API 통해이 구독에 대 한 복원 요청 또는 프로 비전 해제 요청을 가져오려는 예상할 수 있습니다. 

#### <a name="unsubscribed"></a>구독 취소 

구독 미납으로 인해의 응답으로 또는 명시적 고객 요청에 대 한 응답으로이 상태에 도달 합니다. ISV에서 기대 하는 경우 고객의 데이터가 최소 X 일에 대 한 요청에 대 한 복구를 위해 보관 및 삭제 한 다음 


## <a name="api-reference"></a>API 참조

이 섹션에서는 설명 SaaS *구독 API* 하 고 *Operations API*합니다.  값을 `api-version` 버전 2에 대 한 매개 변수 Api는 `2018-08-31`합니다.  


### <a name="parameter-and-entity-definitions"></a>매개 변수 및 엔터티 정의

다음 표에서 일반적인 매개 변수 및 Fulfillment Api에서 사용 하는 엔터티에 대 한 정의 나열 합니다.

|     엔터티/매개 변수     |     정의                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | SaaS 리소스에 대 한 GUID 식별자  |
| `name`                   | 고객이 제공한이 리소스에 대 한 이름 |
| `publisherId`            | 예를 들어 "contoso" 각 게시자에 대 한 고유한 문자열 식별자 |
| `offerId`                | 예를 들어 "offer1" 각 제품에 대 한 고유한 문자열 식별자  |
| `planId`                 | 각 계획/sku에 대 한 예를 들어 "silver" 고유 문자열 식별자 |
| `operationId`            | 특정 작업에 대 한 GUID 식별자  |
|  `action`                | 하거나 리소스에서 수행 되는 작업 `subscribe`, `unsubscribe`를 `suspend`합니다 `reinstate`, 또는 `changePlan`, `changeQuantity`, `transfer`  |
|   |   |

전역적으로 고유 식별자 ([Guid](https://en.wikipedia.org/wiki/Universally_unique_identifier))는 일반적으로 자동으로 생성 된 128 비트 (32 16 진수) 숫자입니다. 

#### <a name="resolve-a-subscription"></a>구독 확인 

확인 끝점에 영구 리소스 id입니다. marketplace 토큰을 확인 하려면 게시자를 사용 하도록 설정 리소스 ID는 SAAS 구독의 고유 식별자입니다.  사용자가 ISV의 웹 사이트로 리디렉션되면 URL에 쿼리 매개 변수의 토큰이 포함됩니다. ISV는 이 토큰을 사용하고, 이를 확인하는 요청을 수행해야 합니다. 응답에는 고유한 SAAS 구독 ID, 이름, 제품 ID 및 리소스 계획이 포함됩니다. 이 토큰은 한 시간 동안만 유효합니다. 

**Post:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전  |

*요청 헤더:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      | `application/json` |
|  x-ms-requestid    |  가급적 GUID 클라이언트에서 요청을 추적 하는 것에 대 한 고유한 문자열 값입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  x-ms-correlationid |  클라이언트에서 작업에 대 한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  권한 부여     |  [JSON 웹 토큰 (JWT) 전달자 토큰 가져오기](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) |
|  x-ms-marketplace-token  |  Azure에서 SaaS ISV의 웹 사이트에 사용자가 리디렉션될 경우 URL에 토큰 쿼리 매개 변수 (예: `https://contoso.com/signup?token=..`). *참고:* URL을 사용 하기 전에 브라우저에서 토큰 값을 디코딩합니다.  |

*응답 코드:*

코드: 200<br>
SaaS 구독으로 불투명 토큰을 확인합니다.<br>

```json
Response body:
{
    "subscriptionId": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

코드: 404<br>
찾을 수 없음

코드: 400<br>
잘못된 요청입니다. x-ms-marketplace-토큰이 없거나 형식이 잘못 되었거나 만료 된 경우

코드: 403<br>
권한이 없습니다. 인증 토큰 입력 하지 않았으므로 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.

코드: 500<br>
내부 서버 오류

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>구독 API

API 구독에는 다음 HTTPS 작업을 지원합니다. **가져올**, **Post**를 **패치**, 및 **삭제**합니다.


#### <a name="list-subscriptions"></a>구독 나열

게시자에 대 한 모든 SaaS 구독을 나열합니다.

**가져오기:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`**

*쿼리 매개 변수:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  이 요청에 사용할 작업의 버전입니다.  |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
| 콘텐츠 형식       |  `application/json`  |
| x-ms-requestid     |  가급적 GUID 클라이언트에서 요청을 추적 하는 것에 대 한 고유한 문자열 값입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| x-ms-correlationid |  클라이언트에서 작업에 대 한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.  |
| 권한 부여      |  [JSON 웹 토큰 (JWT) 전달자를 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*응답 코드:*

코드: 200 <br/>
인증 토큰을 기준, 게시자 및 게시자의 모든 제품에 대 한 해당 구독을 얻게 됩니다.<br> 응답 페이로드:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

연속 토큰 추가 "페이지"를 검색 하는 계획의 경우에 있게 됩니다. 

코드: 403 <br>
권한이 없습니다. 인증 토큰 입력 하지 않았으므로 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다. 

코드: 500 내부 서버 오류

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>구독 가져오기

지정 된 SaaS 구독을 가져옵니다. 이 호출을 사용 하 여 계획 정보 및 라이선스 정보를 제공 합니다.

**가져오기:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`**

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   해결 API를 통해 토큰을 해결 한 후에 얻은 SaaS 구독의 고유 식별자   |
|  ApiVersion        |   이 요청에 사용할 작업의 버전   |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      |  `application/json`  |
|  x-ms-requestid    |  가급적 GUID 클라이언트에서 요청을 추적 하는 것에 대 한 고유한 문자열 값입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  x-ms-correlationid |  클라이언트에서 작업에 대 한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.  |
|  권한 부여     |  [JSON 웹 토큰 (JWT) 전달자를 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*응답 코드:*

코드: 200<br>
SaaS 구독을 식별자를 가져옵니다.<br> 응답 페이로드:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

코드: 404<br>
찾을 수 없음<br> 

코드: 403<br>
권한이 없습니다. 인증 토큰 입력 하지 않았으므로 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.

코드: 500<br>
내부 서버 오류<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>사용 가능한 계획 목록

현재 게시자에 대 한 개인/공개 오퍼 있는지 확인 하려면이 호출을 사용 합니다.

**가져오기:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`**

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   이 요청에 사용할 작업의 버전  |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|   콘텐츠 형식     |  `application/json` |
|   x-ms-requestid   |   가급적 GUID 클라이언트에서 요청을 추적 하는 것에 대 한 고유한 문자열 값입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  x-ms-correlationid  | 클라이언트에서 작업에 대 한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  권한 부여     |  [JSON 웹 토큰 (JWT) 전달자를 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*응답 코드:*

코드: 200<br>
고객에 대 한 사용 가능한 계획의 목록을 가져옵니다.<br>

응답 본문:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

코드: 404<br>
찾을 수 없음<br> 

코드: 403<br>
권한이 없습니다. 인증 토큰 입력 하지 않았으므로 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다. <br> 

코드: 500<br>
내부 서버 오류<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>구독 활성화

**Post:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`**

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전  |
| subscriptionId     | 해결 API를 사용 하 여 토큰을 해결 한 후에 얻은 SaaS 구독의 고유 식별자  |

*요청 헤더:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      | `application/json`  |
|  x-ms-requestid    | 가급적 GUID 클라이언트에서 요청을 추적 하는 것에 대 한 고유한 문자열 값입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  x-ms-correlationid  | 클라이언트에서 작업에 대 한 고유한 문자열 값입니다. 이 문자열에서 서버 쪽 이벤트를 사용 하 여 클라이언트 작업의 모든 이벤트를 상호 연결합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.  |
|  권한 부여     |  [JSON 웹 토큰 (JWT) 전달자를 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*요청:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*응답 코드:*

코드: 200<br>
구독을 활성화합니다.<br>

코드: 404<br>
찾을 수 없음

코드: 400<br>
잘못 된 요청 유효성 검사 오류

코드: 403<br>
권한이 없습니다. 인증 토큰 입력 하지 않았으므로 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.

코드: 500<br>
내부 서버 오류

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>구독에서 계획 변경

구독 계획을 업데이트 합니다.

**Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | 해결 API를 사용 하 여 토큰을 해결 한 후에 얻은 SaaS 구독의 고유 식별자입니다.  |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      | `application/json` |
|  x-ms-requestid    |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  x-ms-correlationid  |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.    |
| 권한 부여      |  [JSON 웹 토큰 (JWT) 전달자를 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*요청 페이로드:*

```json
Request Body:
{
    "planId": "gold"
}
```

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 작업의 상태를 가져오려면 리소스에 연결 합니다.   |

*응답 코드:*

코드: 202<br>
계획을 변경 하는 요청이 수락 되었습니다. ISV는 성공/실패를 확인 하려면 작업 위치를 폴링 하려면 필요 합니다. <br>

코드: 404<br>
찾을 수 없음

코드: 400<br>
잘못 된 요청 유효성 검사 오류입니다.

>[!Note]
>한 번에 패치가 적용 된, 둘 다가 아닌 계획 또는 수량만 될 수 있습니다. 사용 하 여 구독에서 편집 **업데이트** 에 없는 `allowedCustomerOperations`합니다.

코드: 403<br>
권한이 없습니다. 인증 토큰 입력 하지 않았으므로 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.

코드: 500<br>
내부 서버 오류

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-quantity-on-the-subscription"></a>구독에 수량 변경

구독에 수량을 업데이트 합니다.

**Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | 해결 API를 사용 하 여 토큰을 해결 한 후에 얻은 SaaS 구독의 고유 식별자입니다.  |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      | `application/json` |
|  x-ms-requestid    |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  x-ms-correlationid  |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.    |
| 권한 부여      |  [JSON 웹 토큰 (JWT) 전달자를 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*요청 페이로드:*

```json
Request Body:
{
    "quantity": 5
}
```

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 작업의 상태를 가져오려면 리소스에 연결 합니다.   |

*응답 코드:*

코드: 202<br>
수락됨 수량을 변경 하는 요청이 수락 되었습니다. ISV는 성공/실패를 확인 하려면 작업 위치를 폴링 하려면 필요 합니다. <br>

코드: 404<br>
찾을 수 없음

코드: 400<br>
잘못 된 요청 유효성 검사 오류입니다.

>[!Note]
>한 번에 패치가 적용 된, 둘 다가 아닌 계획 또는 수량만 될 수 있습니다. 사용 하 여 구독에서 편집 **업데이트** 에 없는 `allowedCustomerOperations`합니다.

코드: 403<br>
권한이 없습니다. 인증 토큰 입력 하지 않았으므로 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.

코드: 500<br>
내부 서버 오류

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="delete-a-subscription"></a>구독 삭제

구독을 취소 하 고 지정된 된 구독을 삭제 합니다.

**삭제:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`**

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | 해결 API를 사용 하 여 토큰을 해결 한 후에 얻은 SaaS 구독의 고유 식별자입니다.  |

*요청 헤더:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   콘텐츠 형식     |  `application/json` |
|  x-ms-requestid    |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.   |
|  x-ms-correlationid  |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.   |
|  권한 부여     |  [JSON 웹 토큰 (JWT) 전달자를 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*응답 코드:*

코드: 202<br>
나타내기 위해 시작 하는 ISV 호출 SaaS 구독에서 구독을 취소 합니다.<br>

코드: 404<br>
찾을 수 없음

코드: 400<br>
구독을 삭제 **삭제할** 나타나지 `allowedCustomerOperations`합니다.

코드: 403<br>
권한이 없습니다. 인증 토큰 입력 하지 않았으므로 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.

코드: 500<br>
내부 서버 오류

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>API 작업

작업 API를 다음 패치 및 Get 작업을 지원합니다.

#### <a name="list-outstanding-operations"></a>목록 처리 중인 작업 

현재 게시자에 대 한 처리 중인 작업을 나열합니다. 

**가져오기:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`**

*쿼리 매개 변수:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   이 요청에 사용할 작업의 버전입니다.                |
| subscriptionId     | 해결 API를 사용 하 여 토큰을 해결 한 후에 얻은 SaaS 구독의 고유 식별자입니다.  |

*요청 헤더:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   콘텐츠 형식     |  `application/json` |
|  x-ms-requestid    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.  |
|  권한 부여     |  [JSON 웹 토큰 (JWT) 전달자를 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*응답 코드:*

코드: 200<br> 보류 중인 구독에서 작업의 목록을 가져옵니다.<br>
응답 페이로드:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```

코드: 404<br>
찾을 수 없음

코드: 400<br>
잘못 된 요청 유효성 검사 오류

코드: 403<br>
권한이 없습니다. 인증 토큰 입력 하지 않았으므로 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.

코드: 500<br>
내부 서버 오류

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>작업 상태 가져오기

지정 된 트리거된 비동기 작업의 상태를 추적 하려면 게시자를 사용 하도록 설정 (구독 / 구독을 취소 / 변경 계획 / 변경 수량).

**가져오기:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      |  `application/json`   |
|  x-ms-requestid    |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.  |
|  권한 부여     |[JSON 웹 토큰 (JWT) 전달자를 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*응답 코드:* 코드: 200<br> SaaS 작업 보류 된 가져옵니다.<br>
응답 페이로드:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

코드: 404<br>
찾을 수 없음

코드: 400<br>
잘못 된 요청 유효성 검사 오류

코드: 403<br>
권한이 없습니다. 인증 토큰 입력 하지 않았으므로 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.
 
코드: 500<br> 내부 서버 오류

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>작업의 상태를 업데이트 합니다.

제공 된 값을 사용 하 여 성공/실패를 표시 하는 작업의 상태를 업데이트 합니다.

**Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | 해결 API를 사용 하 여 토큰을 해결 한 후에 얻은 SaaS 구독의 고유 식별자입니다.  |
|  operationId       | 완료 중인 작업입니다. |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|   콘텐츠 형식     | `application/json`   |
|   x-ms-requestid   |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다. |
|  권한 부여     |  [JSON 웹 토큰 (JWT) 전달자를 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*요청 페이로드:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*응답 코드:*

코드: 200<br> ISV 측면에 대 한 작업의 완료를 알리기 위해 호출 합니다. 예를 들어이 응답 사용자/계획을 변경 하 여 신호를 보낼 수 있습니다.

코드: 404<br>
찾을 수 없음

코드: 400<br>
잘못 된 요청 유효성 검사 오류

코드: 403<br>
권한이 없습니다. 인증 토큰 입력 하지 않았으므로 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.

코드: 409<br>
충돌 합니다. 예를 들어 최신 트랜잭션이 이미 충족

코드: 500<br> 내부 서버 오류

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="webhook-on-the-saas-service"></a>SaaS 서비스에서 웹 후크

게시자는 사용자에 게 사전에 알리는 해당 서비스의 변경 내용을이 SaaS 서비스에서 웹 후크를 구현 해야 합니다. API는 인증 되지 않은 것 하 고 Microsoft SaaS 서비스에 의해 호출 됩니다. SaaS 서비스는 작업의 유효성을 검사 하 고 웹 후크 알림을 작업을 수행 하기 전에 권한을 부여 하는 API를 호출 해야 합니다.

```json
{
  "id": "<this is a Guid operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher’s name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
여기서 작업 중 하나일 수 있습니다. 
- `Subscribe`(때 리소스 활성화 된)
- `Unsubscribe`(경우 리소스가 삭제 되었습니다)
- `ChangePlan`(변경 계획 작업이 완료 되 면)
- `ChangeQuantity`(수량 변경 작업에 완료 되 면)
- `Suspend`(때 리소스 일시 중단 됨)
- `Reinstate`(때 리소스에 복원 되었습니다 일시 중단 후)

여기서 상태 중 하나일 수 있습니다. <br>
        -NotStarted, <br>
        -InProgress, <br>
        -성공 <br>
        -실패 <br>
        -충돌 <br>

실행 가능한 상태 웹 후크 알림의 성공 및 실패는입니다. 작업의 수명 주기 NotStarted에서 성공/실패/충돌 같은 터미널 상태입니다. 시작 되지 않은 경우, 진행 중인 작업에 작업을 수행 하기 전에 터미널 상태에 도달할 때까지 상태 가져오기 작업 API 통해 요청을 계속 하십시오. 

## <a name="mock-api"></a>Mock API

프로젝트 테스트 및 개발, 특히 프로토타입 생성을 사용 하 여 시작 하는 데 모의 Api를 사용할 수 있습니다. 

호스트 끝점: `https://marketplaceapi.microsoft.com/api` <br/>
API 버전: `2018-09-15` <br/>
인증 필요 없음 <br/>
예제 Uri: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

API 끝점 경로 Mock와 실제 Api를 모두에서 동일 하지만 API 버전 다릅니다. 버전에는 Mock의 경우 2018-09-15 및 프로덕션 버전의 경우 2018-08-31입니다. 

이 문서에서는 API 호출의 모의 호스트 끝점을 만들 수 있습니다. 응답으로 다시 모의 데이터를 가져오는 예상할 수 있습니다. 일반적으로 대 한 응답으로 다시 모의 데이터를 가져오는 예상할 수 있습니다. 모의 API에서 업데이트 구독 메서드를 호출 하는 항상 500을 반환합니다. 

## <a name="next-steps"></a>다음 단계

개발자 또한 프로그래밍 방식으로 검색할 수 및 워크 로드, 제품 및 게시자의 조작을 사용 하 여 프로필을 [클라우드 파트너 포털 REST Api](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)합니다.
