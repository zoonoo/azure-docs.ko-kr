---
title: SaaS 주문 처리 API v2 | Azure 마켓플레이스
description: 이 문서에서는 연결된 주문 처리 v2 API를 사용하여 AppSource 및 Azure 마켓플레이스에서 SaaS 오퍼를 만들고 관리하는 방법을 설명합니다.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/18/2019
ms.author: dsindona
ms.openlocfilehash: ca49418013357ecaae62ea5e91374eaa1cbde59d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275767"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS 처리 API, 버전 2 

이 문서에서는 파트너가 AppSource 마켓플레이스 및 Azure 마켓플레이스에서 SaaS 응용 프로그램을 판매할 수 있도록 하는 API에 대해 자세히 설명합니다. 이러한 API는 AppSource 및 Azure 마켓플레이스에서 거래 가능한 SaaS 오퍼의 요구 사항입니다.

## <a name="managing-the-saas-subscription-life-cycle"></a>SaaS 구독 수명 주기 관리

Azure SaaS는 SaaS 구독 구매의 전체 수명 주기를 관리합니다. 이행 API를 메커니즘으로 사용하여 파트너와 함께 실제 이행, 계획 변경 및 구독 삭제를 유도합니다. 고객의 청구서는 Microsoft가 유지 관리하는 SaaS 구독의 상태를 기반으로 합니다. 다음 다이어그램은 상태 간의 변경을 구동하는 상태와 작업을 설명합니다.

