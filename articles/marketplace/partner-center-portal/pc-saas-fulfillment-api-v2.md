---
title: Microsoft 상업적 marketplace의 SaaS 등록 Api v2
description: Api 버전 2를 사용 하 여 Microsoft AppSource 및 Azure Marketplace에서 SaaS 제품을 만들고 관리 하는 방법에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 4a98207ef5b03f77a4f741894ec210f7551c5933
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378137"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>상업적 marketplace의 SaaS 등록 Api 버전 2

이 문서에서는 파트너가 Microsoft AppSource 및 Azure Marketplace에서 SaaS 제품을 판매할 수 있도록 하는 Api에 대해 자세히 설명 합니다. 파트너 센터에서 불가능 SaaS 제품을 게시 하려면 게시자가 이러한 Api와의 통합을 구현 해야 합니다.

## <a name="managing-the-saas-subscription-life-cycle"></a>SaaS 구독 수명 주기 관리

상용 marketplace는 최종 고객에 의해 구매한 후 SaaS 구독의 전체 수명 주기를 관리 합니다.  방문 페이지, 다시 사용 Api, 작업 Api 및 웹 후크를 사용 하 여 실제 SaaS 구독 활성화 및 사용, 업데이트 및 구독의 취소를 구동 합니다.  최종 고객의 청구서는 Microsoft에서 유지 관리 하는 SaaS 구독의 상태를 기준으로 합니다. 

### <a name="states-of-a-saas-subscription"></a>SaaS 구독의 상태

SaaS 구독 상태와 적용 가능한 작업이 표시 됩니다.

