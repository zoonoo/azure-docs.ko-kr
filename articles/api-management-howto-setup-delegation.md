<properties pageTitle="How to delegate user registration and product subscription" metaKeywords="" description="Learn how to delegate user registration and product subscription to a third party in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to delegate user registration and product subscription in Azure API Management" authors="antonba" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="antonba"></tags>

# 사용자 등록 및 제품 구독을 위임하는 방법

위임을 통해 개발자 로그인/등록 및 제품 구독을 처리하는 데 개발자 포털의 기본 제공된 기능이 아닌 기존 웹 사이트를 사용할 수 있습니다. 따라서 웹 사이트에서 사용자 데이터를 소유하고 이러한 단계에 대한 유효성 검사를 편리한 방식으로 수행할 수 있습니다.


## 항목 내용

- [개발자 로그인 및 등록 위임][개발자 로그인 및 등록 위임]
- [제품 구독 위임][제품 구독 위임]

## <a name="delegate-signin-up"></a> 개발자 로그인 및 등록 위임

개발자 로그인 및 등록을 기존 웹 사이트에 위임하려면 API 관리 개발자 포털에서 시작된 이러한 요청에 대한 진입점 역할을 하는 특수한 위임 끝점을 사이트에 만들어야 합니다.

최종 워크플로는 다음과 같습니다.

1. 개발자가 API 관리 개발자 포털의 로그인 또는 등록 링크를 클릭합니다.
2. 브라우저가 위임 끝점으로 리디렉션됩니다.
3. 위임 끝점이 리디렉션되거나 사용자에게 로그인 또는 등록을 요청하는 UI를 표시합니다.
4. 성공하면 사용자가 처음 시작했던 API 관리 개발자 포털 페이지로 다시 리디렉션됩니다.


먼저, 위임 끝점을 통해 요청을 라우팅하도록 API 관리를 설정하겠습니다. API 관리 게시자 포털에서, 왼쪽 메뉴의 **개발자 포털** 제목에 있는 **위임**을 클릭한 다음 **로그인 및 등록 위임**을 클릭합니다.

![위임 페이지][위임 페이지]

- 특수한 위임 끝점의 URL을 결정하고 **위임 끝점 URL** 필드에 이 URL을 입력합니다.

- **위임 인증 키** 필드에서 요청이 Azure API 관리에서 들어오는지 확인하기 위해 사용자에게 제공된 서명을 계산하는 데 사용되는 암호를 입력합니다.

이제 **위임 끝점**을 만들어야 합니다. 몇 가지 작업을 수행해야 합니다.

1.  다음 형식의 요청을 받습니다.

	> *http://www.yourwebsite.com/apimdelegation?operation=SignIn&redirectUrl={URL of source page}&salt={string}&sid={string}*

    로그인/등록 케이스의 쿼리 매개 변수:
    - **operation**: 위임 요청이 무엇인지 식별합니다. 이 경우 "SignIn"이 될 수 있습니다.
    - **redirectUrl**: 사용자가 로그인 또는 등록 링크를 클릭하는 페이지의 URL입니다.
    - **salt**: 보안 해시를 계산하는 데 사용되는 특수한 salt 문자열입니다.
    - **sig**: 사용자의 계산된 해시와 비교하는 데 사용할 계산된 보안 해시입니다.

2. 요청이 Azure API 관리에서 들어오는지 확인합니다(선택 사항이지만 보안을 위해 상당히 권장됨).

    - **redirectUrl** 및 **salt** 쿼리 매개 변수를 기반으로 하여 문자열의 HMAC-SHA512 해시를 다음과 같이 계산합니다.
        > HMAC(**redirectUrl** + '\\n' + **salt**)

    -   위의 계산된 해시와 **sig** 쿼리 매개 변수 값을 비교합니다. 두 해시가 일치하면 다음 단계를 진행하고, 그렇지 않으면 요청을 거부합니다.

3. 로그인/등록을 위한 요청을 받고 있는지 확인합니다. **operation** 쿼리 매개 변수가 "**SignIn**"으로 설정됩니다.

4. 로그인 또는 등록에 대한 UI를 사용자에게 표시합니다.