![SaaS 구독 수명 주기 상태](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>SaaS 구독의 상태

다음 표에는 각 구독에 대한 설명 및 시퀀스 다이어그램을 포함하여 SaaS 구독에 대한 프로비저닝 상태가 나열되어 있습니다(해당하는 경우). 

#### <a name="provisioning"></a>프로비전

고객이 구매를 시작하면 파트너는 URL 매개 변수를 사용하는 고객 대화형 웹 페이지의 권한 부여 코드에서 이 정보를 받습니다. 예를 들어 `https://contoso.com/signup?token=..`파트너 센터의 방문 페이지 `https://contoso.com/signup`URL은 은 입니다. 확인 API를 호출하여 프로비저닝 서비스의 세부 정보에 대해 인증 코드의 유효성을 검사하고 교환할 수 있습니다.  SaaS 서비스가 프로비저닝을 완료하면 활성화 호출을 전송하여 이행이 완료되고 고객에게 대금이 청구될 수 있음을 나타냅니다. 

다음 다이어그램에서는 프로비저닝 시나리오에 대한 API 호출 순서를 보여 주며 있습니다.  

![API는 SaaS 서비스 프로비저닝을 요청합니다.](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>프로비전됨

이 상태는 프로비저닝된 서비스의 정상 상태입니다.

##### <a name="provisioning-for-update"></a>업데이트를 위한 프로비저닝 

이 상태는 기존 서비스에 대한 업데이트가 보류 중임을 의미합니다. 이러한 업데이트는 고객이 마켓플레이스 또는 SaaS 서비스에서 시작할 수 있습니다(직접 고객 거래에만 해당).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>업데이트 프로비저닝(마켓플레이스에서 시작된 경우)

다음 다이어그램은 마켓플레이스에서 업데이트를 시작할 때의 작업 순서를 보여 주며 있습니다.

![마켓플레이스에서 업데이트가 시작될 때 API 호출](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>업데이트 프로비저닝(SaaS 서비스에서 시작된 경우)

다음 다이어그램은 SaaS 서비스에서 업데이트를 시작할 때의 작업을 보여 주며 있습니다. 웹 후크 호출은 SaaS 서비스에서 시작한 구독에 대한 업데이트로 대체됩니다. 

![SaaS 서비스에서 업데이트를 시작할 때 API 호출](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>일시 중단

이 상태는 고객의 결제가 수신되지 않았음을 나타냅니다. 정책에 따라 구독을 취소하기 전에 고객에게 유예 기간을 제공합니다. 구독이 이 상태에 있는 경우: 

- 파트너는 서비스에 대한 사용자의 액세스를 저하시키거나 차단하도록 선택할 수 있습니다.
- 구독은 데이터 나 설정의 손실없이 모든 기능을 복원 할 수있는 복구 가능한 상태로 유지되어야합니다. 
- 이행 API 또는 유예 기간이 끝날 때 프로비저닝 해제 요청을 통해 이 구독에 대한 복원 요청을 받을 것으로 예상됩니다. 

#### <a name="unsubscribed"></a>탈퇴 

구독은 명시적 고객 요청 또는 회비 미납에 대한 응답으로 이 상태에 도달합니다. 파트너의 기대치는 특정 일 수일 동안 요청에 따라 복구를 위해 고객의 데이터가 보존된 다음 삭제된다는 것입니다. 


## <a name="api-reference"></a>API 참조

이 섹션에서는 SaaS *구독 API* 및 *운영 API를*간역합니다.  버전 2 `api-version` API에 대한 매개 `2018-08-31`변수값은 .  


### <a name="parameter-and-entity-definitions"></a>매개 변수 및 엔터티 정의

다음 표에는 이행 API에서 사용하는 공통 매개 변수 및 엔터티에 대한 정의가 나열되어 있습니다.

|     엔터티/매개 변수     |     정의                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | SaaS 리소스의 GUID 식별자입니다.  |
| `name`                   | 고객이 이 리소스에 대해 제공한 친숙한 이름입니다. |
| `publisherId`            | 각 게시자의 고유 문자열 식별자(예: "contoso"). |
| `offerId`                | 각 오퍼에 대한 고유 문자열 식별자(예: "offer1").  |
| `planId`                 | 각 계획/SKU에 대한 고유 문자열 식별자(예: "silver"). |
| `operationId`            | 특정 작업에 대한 GUID 식별자입니다.  |
|  `action`                | 리소스에서 수행중인 작업( `Unsubscribe`" `Suspend` `Reinstate`또는 `ChangePlan`" `ChangeQuantity` `Transfer`) |
|   |   |

전역고유[식별자(GUID)는](https://en.wikipedia.org/wiki/Universally_unique_identifier)일반적으로 자동으로 생성되는 128비트(32-헥사드) 숫자입니다. 

#### <a name="resolve-a-subscription"></a>구독 확인 

해결 끝점을 사용하면 게시자가 마켓플레이스 토큰을 영구 리소스 ID로 확인할 수 있습니다. 리소스 ID는 SaaS 구독의 고유 식별자입니다. 사용자가 파트너의 웹 사이트로 리디렉션되면 URL에는 쿼리 매개 변수에 토큰이 포함됩니다. 파트너는 이 토큰을 사용하고 이를 해결하기 위해 요청을 해야 합니다. 응답에는 고유한 SaaS 구독 ID, 이름, 제안 ID 및 리소스에 대한 계획이 포함됩니다. 이 토큰은 1시간 동안만 유효합니다. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>게시<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |

*헤더 요청:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      | `application/json` |
|  x-ms-requestid    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다. |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트와 서버 측의 이벤트의 상관 관계를 지정합니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다.  |
|  권한 부여     |  [JSON 웹 토큰 (JWT) 베어러 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) 예를 들어"`Bearer <access_token>`" " |
|  x-ms-marketplace-token  |  사용자가 Azure에서 SaaS 파트너의 웹 사이트로 리디렉션될 때 URL의 토큰 쿼리 `https://contoso.com/signup?token=..`매개 변수(예: ). *참고:* URL을 사용하기 전에 브라우저에서 토큰 값을 디코딩합니다.  |

*응답 코드:*

코드: 200<br>
불투명 토큰을 SaaS 구독으로 확인합니다. 응답 본문:
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

코드: 400<br>
잘못된 요청입니다. x-ms-마켓플레이스 토큰이 없거나, 형식이 잘못되었거나, 만료되었습니다.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공되지 않았거나 유효하지 않거나 요청이 현재 게시자가 아닌 획득에 액세스하려고 합니다.

코드: 404<br>
찾을 수 없음

코드: 500<br>
내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>구독 API

구독 API는 다음 HTTPS 작업을 지원합니다: **받기**, **게시,** **패치**및 **삭제**.


#### <a name="list-subscriptions"></a>구독 목록

게시자의 모든 SaaS 구독을 나열합니다.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>가져오기<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  이 요청에 사용할 작업의 버전입니다.  |

*헤더 요청:*

|                    |                   |
|  ---------------   |  ---------------  |
| 콘텐츠 형식       |  `application/json`  |
| x-ms-requestid     |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다. |
| x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트와 서버 측의 이벤트의 상관 관계를 지정합니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다.  |
| 권한 부여      |  [JSON 웹 토큰 (JWT) 베어러 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) 예를 들어"`Bearer <access_token>`" "  |

*응답 코드:*

코드: 200 <br/>
인증 토큰을 기반으로 모든 게시자의 오퍼에 대한 게시자 및 해당 구독을 가져옵니다.

>[!Note]
>[모의 API는](#mock-apis) 오퍼를 처음 개발할 때 사용되며 실제로 오퍼를 게시할 때 실제 API를 사용해야 합니다.  실제 API와 모의 API는 코드의 첫 번째 줄에 따라 다릅니다.  실제 API에는 섹션이 `subscription` 있지만 이 섹션은 모의 API에 대해 존재하지 않습니다.

모의 API에 대한 응답 페이로드:<br>

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
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```
그리고 실제 API의 경우: <br>

```json
{
  "subscriptions": [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant, object id and email address for which SaaS subscription is purchased.
              "emailId": "<email>",
              "objectId": "<guid>",                     
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant, object id and email address that purchased the SaaS subscription. These could be different for reseller scenario
              "emailId": "<email>",
              "objectId": "<guid>",                      
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.(optional field - default false)
          "isTest": false, //indicating whether the current subscription is a test asset
          "sandboxType": "None", // Possible Values: None, Csp (Csp sandbox purchase)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "@nextLink": ""
}
```
연속 토큰은 검색 할 계획의 추가 "페이지"가있는 경우에만 존재합니다. 

코드: 403 <br>
권한이 없습니다. 인증 토큰이 제공되지 않았거나 유효하지 않거나 요청이 현재 게시자가 아닌 획득에 액세스하려고 합니다. 

코드: 500<br>
내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>구독 받기

지정된 SaaS 구독을 가져옵니다. 이 호출을 사용하여 라이센스 정보 및 계획 정보를 가져옵니다.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>가져오기<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   해결 API를 통해 토큰을 해결한 후 얻은 SaaS 구독의 고유 식별자입니다.   |
|  ApiVersion        |   이 요청에 사용할 작업의 버전입니다.   |

*헤더 요청:*

|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      |  `application/json`  |
|  x-ms-requestid    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다. |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트와 서버 측의 이벤트의 상관 관계를 지정합니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다.  |
|  권한 부여     |  [JSON 웹 토큰 (JWT) 베어러 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) 예를 들어"`Bearer <access_token>`" "  |

*응답 코드:*

코드: 200<br>
식별자에서 SaaS 구독을 가져옵니다. 응답 페이로드:<br>

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
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "isFreeTrial": "true", // true - customer subscription is currently in free trial, false - customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공되지 않았거나 유효하지 않거나 요청이 현재 게시자가 아닌 획득에 액세스하려고 합니다.

코드: 404<br>
찾을 수 없음<br> 

코드: 500<br>
내부 서버 오류.<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>사용 가능한 계획 목록

이 호출을 사용하여 현재 게시자의 비공개 또는 공개 제안이 있는지 확인합니다.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>가져오기<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   이 요청에 사용할 작업의 버전입니다.  |

*헤더 요청:*

|                    |                   |
|  ---------------   |  ---------------  |
|   콘텐츠 형식     |  `application/json` |
|   x-ms-requestid   |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다. |
|  x-ms-correlationid  | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트와 서버 측의 이벤트의 상관 관계를 지정합니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다. |
|  권한 부여     |  [JSON 웹 토큰 (JWT) 베어러 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  예를 들어"`Bearer <access_token>`" " |

*응답 코드:*

코드: 200<br>
고객에 대 한 사용 가능한 계획의 목록을 가져옵니다. 응답 본문:

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
권한이 없습니다. 인증 토큰이 제공되지 않았거나 유효하지 않거나 요청이 현재 게시자가 아닌 획득에 액세스하려고 합니다. <br> 

코드: 500<br>
내부 서버 오류.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>구독 활성화

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>게시<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | Resolve API를 사용하여 토큰을 해결한 후 얻은 SaaS 구독의 고유 식별자입니다.  |

*헤더 요청:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      | `application/json`  |
|  x-ms-requestid    | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다.  |
|  x-ms-correlationid  | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 문자열은 클라이언트 작업의 모든 이벤트와 서버 측의 이벤트의 상관 관계를 지정합니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다.  |
|  권한 부여     |  [JSON 웹 토큰 (JWT) 베어러 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  예를 들어"`Bearer <access_token>`" " |

*페이로드 요청:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*응답 코드:*

코드: 200<br>
구독을 활성화합니다.<br>

코드: 400<br>
잘못된 요청: 유효성 검사 실패.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공되지 않았거나 유효하지 않거나 요청이 현재 게시자가 아닌 획득에 액세스하려고 합니다.

코드: 404<br>
찾을 수 없음

코드: 500<br>
내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>구독에 대한 요금변경

구독에서 계획을 업데이트합니다.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>패치<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | Resolve API를 사용하여 토큰을 해결한 후 얻은 SaaS 구독의 고유 식별자입니다.  |

*헤더 요청:*

|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      | `application/json` |
|  x-ms-requestid    |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다.  |
|  x-ms-correlationid  |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트와 서버 측의 이벤트의 상관 관계를 지정합니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다.    |
| 권한 부여      |  [JSON 웹 토큰 (JWT) 베어러 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  예를 들어"`Bearer <access_token>`" "  |

*페이로드 요청:*

```json
Request Body:
{
    "planId": "gold"
}
```

*헤더 요청:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 작업의 상태를 얻으려면 리소스에 대한 링크입니다.   |

*응답 코드:*

코드: 202<br>
계획 변경 요청이 수락되었습니다. 파트너는 성공 또는 실패를 결정하기 위해 운영 위치를 폴링해야 합니다. <br>

코드: 400<br>
잘못된 요청: 유효성 검사 실패.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공되지 않았거나 유효하지 않거나 요청이 현재 게시자가 아닌 획득에 액세스하려고 합니다.

코드: 404<br>
찾을 수 없음

코드: 500<br>
내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>계획 또는 수량만 한 번에 패치할 수 있으며 둘 다 패치할 수는 없습니다. **업데이트가** 있는 구독에 대한 편집이 에 `allowedCustomerOperations`없습니다.

#### <a name="change-the-quantity-on-the-subscription"></a>구독 수량 변경

구독의 수량을 업데이트합니다.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>패치:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | Resolve API를 사용하여 토큰을 해결한 후 얻은 SaaS 구독의 고유 식별자입니다.  |

*헤더 요청:*

|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      | `application/json` |
|  x-ms-requestid    |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다.  |
|  x-ms-correlationid  |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트와 서버 측의 이벤트의 상관 관계를 지정합니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다.    |
| 권한 부여      |  [JSON 웹 토큰 (JWT) 베어러 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  예를 들어"`Bearer <access_token>`" "  |

*페이로드 요청:*

```json
Request Body:
{
    "quantity": 5
}
```

*헤더 요청:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 리소스에 연결하여 작업의 상태를 가져옵니다.   |

*응답 코드:*

코드: 202<br>
수량 변경 요청이 수락되었습니다. 파트너는 성공 또는 실패를 결정하기 위해 운영 위치를 폴링해야 합니다. <br>

코드: 400<br>
잘못된 요청: 유효성 검사 실패.


코드: 403<br>
권한이 없습니다. 인증 토큰이 제공되지 않았거나 유효하지 않거나 요청이 현재 게시자가 아닌 획득에 액세스하려고 합니다.

코드: 404<br>
찾을 수 없음

코드: 500<br>
내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>계획 또는 수량만 한 번에 패치할 수 있으며 둘 다 패치할 수는 없습니다. **업데이트가** 있는 구독에 대한 편집이 에 `allowedCustomerOperations`없습니다.

#### <a name="delete-a-subscription"></a>구독 삭제

지정된 구독을 구독 취소하고 삭제합니다.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>DELETE<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | Resolve API를 사용하여 토큰을 해결한 후 얻은 SaaS 구독의 고유 식별자입니다.  |

*헤더 요청:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   콘텐츠 형식     |  `application/json` |
|  x-ms-requestid    |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다.   |
|  x-ms-correlationid  |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트와 서버 측의 이벤트의 상관 관계를 지정합니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다.   |
|  권한 부여     |  [JSON 웹 토큰 (JWT) 베어러 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  예를 들어"`Bearer <access_token>`" "  |

*응답 코드:*

코드: 202<br>
파트너가 SaaS 구독을 구독 취소하라는 호출을 시작했습니다.<br>

코드: 400<br>
에 **삭제가** 아닌 구독에서 `allowedCustomerOperations`삭제

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공되지 않았거나 유효하지 않거나 요청이 현재 게시자가 아닌 획득에 액세스하려고 합니다.

코드: 404<br>
찾을 수 없음

코드: 500<br>
내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>운영 API

운영 API는 다음 패치 및 Get 작업을 지원합니다.

#### <a name="list-outstanding-operations"></a>미해결 작업 목록 

현재 게시자의 미해결 작업을 나열합니다. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>가져오기<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   이 요청에 사용할 작업의 버전입니다.                |
| subscriptionId     | Resolve API를 사용하여 토큰을 해결한 후 얻은 SaaS 구독의 고유 식별자입니다.  |

*헤더 요청:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   콘텐츠 형식     |  `application/json` |
|  x-ms-requestid    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다.  |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트와 서버 측의 이벤트의 상관 관계를 지정합니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다.  |
|  권한 부여     |  [JSON 웹 토큰 (JWT) 베어러 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  예를 들어"`Bearer <access_token>`" "  |

*응답 코드:*

코드: 200<br> 구독에서 보류 중인 작업 목록을 가져옵니다. 응답 페이로드:

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


코드: 400<br>
잘못된 요청: 유효성 검사 실패.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공되지 않았거나 유효하지 않거나 요청이 현재 게시자가 아닌 획득에 액세스하려고 합니다.

코드: 404<br>
찾을 수 없음

코드: 500<br>
내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>작업 상태 얻기

게시자가 지정된 트리거된 `Subscribe`비동기 작업(예: " `Unsubscribe` `ChangePlan`및)의 `ChangeQuantity`상태를 추적할 수 있습니다.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>가져오기<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |

*헤더 요청:*

|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      |  `application/json`   |
|  x-ms-requestid    |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다.  |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트와 서버 측의 이벤트의 상관 관계를 지정합니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다.  |
|  권한 부여     |  [JSON 웹 토큰 (JWT) 베어러 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) 예를 들어"`Bearer <access_token>`" "  |

*응답 코드:*<br>

코드: 200<br> 지정된 보류 중인 SaaS 작업을 가져옵니다. 응답 페이로드:

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

코드: 400<br>
잘못된 요청: 유효성 검사 실패.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공되지 않았거나 유효하지 않거나 요청이 현재 게시자가 아닌 획득에 액세스하려고 합니다.
 
코드: 404<br>
찾을 수 없음

코드: 500<br> 내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>작업 상태 업데이트

제공된 값의 성공 또는 실패를 나타내기 위해 작업의 상태를 업데이트합니다.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>패치<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | Resolve API를 사용하여 토큰을 해결한 후 얻은 SaaS 구독의 고유 식별자입니다.  |
|  operationId       | 작업이 완료되는 작업입니다. |

*헤더 요청:*

|                    |                   |
|  ---------------   |  ---------------  |
|   콘텐츠 형식     | `application/json`   |
|   x-ms-requestid   |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다. |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트와 서버 측의 이벤트의 상관 관계를 지정합니다. 이 값이 제공되지 않으면 응답 헤더에 생성되고 제공됩니다. |
|  권한 부여     |  [JSON 웹 토큰 (JWT) 베어러 토큰을 가져옵니다.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  예를 들어"`Bearer <access_token>`" "  |

*페이로드 요청:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*응답 코드:*

코드: 200<br> 파트너 측에서 작업 완료를 알리는 호출입니다. 예를 들어, 이 응답은 좌석 또는 계획의 변경을 신호할 수 있습니다.

코드: 400<br>
잘못된 요청: 유효성 검사 실패.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공되지 않았거나 유효하지 않거나 요청이 현재 게시자가 아닌 획득에 액세스하려고 합니다.

코드: 404<br>
찾을 수 없음

코드: 409<br>
충돌. 예를 들어 최신 트랜잭션이 이미 충족되었습니다.

코드: 500<br> 내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>SaaS 서비스에 웹후크 구현

게시자는 이 SaaS 서비스에서 웹후크를 구현하여 사용자에게 서비스 변경 사항을 사전에 알립니다. SaaS 서비스는 웹후크 알림에서 작업을 수행하기 전에 운영 API를 호출하여 유효성을 검사하고 권한을 부여해야 합니다.


```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher's name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
작업이 다음 중 하나가 될 수 있는 위치: 
- `Unsubscribe`(리소스가 삭제된 경우)
- `ChangePlan`(변경 계획 작업이 완료된 경우)
- `ChangeQuantity`(변경 수량 작업이 완료된 경우)
- `Suspend`(리소스가 일시 중단된 경우)
- `Reinstate`(일시 중단 후 리소스가 복원된 경우)

상태가 다음 중 하나가 될 수 있는 위치: 
- **NotStarted** <br>
 - **Inprogress** <br>
- **성공** <br>
- **실패** <br>
- **충돌** <br>

웹후크 알림에서 실행 가능한 상태는 **성공** 및 **실패**상태입니다. 작업의 수명 주기는 **NotStarted에서** **성공,** **실패**또는 **충돌과**같은 터미널 상태로 입니다. **NotStarted** 또는 **InProgress를**수신하는 경우 작업을 수행하기 전에 작업이 터미널 상태에 도달할 때까지 GET API를 통해 상태를 계속 요청합니다. 

## <a name="mock-apis"></a>모의 API

모의 API를 사용하여 개발, 특히 프로토타이핑및 프로젝트 테스트에 도움을 줄 수 있습니다. 

호스트 끝점: `https://marketplaceapi.microsoft.com/api` (인증이 필요 없음)<br/>
API 버전:`2018-09-15`<br/>
샘플 URI:`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

API 끝점 경로는 모의 API와 실제 API 모두에서 동일하지만 API 버전은 다릅니다. 버전은 `2018-09-15` 모의 버전과 `2018-08-31` 프로덕션 버전용입니다. 

이 문서의 모든 API 호출은 모의 호스트 끝점에 대해 만들 수 있습니다. 일반적으로 모의 데이터를 응답으로 다시 얻을 것으로 예상합니다. 모의 API에서 업데이트 구독 메서드에 대한 호출은 항상 500을 반환합니다. 

## <a name="next-steps"></a>다음 단계

개발자는 [또한 클라우드 파트너 포털 REST API를](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)사용하여 워크로드, 오퍼 및 퍼블리셔 프로필을 프로그래밍 방식으로 검색하고 조작할 수 있습니다.