![Marketplace에서 SaaS 구독의 수명 주기](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>구매 되었지만 아직 활성화 되지 않음 (*PendingFulfillmentStart*)

최종 고객이 marketplace에서 SaaS 제품을 구매한 후 게시자는 새 SaaS 계정이 생성 되 고 게시자 쪽에서 최종 고객에 대해 구성 되도록 해당 제품에 대 한 알림이 제공 되어야 합니다.

계정을 만들려면 다음을 수행 합니다.

1. 고객은 Microsoft AppSource 또는 Azure Portal에서 구매가 완료 된 후 SaaS 제품에 사용할 수 있는 **구성** 단추를 클릭 해야 합니다. 또는 구매 직후 고객이 받게 될 전자 메일의 또는입니다.
2. 그러면 Microsoft는 새 브라우저 탭에서 토큰 매개 변수 (상업적 marketplace 구매 식별 토큰)를 사용 하 여 방문 페이지 URL을 열고 구매에 대해 파트너에 게 알립니다.

이러한 호출의 예로는가 있지만 `https://contoso.com/signup?token=<blob>` 파트너 센터에서이 SaaS 제안의 방문 페이지 URL은으로 구성 됩니다 `https://contoso.com/signup` . 이 토큰은 SaaS 구매 및 고객을 고유 하 게 식별 하는 ID를 게시자에 게 제공 합니다.

>[!NOTE]
>고객이 Microsoft 쪽에서 구성 프로세스를 시작할 때까지 게시자는 SaaS 구매에 대 한 통지를 받지 않습니다.

방문 페이지 url은 연중 무휴 실행 되며 Microsoft에서 항상 새 전화를 받을 준비가 되어 있어야 합니다. 방문 페이지를 사용할 수 없게 되 면 고객은 SaaS 서비스에 등록 하 고 사용을 시작할 수 없습니다.

그런 다음 헤더 매개 변수 값으로 [SaaS RESOLVE API](#resolve-a-purchased-subscription)를 호출 하 여 게시자에서 *토큰* 을 다시 Microsoft로 전달 해야 합니다 `x-ms-marketplace-token header` .  API 호출의 결과로, 토큰은 구매한 구매의 고유 ID, 구매한 제안 ID, 구매한 요금제 ID 등 SaaS 구매 세부 정보를 위해 교환 됩니다.

방문 페이지에서 고객은 Azure Active Directory (Azure AD) SSO (Single Sign-on)를 통해 신규 또는 기존 SaaS 계정에 로그온 해야 합니다.

게시자는이 흐름에 Microsoft에서 요구 하는 사용자 환경을 제공 하기 위해 SSO 로그인을 구현 해야 합니다.  SSO를 구성할 때 다중 테 넌 트 Azure AD 응용 프로그램을 사용 하 여 회사 및 학교 계정이 나 개인 Microsoft 계정을 모두 허용 합니다.  이 요구 사항은 방문 페이지 및 Microsoft 자격 증명을 사용 하 여 이미 로그인 한 경우 SaaS 서비스로 리디렉션되는 사용자 에게만 적용 됩니다. SaaS 서비스에 대 한 모든 로그인에는 적용 되지 않습니다.

> [!NOTE]
>SSO 로그인에 관리자가 앱에 대 한 권한을 부여 해야 하는 경우 파트너 센터의 제안에 대 한 설명은 관리 수준 액세스가 필요 하다는 것을 공개 해야 합니다. 이는 [상업적 marketplace 인증 정책을](https://docs.microsoft.com/legal/marketplace/certification-policies#10003-authentication-options)준수 하기 위한 것입니다.

로그인 한 후에는 고객이 게시자 쪽에서 SaaS 구성을 완료 해야 합니다. 그런 다음 게시자는 [구독 API 활성화](#activate-a-subscription) 를 호출 하 여 SaaS 계정의 프로비저닝이 완료 되었음을 Marketplace에 보내야 합니다.
그러면 고객의 청구 주기가 시작 됩니다. 구독 활성화 API 호출에 성공 하지 않으면 고객에 게 구매 요금이 청구 되지 않습니다.


![프로 비전 시나리오에 대 한 API 호출](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>활성 (구독 됨)

이 상태는 프로 비전 된 SaaS 구독의 안정 된 상태입니다. [구독 활성화 API](#activate-a-subscription) 호출이 Microsoft 쪽에서 처리 되 면 SaaS 구독은 구독으로 표시 됩니다. 이제 고객이 게시자 측에서 SaaS 서비스를 사용할 준비가 되었으며 고객에 게 요금이 청구 됩니다.

SaaS 구독이 이미 활성 상태이 고 고객이 Azure Portal 또는 M365 관리 센터에서 saas 환경 **관리** 를 시작 하도록 선택 하는 경우 활성 흐름과 동일한 *토큰* 매개 변수를 사용 하 여 MICROSOFT에서 **방문 페이지 URL** 을 다시 호출 합니다.  게시자는 기존 SaaS 계정의 새로운 구매와 관리를 구분 하 고이에 따라이 방문 페이지 URL 호출을 처리 해야 합니다.

#### <a name="being-updated-subscribed"></a>업데이트 (구독)

이 작업은 기존 활성 SaaS 구독에 대 한 업데이트가 Microsoft와 게시자 모두에서 처리 됨을 의미 합니다. 이러한 업데이트는 다음을 통해 시작할 수 있습니다.

- 상업적 marketplace의 고객입니다.
- 상업적 marketplace의 CSP입니다.
- 게시자의 SaaS 사이트에서 고객 (구매를 만든 CSP에는 적용 되지 않음)

SaaS 구독에는 두 가지 유형의 업데이트를 사용할 수 있습니다.

- 고객이 구독에 대해 다른 계획을 선택할 때 계획을 업데이트 합니다.
- 고객이 구독에 대해 구매한 사용자 수를 변경 하는 경우 수량 업데이트

활성 구독만 업데이트할 수 있습니다. 구독이 업데이트 되는 동안 해당 상태는 Microsoft 쪽에서 활성 상태로 유지 됩니다.

##### <a name="update-initiated-from-the-marketplace"></a>Marketplace에서 시작 된 업데이트

이 흐름에서 고객은 M365 관리 센터에서 구독 계획 또는 좌석 수량을 변경 합니다.  

1. 업데이트를 입력 하면 Microsoft에서 파트너 센터의 **연결 webhook** 필드에 구성 된 게시자의 웹 후크 URL을 호출 하 고 적절 한 *작업* 및 기타 관련 매개 변수 값을 호출 합니다.  
1. 게시자 쪽에서 SaaS 서비스에 필요한 변경을 수행 하 고 [작업 API의 업데이트 상태](#update-the-status-of-an-operation)를 호출 하 여 변경이 완료 되 면 Microsoft에 알려야 합니다.
1. 패치가 실패 상태와 함께 전송 되는 경우 Microsoft 쪽에서 업데이트 프로세스가 완료 되지 않습니다.  SaaS 구독은 기존 계획과 수량을 사용 하 여 유지 됩니다.

Marketplace에서 시작한 업데이트 시나리오에 대 한 API 호출 시퀀스는 다음과 같습니다.

![Marketplace에서 시작한 업데이트에 대 한 API 호출](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>게시자에서 시작 된 업데이트

이 흐름에서 고객은 SaaS 서비스 자체에서 구매한 구독 계획 또는 수량을 변경 합니다. 

1. 게시자 쪽에서 요청 된 변경을 수행 하기 전에 게시자 코드에서 [변경 계획 api](#change-the-plan-on-the-subscription) 및/또는 [변경 수량 api](#change-the-quantity-of-seats-on-the-saas-subscription) 를 호출 해야 합니다. 

1. Microsoft에서 구독에 변경 내용을 적용 한 다음 동일한 변경 내용을 적용 하도록 **연결 Webhook** 를 통해 게시자에 게 알립니다.  

1. 그 다음에만 게시자가 SaaS 구독에 필요한 변경을 수행 하 고, [작업 API의 업데이트 상태](#update-the-status-of-an-operation)를 호출 하 여 변경 작업을 수행 하면 Microsoft에이를 알립니다.

게시자 쪽에서 시작한 업데이트 시나리오에 대 한 API 호출의 시퀀스입니다.

![게시자 쪽에서 시작한 업데이트에 대 한 API 호출](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>일시 중단 됨 (*일시 중단 됨*)

이 상태는 SaaS 서비스에 대 한 고객의 지불을 받지 못했음을 나타냅니다. Microsoft에서 제공 하는 SaaS 구독 상태 변경 내용에 대 한 알림이 게시자에 게 표시 됩니다. 알림은 *action* 매개 변수가 *Suspended*로 설정 된 웹 후크 호출을 통해 수행 됩니다.

게시자가 게시자 쪽에서 SaaS 서비스를 변경 하거나 변경할 수 없습니다. 이 정보를 일시 중단 된 고객에 게 제공 하 고 고객의 SaaS 서비스 액세스를 제한 하거나 차단 하는 것이 좋습니다.  결제는 수신 되지 않습니다.

Microsoft는 구독을 자동으로 취소 하기 전에 30 일의 유예 기간을 고객에 게 제공 합니다. 구독이 일시 중단 된 상태에 있는 경우:

* SaaS 계정은 ISV에서 복구 가능한 상태로 유지 되어야 합니다. 데이터 나 설정의 손실 없이 전체 기능을 복원할 수 있습니다.
* 유예 기간 동안 지불을 받은 경우이 구독에 대 한 복구 요청을 받을 것으로 간주 하거나, 유예 기간이 종료 될 때 webhook 메커니즘을 통해 프로 비전 해제 요청을 받을 수 있습니다.

구독 상태는 Microsoft 쪽에서 일시 중단 됨으로 변경 되 고 게시자는 모든 작업을 수행 합니다. 활성 구독만 일시 중단할 수 있습니다.

#### <a name="reinstated-suspended"></a>복원 (*일시 중단*)

구독을 복원 하 고 있습니다.

이 작업은 고객의 지불 기기가 다시 유효 하 고 SaaS 구독에 대 한 지불을 했음을 나타냅니다.  구독을 복원 하 고 있습니다. 이 경우 다음과 같습니다. 

1. Microsoft는 *작업* 매개 변수를 *복원* 값으로 설정 하 여 webhook를 호출 합니다.  
1. 게시자는이 구독이 게시자 쪽에서 다시 제대로 작동 하도록 합니다.
1. 게시자는 성공 상태를 사용 하 여 [Patch 작업 API](#update-the-status-of-an-operation) 를 호출 합니다.  
1. 그러면 복구에 성공 하 고 고객에 게 SaaS 구독에 대 한 요금이 다시 청구 됩니다. 
1. 패치가 실패 상태와 함께 전송 되 면 reinstatement 프로세스가 Microsoft 쪽에서 완료 되지 않습니다. 구독은 일시 중단 된 상태로 유지 됩니다.

패치가 실패 상태와 함께 전송 되 면 reinstatement 프로세스가 Microsoft 쪽에서 완료 되지 않습니다.  구독은 일시 중단 된 상태로 유지 됩니다.

일시 중단 된 구독만 복원할 수 있습니다.  SaaS 구독을 복원 하는 동안에는 해당 상태가 일시 중단 된 상태로 유지 됩니다.  이 작업이 완료 되 면 구독의 상태가 활성 상태가 됩니다.

#### <a name="renewed-subscribed"></a>갱신 됨 (*구독*됨)

구독 기간이 끝날 때 (월 또는 연도 이후) SaaS 구독은 Microsoft에서 자동으로 갱신 됩니다.  자동 갱신에 대 한 기본값은 모든 SaaS 구독에 *적용* 됩니다. 활성 SaaS 구독은 정기적으로 계속 갱신 됩니다. 구독을 갱신 하는 경우 Microsoft는 게시자에 게 알리지 않습니다. 고객은 M365 관리 포털을 통하거나 Azure Portal를 통해 SaaS 구독에 대 한 자동 갱신을 해제할 수 있습니다.  이 경우 SaaS 구독은 현재 청구 기간 종료 시 자동으로 취소 됩니다.  또한 고객은 언제 든 지 SaaS 구독을 취소할 수 있습니다.

활성 구독만 자동으로 갱신 됩니다.  갱신 하는 동안 구독을 활성 상태로 유지 하 고 자동 갱신에 성공 합니다.  갱신 후 구독 기간의 시작 및 종료 날짜가 새 기간의 날짜로 업데이트 됩니다.

지불 문제로 인해 자동 갱신에 실패 하는 경우 구독이 일시 중단 됩니다.  게시자에 게 알림이 제공 됩니다.

#### <a name="canceled-unsubscribed"></a>취소 됨 (*구독*취소 됨) 

구독은 게시자 사이트, Azure Portal 또는 M365 관리 센터에서 구독을 취소 하 여 명시적 고객 또는 CSP 작업에 응답 하 여이 상태에 도달 합니다.  Dues의 지불이 아닌 30 일 동안 일시 중단 된 상태로 인해 구독을 암시적으로 취소할 수도 있습니다.

취소 웹 후크 호출을 받을 때 게시자는 최소 7 일 동안 요청 시 복구를 위해 고객 데이터를 보존 해야 합니다. 그 다음에만 고객 데이터를 삭제할 수 있습니다.

SaaS 구독은 수명 주기 동안 언제 든 지 취소할 수 있습니다. 취소 한 후에는 구독을 다시 활성화할 수 없습니다.

## <a name="api-reference"></a>API 참조

이 섹션에서는 SaaS 구독 및 작업 Api에 대해 설명 합니다.

**구독 api** 는 구입부터 취소까지 SaaS 구독 수명 주기를 처리 하는 데 사용 해야 합니다.

**운영 api** 는 다음을 수행 하는 데 사용 해야 합니다.

* 처리 된 웹 후크 호출 확인 및 승인
* 게시자에서 승인 대기 중인 앱의 보류 중인 작업 목록을 가져옵니다.

### <a name="enforcing-tls-12-note"></a>TLS 1.2 메모 적용

TLS 버전 1.2 버전은 HTTPS 통신을 위한 최소 버전으로 곧 적용 됩니다. 코드에서이 TLS 버전을 사용 하는지 확인 합니다.  TLS 버전 1.0 및 1.1은 곧 사용 되지 않을 예정입니다.

### <a name="subscription-apis"></a>구독 Api

#### <a name="resolve-a-purchased-subscription"></a>구매한 구독 해결

확인 끝점을 사용 하면 게시자가 marketplace 구매 id 토큰 (구매한 *경우에도* [활성화 되지 않음](#purchased-but-not-yet-activated-pendingfulfillmentstart))을 지속적으로 구매한 SaaS 구독 id 및 세부 정보로 교환할 수 있습니다.

고객이 파트너의 방문 페이지 URL로 리디렉션되는 경우 고객 식별 토큰은이 URL 호출에서 *토큰* 매개 변수로 전달 됩니다. 파트너는이 토큰을 사용 하 고이 토큰을 확인 하도록 요청 해야 합니다. API 확인 응답에는 SaaS 구독 ID와 구매를 고유 하 게 식별 하는 기타 세부 정보가 포함 됩니다. 방문 페이지 URL 호출과 함께 제공 된 *토큰* 은 일반적으로 24 시간 동안 유효 합니다. 받은 *토큰이* 이미 만료 된 경우 최종 고객에 게 다음 지침을 제공 하는 것이 좋습니다.

"이 구매를 식별할 수 없습니다. Azure Portal 또는 M365 관리 센터에서이 SaaS 구독을 다시 열고" 계정 구성 "또는" 계정 관리 "단추를 다시 클릭 하세요."

Resolve API를 호출 하면 지원 되는 모든 상태에서 SaaS 구독의 구독 정보 및 상태가 반환 됩니다.

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>올리기`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31을 사용 합니다.   |

*요청 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  `x-ms-correlationid` |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `authorization`     |  이 API 호출을 수행 하는 게시자를 식별 하는 고유한 액세스 토큰입니다. 형식은 `"Bearer <accessaccess_token>"` [Azure AD 앱을 기반으로 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명 된 대로 게시자가 토큰 값을 검색 하는 경우입니다. |
|  `x-ms-marketplace-token`  | 확인할 marketplace 구매 id *토큰* 매개 변수입니다.  토큰은 고객이 SaaS 파트너의 웹 사이트로 리디렉션되는 경우 방문 페이지 URL 호출에 전달 됩니다 (예: `https://contoso.com/signup?token=<token><authorization_token>` ). <br> <br>  *참고:* 인코딩된 *토큰* 값은 방문 페이지 url의 일부 이므로이 API 호출에서 매개 변수로 사용 되기 전에 디코딩되 야 합니다.  <br> <br> Url에서 인코딩된 문자열의 예는 다음과 같습니다. `contoso.com/signup?token=ab%2Bcd%2Fef` 여기서 token은입니다. `ab%2Bcd%2Fef`  디코딩된 동일한 토큰은 다음과 같습니다. `Ab+cd/ef` |
| | |

*응답 코드:*

Code: 200은 제공 된를 기반으로 고유한 SaaS 구독 식별자를 반환 합니다 `x-ms-marketplace-token` .

응답 본문 예:

```json
{
    "id": "<guid>",  // purchased SaaS subscription ID 
    "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name 
    "offerId": "offer1", // purchased offer ID
    "planId": "silver", // purchased offer's plan ID
    "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
    "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
"pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
"pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
                   startDate": "2019-05-31", 
   "endDate": "2019-06-29",
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": [
      "Delete",
      "Update",
      "Read"
    ],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

코드: 400 잘못 된 요청입니다. `x-ms-marketplace-token` 이 없거나, 형식이 잘못 되었거나, 잘못 되었거나, 만료 되었습니다.

코드: 403 사용할 수 없음. 권한 부여 토큰이 잘못 되었거나 만료 되었거나 제공 되지 않았습니다.  요청에서 권한 부여 토큰을 만드는 데 사용 된 것과 다른 Azure AD 앱 ID로 게시 된 제안의 SaaS 구독에 액세스 하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md) 을 제대로 수행 하지 않는 증상입니다.

코드: 500 내부 서버 오류입니다.  API 호출을 다시 시도 합니다.  오류가 계속 되 면 [Microsoft 지원](https://partner.microsoft.com/support/v2/?stage=1)에 문의 하세요.

#### <a name="activate-a-subscription"></a>구독 활성화

최종 고객에 대해 SaaS 계정이 구성 되 면 게시자는 Microsoft 쪽에서 구독 활성화 API를 호출 해야 합니다.  이 API 호출이 성공 하지 않으면 고객에 게 요금이 청구 되지 않습니다.

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>올리기`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  --------   |  ---------------  |
| `ApiVersion`  |  2018-08-31을 사용 합니다.   |
| `subscriptionId` | 구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 [RESOLVE API](#resolve-a-purchased-subscription)를 사용 하 여 marketplace 권한 부여 토큰을 확인 한 후에 가져옵니다.
 |

*요청 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `x-ms-correlationid` |  클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 문자열은 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상관 관계를 설정 합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `authorization`      |  이 API 호출을 수행 하는 게시자를 식별 하는 고유한 액세스 토큰입니다. 형식은 `"Bearer <access_token>"` [Azure AD 앱을 기반으로 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명 된 대로 게시자가 토큰 값을 검색 하는 경우입니다. |

*요청 페이로드 예:*

```json
{ // needed for validation of the activation request
    "planId": "gold", // purchased plan, cannot be empty
    "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*응답 코드:*

코드: 200 구독이 Microsoft 쪽에서 구독으로 표시 되었습니다.

이 호출에 대 한 응답 본문이 없습니다.

코드: 400 잘못 된 요청: 유효성 검사에 실패 했습니다.

* `planId` 는 요청 페이로드에 존재 하지 않습니다.
* `planId` 에서 요청 페이로드가 구매한 것과 일치 하지 않습니다.
* `quantity` 에서 요청 페이로드가 구매한 것과 일치 하지 않습니다.
* SaaS 구독이 구독 또는 일시 중단 상태입니다.

코드: 403 사용할 수 없음. 인증 토큰이 잘못 되었거나 만료 되었거나 제공 되지 않았습니다. 요청에서 권한 부여 토큰을 만드는 데 사용 된 것과 다른 Azure AD 앱 ID로 게시 된 제안의 SaaS 구독에 액세스 하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md) 을 제대로 수행 하지 않는 증상입니다.

코드: 404를 찾을 수 없습니다. SaaS 구독이 구독 되지 않은 상태입니다.

코드: 500 내부 서버 오류입니다.  API 호출을 다시 시도 합니다.  오류가 계속 되 면 [Microsoft 지원](https://partner.microsoft.com/support/v2/?stage=1)에 문의 하세요.

#### <a name="get-list-of-all-subscriptions"></a>모든 구독 목록을 가져옵니다.

Marketplace에서 게시자가 게시 한 모든 제품에 대해 구매한 모든 SaaS 구독의 목록을 검색 합니다.  모든 가능한 상태에서 SaaS 구독이 반환 됩니다. 이 정보는 Microsoft 쪽에서 삭제 되지 않으므로 구독 되지 않은 SaaS 등록도 반환 됩니다.

이 API는 페이지가 매겨진 결과를 반환 합니다. 페이지 크기는 100입니다.

##### <a name="gethttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>가져오기`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  --------   |  ---------------  |
| `ApiVersion`  |  2018-08-31을 사용 합니다.  |
| `continuationToken`  | 선택적 매개 변수입니다. 결과의 첫 번째 페이지를 검색 하려면 빈 상태로 둡니다.  매개 변수에서 반환 된 값을 사용 `@nextLink` 하 여 다음 페이지를 검색 합니다. |

*요청 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `x-ms-correlationid` |  클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| `authorization`      |  이 API 호출을 수행 하는 게시자를 식별 하는 고유한 액세스 토큰입니다.  형식은 `"Bearer <access_token>"` [Azure AD 앱을 기반으로 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명 된 대로 게시자가 토큰 값을 검색 하는 경우입니다. |

*응답 코드:*

Code: 200 게시자의 권한 부여 토큰에 따라이 게시자의 모든 제품에 대 한 모든 기존 구독 목록을 반환 합니다.

*응답 본문 예:*

```json
{
  "subscriptions": [
      {
          "id": "<guid>", // purchased SaaS subscription ID
          "name": "Contoso Cloud Solution", // SaaS subscription name
          "publisherId": "contoso", // publisher ID
          "offerId": "offer1", // purchased offer ID
          "planId": "silver", // purchased plan ID
          "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
          "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
              "emailId": " test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
          "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
              "emailId": " test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
            "term": { // The period for which the subscription was purchased. 
                "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
                "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
                "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
          },
          "allowedCustomerOperations": [
              "Read", "Update", "Delete" 
          ], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
          "sessionMode": "None", // not relevant
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
          "isTest": false, // not relevant
          "sandboxType": "None", // not relevant
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
      },
// next SaaS subscription details, might be a different offer
{
          "id": "<guid1>", 
          "name": "Contoso Cloud Solution1", 
          "publisherId": "contoso", 
          "offerId": "offer2", 
          "planId": "gold", 
          "quantity": "", 
          "beneficiary": {
              "emailId": " test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
          "purchaser": { 
              "emailId": "purchase@csp.com ",
              "objectId": "<guid>",
              "tenantId": "<guid>",
               "pid": "<ID of the user>"
          },
            "term": { 
                "startDate": "2019-05-31",
                "endDate": "2020-04-30",
                "termUnit": "P1Y"
          },
          "allowedCustomerOperations": [
              "Read" 
          ], 
          "sessionMode": "None",
          "isFreeTrial": false,
          "isTest": false,
          "sandboxType": "None",
          "saasSubscriptionStatus": "Suspended"
      }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

이 게시자에 대해 구매한 SaaS 구독을 찾을 수 없는 경우 빈 응답 본문이 반환 됩니다.

코드: 403 사용할 수 없음. 권한 부여 토큰을 사용할 수 없거나, 잘못 되었거나, 만료 되었습니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md) 을 제대로 수행 하지 않는 증상입니다. 

코드: 500 내부 서버 오류입니다. API 호출을 다시 시도 합니다.  오류가 계속 되 면 [Microsoft 지원](https://partner.microsoft.com/support/v2/?stage=1)에 문의 하세요.

#### <a name="get-subscription"></a>구독 가져오기

게시자가 marketplace에 게시 한 SaaS 제품에 대해 지정 된 구매한 SaaS 구독을 검색 합니다. 모든 구독 목록을 가져오기 위해 API를 호출 하는 대신 ID를 사용 하 여 특정 SaaS 구독에 대해 사용 가능한 모든 정보를 가져오려면이 호출을 사용 합니다.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>가져오기 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   2018-08-31을 사용 합니다. |
| `subscriptionId`     |  구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 Resolve API를 사용 하 여 marketplace 권한 부여 토큰을 확인 한 후에 가져옵니다. |

*요청 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  `x-ms-correlationid` |  클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  `authorization`     | 이 API 호출을 수행 하는 게시자를 식별 하는 고유한 액세스 토큰입니다. 형식은 `"Bearer <access_token>"` [Azure AD 앱을 기반으로 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명 된 대로 게시자가 토큰 값을 검색 하는 경우입니다.  |

*응답 코드:*

Code: 200는 제공 된를 기반으로 하는 SaaS 구독에 대 한 세부 정보를 반환 합니다 `subscriptionId` .

*응답 본문 예:*

```json
{
        "id":<guid>, // purchased SaaS subscription ID
        "name":"Contoso Cloud Solution", // SaaS subscription name
         "publisherId": "contoso", // publisher ID
          "offerId": "offer1", // purchased offer ID
          "planId": "silver", // purchased plan ID
          "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
         "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
              "emailId": "test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
          "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
              "emailId": "test@test.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
        "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
        "sessionMode": "None", // not relevant
        "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
          "isTest": false, // not relevant
          "sandboxType": "None", // not relevant
          "saasSubscriptionStatus": " Subscribed " // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
          "term": { // the period for which the subscription was purchased 
            "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
            "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
            "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
        }
}
```

코드: 403 사용할 수 없음. 인증 토큰이 잘못 되었거나 만료 되었으며 제공 되지 않았습니다. 요청에서 권한 부여 토큰을 만드는 데 사용 된 것과 다른 Azure AD 앱 ID로 게시 된 제안의 SaaS 구독에 액세스 하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md) 을 제대로 수행 하지 않는 증상입니다. 

코드: 404를 찾을 수 없습니다.  지정 된을 사용 하는 SaaS 구독을 `subscriptionId` 찾을 수 없습니다.

코드: 500 내부 서버 오류입니다.  API 호출을 다시 시도 합니다.  오류가 계속 되 면 [Microsoft 지원](https://partner.microsoft.com/support/v2/?stage=1)에 문의 하세요.

#### <a name="list-available-plans"></a>사용 가능한 계획 나열

이 제안의 특정 구매에 의해 식별 된 SaaS 제품에 대 한 모든 계획을 검색 `subscriptionId` 합니다.  이 호출을 사용 하 여 SaaS 구독의 수혜자가 구독에 대해 업데이트할 수 있는 모든 개인 및 공용 계획의 목록을 가져올 수 있습니다.  반환 된 계획은 이미 구매한 계획과 동일한 지역에서 사용할 수 있습니다.

이 호출은 이미 구매한 고객 외에도 해당 고객에 게 제공 되는 계획의 목록을 반환 합니다.  이 목록은 게시자 사이트의 최종 고객에 게 제공 될 수 있습니다.  최종 고객은 구독 계획을 반환 된 목록의 계획 중 하나로 변경할 수 있습니다.  계획을 목록에 나열 되지 않은 항목으로 변경 하면 실패 합니다.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>가져오기 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31을 사용 합니다.  |
|  `subscriptionId`    |  구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 Resolve API를 사용 하 여 marketplace 권한 부여 토큰을 확인 한 후에 가져옵니다. |

*요청 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  `x-ms-correlationid`  |  클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  `authorization`     |  이 API 호출을 수행 하는 게시자를 식별 하는 고유한 액세스 토큰입니다.  형식은 `"Bearer <access_token>"` [Azure AD 앱을 기반으로 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명 된 대로 게시자가 토큰 값을 검색 하는 경우입니다.  |

*응답 코드:*

Code: 200 이미 구매한 계획을 포함 하 여 기존 SaaS 구독에 대해 사용 가능한 모든 계획의 목록을 반환 합니다.

응답 본문 예:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
        "isPrivate": true //true or false
    },
{ 
        "planId": "gold",
        "displayName": "Gold plan for Contoso", 
        "isPrivate": false //true or false
    }
]
}
```

`subscriptionId`를 찾을 수 없는 경우 빈 응답 본문이 반환 됩니다.

코드: 403 사용할 수 없음. 권한 부여 토큰이 잘못 되었거나 만료 되었거나 제공 되지 않았습니다.  요청은 권한 부여 토큰을 만드는 데 사용 된 것과 다른 Azure AD 앱 ID로 게시 된 제안의 SaaS 구독에 액세스 하려고 할 수 있습니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md) 을 제대로 수행 하지 않는 증상입니다. 

코드: 500 내부 서버 오류입니다.  API 호출을 다시 시도 합니다.  오류가 계속 되 면 [Microsoft 지원](https://partner.microsoft.com/support/v2/?stage=1)에 문의 하세요.

#### <a name="change-the-plan-on-the-subscription"></a>구독에 대 한 계획 변경

SaaS 구독에 대해 구매한 기존 계획을 새 요금제 (공용 또는 개인)로 업데이트 합니다.  Marketplace에서 구매한 SaaS 구독의 계획이 게시자 쪽에서 변경 될 때 게시자는이 API를 호출 해야 합니다.

이 API는 활성 구독에 대해서만 호출할 수 있습니다.  모든 계획은 다른 기존 계획 (공용 또는 개인)으로 변경할 수 있지만 그 자체로는 변경할 수 없습니다.  개인 계획의 경우 파트너 센터에서 고객의 테 넌 트를 계획 대상의 일부로 정의 해야 합니다.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>패치나 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31을 사용 합니다.  |
| `subscriptionId`     | 구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 Resolve API를 사용 하 여 marketplace 권한 부여 토큰을 확인 한 후에 가져옵니다. |

*요청 헤더:*
 
|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `x-ms-correlationid`  | 클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `authorization`     |  이 API 호출을 수행 하는 게시자를 식별 하는 고유한 액세스 토큰입니다.  형식은 `"Bearer <access_token>"` [Azure AD 앱을 기반으로 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명 된 대로 게시자가 토큰 값을 검색 하는 경우입니다. |

*요청 페이로드 예:*

```json
{
    "planId": "gold" // the ID of the new plan to be purchased
}
```

*응답 코드:*

코드: 202 변경 계획에 대 한 요청을 비동기적으로 수락 하 고 처리 했습니다.  파트너는 **작업 위치 URL** 을 폴링하여 변경 계획 요청의 성공 또는 실패를 확인할 것으로 예상 됩니다.  작업에 대해 실패, 성공 또는 충돌의 최종 상태가 수신 될 때까지 몇 초 마다 폴링을 수행 해야 합니다.  최종 작업 상태는 빠르게 반환 되어야 하지만 일부 경우에는 몇 분 정도 걸릴 수 있습니다.

또한이 파트너는 Marketplace 쪽에서 작업이 성공적으로 완료 될 준비가 되 면 webhook 알림을 받게 됩니다.  게시자가 게시자 쪽에서 계획을 변경 해야 합니다.

*응답 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  작업 상태를 가져오기 위한 URL입니다.  예들 들어 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`입니다. |

코드: 400 잘못 된 요청: 유효성 검사 오류입니다.

* 이 특정 SaaS 구독에 대해 새 계획이 없거나 사용할 수 없습니다.
* 동일한 계획으로 변경 하려고 합니다.
* SaaS 구독 상태는 구독 되지 않습니다.
* SaaS 구독에 대 한 업데이트 작업은에 포함 되어 있지 않습니다 `allowedCustomerOperations` .

코드: 403 사용할 수 없음. 권한 부여 토큰이 잘못 되었거나 만료 되었거나 제공 되지 않았습니다.  요청에서 권한 부여 토큰을 만드는 데 사용 된 것과 다른 Azure AD 앱 ID로 게시 된 제안의 SaaS 구독에 액세스 하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md) 을 제대로 수행 하지 않는 증상입니다.

코드: 404를 찾을 수 없습니다.  로 SaaS 구독을 `subscriptionId` 찾을 수 없습니다.

코드: 500 내부 서버 오류입니다.  API 호출을 다시 시도 합니다.  오류가 계속 되 면 [Microsoft 지원](https://partner.microsoft.com/support/v2/?stage=1)에 문의 하세요.

>[!NOTE]
>하나 이상의 계획 또는 수량을 한 번에 변경할 수 있습니다.

>[!Note]
>이 API는 최종 고객이 변경에 대 한 명시적 승인을 받은 후에만 호출할 수 있습니다.

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>SaaS 구독의 좌석 수량 변경

SaaS 구독에 대해 구매한 사용자의 수량을 업데이트 (증가 또는 감소) 합니다.  Marketplace에서 만든 SaaS 구독의 사용자 수가 게시자 쪽에서 변경 된 경우 게시자는이 API를 호출 해야 합니다.

좌석 수량은 현재 계획에서 허용 되는 것 보다 많을 수 없습니다.  이 경우 수량을 변경 하기 전에 계획을 변경 해야 합니다.

##### <a name="patchhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>패치`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31을 사용 합니다.  |
|  `subscriptionId`     | 구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 Resolve API를 사용 하 여 marketplace 권한 부여 토큰을 확인 한 후에 가져옵니다.  |

*요청 헤더:*
 
|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `x-ms-correlationid`  | 클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `authorization`     | 이 API 호출을 수행 하는 게시자를 식별 하는 고유한 액세스 토큰입니다.  형식은 `"Bearer <access_token>"` [Azure AD 앱을 기반으로 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명 된 대로 게시자가 토큰 값을 검색 하는 경우입니다.  |

*요청 페이로드 예:*

```json
{
    "quantity": 5 // the new amount of seats to be purchased
}
```

*응답 코드:*

코드: 202 수량 변경 요청이 비동기적으로 수락 되 고 처리 되었습니다. 파트너는 **작업 위치 URL** 을 폴링하여 변경 수량 요청의 성공 또는 실패를 확인 해야 합니다.  작업에 대해 실패, 성공 또는 충돌의 최종 상태가 수신 될 때까지 몇 초 마다 폴링을 수행 해야 합니다.  최종 작업 상태는 빠르게 반환 되어야 하지만 일부 경우에는 몇 분 정도 걸릴 수 있습니다.

또한이 파트너는 Marketplace 쪽에서 작업이 성공적으로 완료 될 준비가 되 면 webhook 알림을 받게 됩니다.  그런 다음 게시자가 게시자 쪽에서 수량을 변경 하도록 해야 합니다.

*응답 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  작업 상태를 가져오기 위한 리소스에 대 한 링크입니다.  예들 들어 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`입니다.  |

코드: 400 잘못 된 요청: 유효성 검사 오류입니다.

* 새 수량이 현재 계획 제한 보다 크거나 낮습니다.
* 새 수량이 없습니다.
* 동일한 수량으로 변경 하려고 합니다.
* SaaS 구독 상태는 구독 되지 않습니다.
* SaaS 구독에 대 한 업데이트 작업은에 포함 되어 있지 않습니다 `allowedCustomerOperations` .

코드: 403 사용할 수 없음.  권한 부여 토큰이 잘못 되었거나 만료 되었거나 제공 되지 않았습니다.  요청에서 현재 게시자에 속하지 않은 구독에 액세스 하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md) 을 제대로 수행 하지 않는 증상입니다. 

코드: 404를 찾을 수 없습니다.  로 SaaS 구독을 `subscriptionId` 찾을 수 없습니다.

코드: 500 내부 서버 오류입니다.  API 호출을 다시 시도 합니다.  오류가 계속 되 면 [Microsoft 지원](https://partner.microsoft.com/support/v2/?stage=1)에 문의 하세요.

>[!Note]
>한 번에 하나의 계획 또는 수량만 변경할 수 있습니다.

>[!Note]
>이 API는 최종 고객이 변경에 대 한 명시적 승인을 받은 후에만 호출할 수 있습니다.

#### <a name="cancel-a-subscription"></a>구독 취소

지정 된 SaaS 구독을 구독 취소 합니다.  게시자는이 API를 사용할 필요가 없으며 SaaS 구독을 취소 하는 고객을 marketplace로 이동 하는 것이 좋습니다.

게시자 측의 marketplace에서 구매한 SaaS 구독의 취소를 구현 하기로 결정 한 경우이 API를 호출 해야 합니다.  이 호출이 완료 된 후에 Microsoft 쪽에서 구독의 상태가 구독 취소 *됩니다.*

다음 유예 기간 내에 구독이 취소 되 면 고객에 게 요금이 청구 되지 않습니다.

* 활성화 후 월별 구독의 경우 24 시간
* 활성화 후 연도별 구독의 경우 14 일입니다.

위의 유예 기간 후에 구독이 취소 되 면 고객에 게 요금이 청구 됩니다.  취소가 성공 하면 고객이 Microsoft 쪽에서 SaaS 구독에 즉시 액세스할 수 없게 됩니다.

##### <a name="deletehttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>삭제`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31을 사용 합니다.  |
|  `subscriptionId`     | 구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 Resolve API를 사용 하 여 marketplace 권한 부여 토큰을 확인 한 후에 가져옵니다.  |

*요청 헤더:*
 
|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `x-ms-correlationid`  | 클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `authorization`     |  이 API 호출을 수행 하는 게시자를 식별 하는 고유한 액세스 토큰입니다.  형식은 `"Bearer <access_token>"` [Azure AD 앱을 기반으로 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명 된 대로 게시자가 토큰 값을 검색 하는 경우입니다. |

*응답 코드:*

코드: 202 구독 취소 요청을 수락 하 고 비동기적으로 처리 했습니다.  파트너는이 요청의 성공 또는 실패를 확인 하기 위해 **작업 위치 URL** 을 폴링할 것으로 예상 합니다.  작업에 대해 실패, 성공 또는 충돌의 최종 상태가 수신 될 때까지 몇 초 마다 폴링을 수행 해야 합니다.  최종 작업 상태는 빠르게 반환 되어야 하지만 일부 경우에는 몇 분 정도 걸릴 수 있습니다.

또한이 파트너는 Marketplace 쪽에서 작업이 성공적으로 완료 되 면 webhook 알림을 받습니다.  게시자가 게시자 쪽에서 구독을 취소 해야 합니다.

*응답 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  작업 상태를 가져오기 위한 리소스에 대 한 링크입니다.  예들 들어 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`입니다. |

코드: 400 잘못 된 요청입니다.  `allowedCustomerOperations`이 SaaS 구독의 삭제는 목록에 없습니다.

코드: 403 사용할 수 없음.  권한 부여 토큰이 잘못 되었거나 만료 되었거나 사용할 수 없습니다. 요청에서 권한 부여 토큰을 만드는 데 사용 된 것과 다른 Azure AD 앱 ID로 게시 된 제안의 SaaS 구독에 액세스 하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md) 을 제대로 수행 하지 않는 증상입니다.

코드: 404를 찾을 수 없습니다.  로 SaaS 구독을 `subscriptionId` 찾을 수 없습니다.

코드: 500 내부 서버 오류입니다. API 호출을 다시 시도 합니다.  오류가 계속 되 면 [Microsoft 지원](https://partner.microsoft.com/support/v2/?stage=1)에 문의 하세요.

### <a name="operations-apis"></a>작업 Api

#### <a name="list-outstanding-operations"></a>미해결 작업 나열 

지정 된 SaaS 구독에 대해 보류 중인 작업 목록을 가져옵니다.  반환 된 작업은 [작업 패치 API](#update-the-status-of-an-operation)를 호출 하 여 게시자가 승인 해야 합니다.

현재이 API 호출에 대 한 응답으로 **복원 작업만** 반환 됩니다.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>가져오기 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  2018-08-31을 사용 합니다.         |
|    `subscriptionId` | 구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 Resolve API를 사용 하 여 marketplace 권한 부여 토큰을 확인 한 후에 가져옵니다.  |

*요청 헤더:*
 
|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `x-ms-correlationid` |  클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `authorization`     |  형식은 `"Bearer <access_token>"` [Azure AD 앱을 기반으로 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명 된 대로 게시자가 토큰 값을 검색 하는 경우입니다.  |

*응답 코드:*

Code: 200 지정 된 SaaS 구독에서 보류 중인 복원 작업을 반환 합니다.

*응답 페이로드 예:*

```json
{"operations": [{
    "id": "<guid>",  //Operation ID, should be provided in the operations patch API call
    "activityId": "<guid>", //not relevant
    "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
    "offerId": "offer1",  // purchased offer ID
    "publisherId": "contoso",  
    "planId": "silver",  // purchased plan ID
    "quantity": "20", // purchased amount of seats, will be empty is not relevant
    "action": "Reinstate", 
    "timeStamp": "2018-12-01T00:00:00",  // UTC
    "status": "InProgress" // the only status that can be returned in this case
}]
}
```

보류 중인 복원 작업이 없는 경우 빈 json을 반환 합니다.

코드: 400 잘못 된 요청: 유효성 검사 오류입니다.

코드: 403 사용할 수 없음. 권한 부여 토큰이 잘못 되었거나 만료 되었거나 제공 되지 않았습니다.  요청에서 권한 부여 토큰을 만드는 데 사용 된 것과 다른 Azure AD 앱 ID로 게시 된 제안의 SaaS 구독에 액세스 하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md) 을 제대로 수행 하지 않는 증상입니다. 

코드: 404를 찾을 수 없습니다.  로 SaaS 구독을 `subscriptionId` 찾을 수 없습니다.

코드: 500 내부 서버 오류입니다. API 호출을 다시 시도 합니다.  오류가 계속 되 면 [Microsoft 지원](https://partner.microsoft.com/support/v2/?stage=1)에 문의 하세요.

#### <a name="get-operation-status"></a>작업 상태 가져오기

게시자가 지정 된 비동기 작업의 상태 (  **구독 취소**, **Changeplan**또는 **chang)** 를 추적할 수 있도록 합니다.

`operationId`이 API 호출에 대 한는 **작업 위치**에서 반환 된 값, 보류 중인 작업 API 호출 또는 `<id>` webhook 호출에서 받은 매개 변수 값에서 검색할 수 있습니다.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>가져오기 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  2018-08-31을 사용 합니다.  |
|  `subscriptionId`    |  구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 Resolve API를 사용 하 여 marketplace 권한 부여 토큰을 확인 한 후에 가져옵니다. |
|  `operationId`       |  검색 되는 작업의 고유 식별자입니다. |

*요청 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  `x-ms-correlationid` |  클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
|  `authorization`     |  이 API 호출을 수행 하는 게시자를 식별 하는 고유한 액세스 토큰입니다.  형식은 `"Bearer <access_token>"` [Azure AD 앱을 기반으로 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명 된 대로 게시자가 토큰 값을 검색 하는 경우입니다.  |

*응답 코드:*

Code: 200 지정 된 SaaS 작업에 대 한 세부 정보를 가져옵니다. 

*응답 페이로드 예:*

```json
Response body:
{
    "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
    "activityId": "<guid>", //not relevant
    "subscriptionId":"<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
    "offerId": "offer1", // purchased offer ID
    "publisherId": "contoso",  
    "planId": "silver", // purchased plan ID
    "quantity": "20", // purchased amount of seats 
    "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
    "timeStamp": "2018-12-01T00:00:00", // UTC
    "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeed, Conflict (new quantity / plan is the same as existing)

"errorStatusCode": "",
"errorMessage": ""
}
```

코드: 403 사용할 수 없음. 권한 부여 토큰이 잘못 되었거나 만료 되었거나 제공 되지 않았습니다.  요청에서 권한 부여 토큰을 만드는 데 사용 된 것과 다른 Azure AD 앱 ID로 게시 된 제안의 SaaS 구독에 액세스 하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md) 을 제대로 수행 하지 않는 증상입니다. 

코드: 404를 찾을 수 없습니다.  

* 을 (를) 사용 하 여 구독 `subscriptionId` 을 찾을 수 없습니다.
* 을 (를) 사용 하 여 작업 `operationId` 을 찾을 수 없습니다.

코드: 500 내부 서버 오류입니다.  API 호출을 다시 시도 합니다.  오류가 계속 되 면 [Microsoft 지원](https://partner.microsoft.com/support/v2/?stage=1)에 문의 하세요.

#### <a name="update-the-status-of-an-operation"></a>작업 상태 업데이트

보류 중인 작업의 상태를 업데이트 하 여 게시자 측의 작업 성공 또는 실패를 표시 합니다.

`operationId`이 API 호출에 대 한는 **작업 위치**에서 반환 된 값, 보류 중인 작업 API 호출 또는 `<id>` webhook 호출에서 받은 매개 변수 값에서 검색할 수 있습니다.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>패치나 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  2018-08-31을 사용 합니다.  |
|   `subscriptionId`   |  구매한 SaaS 구독의 고유 식별자입니다.  이 ID는 Resolve API를 사용 하 여 marketplace 권한 부여 토큰을 확인 한 후에 가져옵니다.  |
|   `operationId`      |  완료 되는 작업의 고유 식별자입니다. |

*요청 헤더:*

|  매개 변수         | 값             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|   `x-ms-correlationid` |  클라이언트의 작업에 대한 고유한 문자열 값입니다.  이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다.  이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
|  `authorization`     |  이 API 호출을 수행 하는 게시자를 식별 하는 고유한 액세스 토큰입니다.  형식은 `"Bearer <access_token>"` [Azure AD 앱을 기반으로 토큰 가져오기](./pc-saas-registration.md#get-the-token-with-an-http-post)에 설명 된 대로 게시자가 토큰 값을 검색 하는 경우입니다. |

*요청 페이로드 예:*

```json
{ 
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*응답 코드:*

Code: 200 파트너 측에서 작업 완료를 알리기 위한 호출입니다.  예를 들어이 응답은 게시자 쪽에서 사용자 또는 계획의 변경 완료에 신호를 보낼 수 있습니다.

코드: 403 사용할 수 없음.  권한 부여 토큰을 사용할 수 없거나, 잘못 되었거나, 만료 되었습니다. 요청에서 현재 게시자에 속하지 않은 구독에 액세스 하려고 할 수 있습니다.
사용할 수 없습니다.  권한 부여 토큰이 잘못 되었거나 만료 되었거나 제공 되지 않았습니다.  요청에서 권한 부여 토큰을 만드는 데 사용 된 것과 다른 Azure AD 앱 ID로 게시 된 제안의 SaaS 구독에 액세스 하려고 합니다.

이 오류는 종종 [SaaS 등록](pc-saas-registration.md) 을 제대로 수행 하지 않는 증상입니다.

코드: 404를 찾을 수 없습니다.

* 을 (를) 사용 하 여 구독 `subscriptionId` 을 찾을 수 없습니다.
* 을 (를) 사용 하 여 작업 `operationId` 을 찾을 수 없습니다.

Code: 409 충돌.  예를 들어 최신 업데이트가 이미 수행 되었습니다.

코드: 500 내부 서버 오류입니다.  API 호출을 다시 시도 합니다.  오류가 계속 되 면 [Microsoft 지원](https://partner.microsoft.com/support/v2/?stage=1)에 문의 하세요.

## <a name="implementing-a-webhook-on-the-saas-service"></a>SaaS 서비스에서 webhook 구현

파트너 센터에서 불가능 SaaS 제안을 만들 때 파트너는 HTTP 끝점으로 사용할 **연결 Webhook** URL을 제공 합니다.  이 webhook은 microsoft에서 POST HTTP 호출을 사용 하 여 Microsoft 쪽에서 발생 하는 다음 이벤트를 게시자에 게 알리기 위해 호출 됩니다.

* SaaS 구독이 구독 상태에 있는 경우:
    * ChangePlan 
    * 소유자
    * 일시 중지됨
    * 구독 취소
* SaaS 구독이 일시 중단 됨 상태에 있는 경우:
    * 복구
    * 구독 취소

SaaS 구독 상태를 Microsoft 쪽과 일관 되 게 유지 하려면 게시자가 SaaS 서비스에서 webhook를 구현 해야 합니다.  웹 후크 알림에 따라 작업을 수행 하기 전에 get 작업 API를 호출 하 여 webhook 호출 및 페이로드 데이터의 유효성을 검사 하 고 권한을 부여 하려면 SaaS 서비스가 필요 합니다.  웹 후크 호출이 처리 되는 즉시 게시자는 HTTP 200를 Microsoft에 반환 해야 합니다.  이 값은 게시자가 webhook 호출을 성공적으로 받았는지 승인 합니다.

>[!Note]
>웹 후크 url 서비스는 연중 무휴 실행 되어야 하며, 항상 Microsoft 시간에서 새로운 통화를 받을 준비가 되었습니다.  Microsoft에는 웹 후크 호출에 대 한 다시 시도 정책이 있지만 500 (8 시간 넘게 다시 시도), 게시자가 전화를 수락 하지 않고 응답을 반환 하는 경우 웹 후크를 알리는 작업이 Microsoft 쪽에서 결국 실패 하 게 됩니다.

*Webhook 페이로드 예:*

```json
// end customer changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": <guid>, // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": " 25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success  
}
```

```json
// end customer's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": <guid>, 
  "activityId": <guid>, 
  "subscriptionId": "guid", 
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold", 
  "quantity": " 20", 
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "In Progress" 
} 
```

## <a name="development-and-testing"></a>개발 및 테스트

개발 프로세스를 시작 하려면 게시자 쪽에서 더미 API 응답을 만드는 것이 좋습니다.  이러한 응답은이 문서에 제공 된 샘플 응답을 기반으로 할 수 있습니다.

게시자가 종단 간 테스트를 수행할 준비가 되 면 다음을 수행 합니다. 

* 제한 된 미리 보기 대상에 SaaS 제품을 게시 하 고 미리 보기 단계에 보관 합니다.
* 이 제품은 테스트 하는 동안 실제 요금 청구 비용을 트리거하지 않도록 0 가격의 요금제가 있어야 합니다.  또 다른 옵션은 0이 아닌 가격을 설정 하 고 24 시간 이내에 모든 테스트 구매를 취소 하는 것입니다. 
* 고객이 제품을 구매 하는 것 처럼 모든 흐름이 종단 간 호출 되는지 확인 합니다. 
* 파트너가 전체 구매 및 청구 흐름을 테스트 하려는 경우 $0 보다 높은 가격으로 제공 되는 제품을 사용 하 여이를 수행 합니다.  구매가 청구 되 고 송장이 생성 됩니다.

제품이 게시 되는 위치에 따라 Azure Portal 또는 Microsoft AppSource 사이트에서 구매 흐름을 트리거할 수 있습니다.

*변경 계획*, *수량 변경*및 *구독 취소* 동작은 게시자 쪽에서 테스트 됩니다.  Microsoft 쪽에서 *구독 취소* 는 Azure Portal 및 관리 센터 (Microsoft AppSource 구매가 관리 되는 포털)에서 트리거될 수 있습니다.  *변경 수량 및 요금제* 는 관리 센터 에서만 트리거될 수 있습니다.

## <a name="get-support"></a>지원 받기

게시자 지원 옵션 [은 파트너 센터에서 상업용 marketplace 프로그램 지원](support.md) 을 참조 하세요.


## <a name="next-steps"></a>다음 단계

상업적 marketplace에서 SaaS 제품에 대 한 추가 옵션은 [상업적 marketplace 계량 서비스 api](marketplace-metering-service-apis.md) 를 참조 하세요.

이 문서에서 설명 하는 Api를 기반으로 구축 된 [SAAS SDK](https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK) 를 검토 하 여 사용 합니다.
