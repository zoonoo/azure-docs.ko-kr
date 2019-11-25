---
title: 사용자 등록 및 제품 구독을 위임하는 방법
description: Azure API Management에서 사용자 등록 및 제품 구독을 타사에 위임하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: a69babdf2fffb4cb9d963f1806f3c85755e50294
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454348"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>사용자 등록 및 제품 구독을 위임하는 방법

Delegation allows you to use your existing website for handling developer sign in/sign up and subscription to products, as opposed to using the built-in functionality in the developer portal. It enables your website to own the user data and perform the validation of these steps in a custom way.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>개발자 로그인 및 등록 위임

To delegate developer, sign in and sign up to your existing website, you'll need to create a special delegation endpoint on your site. It needs to act as the entry-point for any such request initiated from the API Management developer portal.

최종 워크플로는 다음과 같습니다.

1. Developer clicks on the sign in or sign up link at the API Management developer portal
2. 브라우저가 위임 엔드포인트로 리디렉션됩니다.
3. Delegation endpoint in return redirects to or presents UI asking user to sign in or sign up
4. 성공하면 사용자가 처음 시작했던 API Management 개발자 포털 페이지로 다시 리디렉션됩니다.

먼저, 위임 엔드포인트를 통해 요청을 라우팅하도록 API Management를 설정하겠습니다. In the Azure portal, search for **Security** in your API Management resource and then click the **Delegation** item. Click the checkbox to enable 'Delegate sign in & sign up'.

![위임 페이지][api-management-delegation-signin-up]

* 특수한 위임 엔드포인트의 URL을 결정하고 **위임 엔드포인트 URL** 필드에 이 URL을 입력합니다. 
* 위임 인증 키 필드 내에서 요청이 실제로 Azure API Management에서 나오는지 확인하기 위해 사용자에게 제공된 서명을 계산하는 데 사용될 비밀을 입력합니다. **생성** 단추를 클릭하여 API Management에서 사용자를 위해 키를 임의로 생성할 수 있습니다.

이제 **위임 엔드포인트**를 만들어야 합니다. 몇 가지 작업을 수행해야 합니다.

1. 다음 형식의 요청을 받습니다.
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL of source page}&salt={string}&sig={string}*
   > 
   > 
   
    Query parameters for the sign in / sign up case:
   
   * **operation**: 위임 요청의 유형을 식별합니다. 이 경우 **SignIn**만 가능합니다.
   * **returnUrl**: the URL of the page where the user clicked on a sign in or sign up link
   * **salt**: 보안 해시를 계산하는 데 사용되는 특수 salt 문자열입니다.
   * **sig**: 자신의 계산된 해시와 비교하는 데 사용되는 계산된 보안 해시입니다.
2. 요청이 Azure API Management에서 들어오는지 확인합니다(선택 사항이지만 보안을 위해 상당히 권장됨).
   
   * **returnUrl** 및 **salt** 쿼리 매개 변수([아래 제공된 예제 코드])에 따라 문자열의 HMAC-SHA512 해시를 컴퓨팅합니다.
     
     > HMAC(**salt** + '\n' + **returnUrl**)
     > 
     > 
   * 위의 계산된 해시와 **sig** 쿼리 매개 변수 값을 비교합니다. 두 해시가 일치하면 다음 단계를 진행하고, 그렇지 않으면 요청을 거부합니다.
3. Verify that you are receiving a request for sign in/sign up: the **operation** query parameter will be set to "**SignIn**".
4. Present the user with UI to sign in or sign up
5. 사용자가 등록하고 있는 경우 API Management에서 사용자의 해당 계정을 만들어야 합니다. API Management REST API를 사용하여 [사용자를 만듭니다]. When doing so, ensure that you set the user ID to the same value as in your user store or to an ID that you can keep track of.
6. 사용자가 인증되면
   
   * API Management REST API를 통해 [SSO(Single-Sign-On) 토큰을 요청]합니다.
   * returnUrl 쿼리 매개 변수를 위의 API 호출에서 받은 SSO URL에 추가합니다.
     
     > 예: https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * 사용자를 위에서 생성한 URL로 리디렉션합니다.