5. 사용자가 등록하고 있는 경우 API 관리에서 사용자의 해당 계정을 만들어야 합니다. API 관리 REST API로 [사용자를 만듭니다][사용자를 만듭니다]. 이때 사용자 ID를 사용자 저장소에 있는 것과 동일한 사용자 ID 또는 추적할 수 있는 사용자 ID로 설정해야 합니다.

6. 사용자가 인증되면

	* API 관리 REST API를 통해 [SSO(Single-Sign-On) 토큰을 요청][SSO(Single-Sign-On) 토큰을 요청]합니다.

	* returnUrl 쿼리 매개 변수를 위의 API 호출에서 받은 SSO URL에 추가합니다.
        >예: <https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url>

	* 사용자를 위에서 생성한 URL로 리디렉션합니다.


## <a name="delegate-product-subscription"></a> 제품 구독 위임

제품 구독 위임은 사용자 로그인/등록과 유사하게 작동합니다. 최종 워크플로는 다음과 같습니다.

1. 개발자는 API 관리 개발자 포털에서 제품을 선택하고 구독 단추를 클릭합니다.
2. 브라우저가 위임 끝점으로 리디렉션됩니다.
3. 위임 끝점에서 필요한 제품 구독 단계를 수행합니다. 이 단계는 사용자가 수행하며, 여기에는 청구 정보를 요청하는 다른 페이지로 리디렉션, 추가 질문 요청 또는 사용자 작업 없이 정보 저장 등이 포함될 수 있습니다.
4. 성공하면 사용자가 처음 시작했던 API 관리 개발자 포털 페이지로 다시 리디렉션됩니다.

이 기능을 사용하려면 **위임** 페이지에서 **제품 구독 위임**을 클릭합니다.

그런 다음 위임 끝점에서 다음 작업을 수행하는지 확인합니다.


1. 다음 형식의 요청을 받습니다.

    >*<http://www.yourwebsite.com/apimdelegation?operation>={operation}&productId={구독할 제품}&userId={요청하는 사용자}&salt={문자열}&sid={문자열}*

    제품 구독 케이스에 대한 쿼리 매개 변수:
    - **operation**: 위임 요청 유형이 무엇인지 식별합니다. 제품 구독 요청의 경우 유효한 옵션은 다음과 같습니다.
		- "Subscribe": 지정된 ID를 사용하여 사용자가 지정된 제품을 구독하도록 요청합니다(아래 참조).
        -   "Unsubscribe": 제품에 대한 사용자 구독을 취소하는 요청입니다.
        -   "Renew": 구독을 갱신하는 요청입니다(예: 만료일이 다가오는 경우).
	- **productId**: 사용자가 구독을 요청한 제품의 ID입니다.
	- **userId**: 요청이 생성된 사용자의 ID입니다.
	- **salt**: 보안 해시를 계산하는 데 사용되는 특수한 salt 문자열입니다.
	- **sig**: 사용자의 계산된 해시와 비교하는 데 사용할 계산된 보안 해시입니다.


2. 요청이 Azure API 관리에서 들어오는지 확인합니다(선택 사항이지만 보안을 위해 상당히 권장됨).

	* **productId**, **userId** 및 **salt** 쿼리 매개 변수를 기반으로 하여 문자열의 HMAC-SHA512를 다음과 같이 계산합니다.
        >HMAC(**productId** + '\n' + **userId** + '\n' + **salt**)

	* 위의 계산된 해시와 **sig** 쿼리 매개 변수 값을 비교합니다. 두 해시가 일치하면 다음 단계를 진행하고, 그렇지 않으면 요청을 거부합니다.

3. **operation**에 요청된 작업의 유형(예: 청구, 추가 질문 등)을 기반으로 하여 제품 구독 처리를 수행합니다.

4. 사용자의 제품 구독을 마치면 [제품 구독을 위해 REST API를 호출][제품 구독을 위해 REST API를 호출]하여 사용자가 API 관리 제품도 구독하도록 합니다.

5. 요청을 받을 때 지정된 **redirectUrl**로 사용자를 다시 리디렉션합니다.



[개발자 로그인 및 등록 위임]: #delegate-signin-up
[제품 구독 위임]: #delegate-product-subscription
[위임 페이지]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
[사용자를 만듭니다]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[SSO(Single-Sign-On) 토큰을 요청]: http://go.microsoft.com/fwlink/?LinkId=507409
[제품 구독을 위해 REST API를 호출]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