**SignIn** 작업 외에도, 이전 단계를 수행하고 다음 작업 중 하나를 사용하여 계정 관리를 수행할 수 있습니다.

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

계정 관리 작업에 대한 다음 쿼리 매개 변수를 전달해야 합니다.

* **operation**: 위임 요청의 유형을 식별합니다(ChangePassword, ChangeProfile 또는 CloseAccount).
* **userId**: the user ID of the account to manage
* **salt**: 보안 해시를 계산하는 데 사용되는 특수 salt 문자열입니다.
* **sig**: 자신의 계산된 해시와 비교하는 데 사용되는 계산된 보안 해시입니다.

## <a name="delegate-product-subscription"> </a>제품 구독 위임
Delegating product subscription works similarly to delegating user sign in/-up. 최종 워크플로는 다음과 같습니다.

1. Developer selects a product in the API Management developer portal and clicks on the Subscribe button.
2. Browser is redirected to the delegation endpoint.
3. Delegation endpoint performs required product subscription steps. It's up to you to design the steps. They may include redirecting to another page to request billing information, asking additional questions, or simply storing the information and not requiring any user action.

이 기능을 사용하려면 **위임** 페이지에서 **제품 구독 위임**을 클릭합니다.

Next, ensure the delegation endpoint does the following actions:

1. 다음 형식의 요청을 받습니다.
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribe to}&userId={user making request}&salt={string}&sig={string}*
   >
   
    제품 구독 케이스에 대한 쿼리 매개 변수:
   
   * **operation**: 위임 요청 유형을 식별합니다. 제품 구독 요청의 경우 유효한 옵션은 다음과 같습니다.
     * "Subscribe": 사용자가 제공된 ID를 사용하여 지정된 제품을 구독하도록 하는 요청입니다(아래 참조).
     * "Unsubscribe": 제품에 대한 사용자 구독을 취소하는 요청입니다.
     * "Renew": 구독을 갱신하는 요청입니다(예: 만료일이 다가오는 경우).
   * **productId**: 사용자가 구독을 요청한 제품의 ID입니다.
   * **subscriptionId**: on *Unsubscribe* and *Renew* - the ID of the product subscription
   * **userId**: the ID of the user the request is made for
   * **salt**: 보안 해시를 계산하는 데 사용되는 특수 salt 문자열입니다.
   * **sig**: 자신의 계산된 해시와 비교하는 데 사용되는 계산된 보안 해시입니다.

2. 요청이 Azure API Management에서 들어오는지 확인합니다(선택 사항이지만 보안을 위해 상당히 권장됨).
   
   * Compute an HMAC-SHA512 of a string based on the **productId**, **userId**, and **salt** query parameters:
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * 위의 계산된 해시와 **sig** 쿼리 매개 변수 값을 비교합니다. 두 해시가 일치하면 다음 단계를 진행하고, 그렇지 않으면 요청을 거부합니다.
3. Process product subscription based on the type of operation requested in **operation** - for example, billing, further questions, etc.
4. On successfully subscribing the user to the product on your side, subscribe the user to the API Management product by [calling the REST API for subscriptions].

## <a name="delegate-example-code"> </a> 예제 코드

These code samples show how to:

* Take the *delegation validation key*, which is set in the Delegation screen of the publisher portal
* Create an HMAC, which is then used to validate the signature, proving the validity of the passed returnUrl.

약간만 수정하면 동일한 코드를 productId 및 userId에도 사용할 수 있습니다.

**returnUrl의 해시를 생성하는 C# 코드**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**returnUrl의 해시를 생성하는 NodeJS 코드**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> You need to [republish the developer portal](api-management-howto-developer-portal-customize.md#publish) for the delegation changes to take effect.

## <a name="next-steps"></a>다음 단계
위임에 대한 자세한 내용은 다음 비디오를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[SSO(Single-Sign-On) 토큰을 요청]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/User/GenerateSsoUrl
[사용자를 만듭니다]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user/createorupdate
[calling the REST API for subscriptions]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate
[Next steps]: #next-steps
[아래 제공된 예제 코드]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
