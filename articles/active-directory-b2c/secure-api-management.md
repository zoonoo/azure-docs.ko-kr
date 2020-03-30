---
title: Azure Active Directory B2C를 사용하여 Azure API 관리 API 보안
description: Azure Active Directory B2C에서 발급한 액세스 토큰을 사용하여 Azure API 관리 API 끝점을 보호하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 00938d831e70289b24acb599b81016aa6e564d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186933"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Azure AD B2C를 통해 Azure API 관리 API 보안

Azure Active Directory B2C(Azure AD B2C)로 인증된 클라이언트로 Azure API 관리(APIM) API에 대한 액세스를 제한하는 방법에 대해 알아봅니다. 이 문서의 단계에 따라 APIM에서 유효한 Azure AD B2C 발급 액세스 토큰을 포함하는 요청만 액세스를 제한하는 인바운드 정책을 만들고 테스트합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 계속하기 전에 다음 리소스가 필요합니다.

* [Azure AD B2C 테넌트](tutorial-create-tenant.md)
* 테넌트에서 [등록된 애플리케이션](tutorial-register-applications.md)
* 테넌트에서 [만든 사용자 흐름](tutorial-create-user-flows.md)
* Azure API 관리에서 [게시된 API](../api-management/import-and-publish.md)
* 보안 액세스를 테스트하는 [우체부(선택](https://www.getpostman.com/) 사항)

## <a name="get-azure-ad-b2c-application-id"></a>Azure AD B2C 응용 프로그램 ID 받기

Azure AD B2C를 사용하여 Azure API 관리에서 API를 보호하는 경우 APIM에서 만드는 [인바운드 정책에](../api-management/api-management-howto-policies.md) 대해 몇 가지 값이 필요합니다. 먼저 Azure AD B2C 테넌트에서 이전에 만든 응용 프로그램의 응용 프로그램 ID를 기록합니다. 필수 구성 프로그램에서 만든 응용 프로그램을 사용하는 경우 *webbapp1에*응용 프로그램 ID를 사용합니다.

현재 **응용 프로그램** 환경 또는 새로운 통합 **앱 등록(미리 보기)** 환경을 사용하여 응용 프로그램 ID를 얻을 수 있습니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[애플리케이션](#tab/applications/)

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **관리**에서 **응용 프로그램을 선택합니다.**
1. *webapp1* 또는 이전에 만든 다른 응용 프로그램에 대한 **응용 프로그램 ID** 열에 값을 기록합니다.

#### <a name="app-registrations-preview"></a>[앱 등록(미리 보기)](#tab/app-reg-preview/)

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **앱 등록(미리 보기)을**선택한 다음 소유 응용 **프로그램 탭을 선택합니다.**
1. *webapp1* 또는 이전에 만든 다른 응용 프로그램에 대한 **응용 프로그램(클라이언트) ID** 열에 값을 기록합니다.

* * *

## <a name="get-token-issuer-endpoint"></a>토큰 발급자 끝점 받기

다음으로 Azure AD B2C 사용자 흐름 중 하나에 대해 잘 알려진 구성 URL을 가져옵니다. 또한 Azure API 관리에서 지원하려는 토큰 발급자 끝점 URI가 필요합니다.

1. [Azure 포털에서](https://portal.azure.com)Azure AD B2C 테넌트로 이동합니다.
1. **정책에서** **사용자 흐름(정책)을 선택합니다.**
1. 기존 정책을 선택합니다(예: *B2C_1_signupsignin1)* **사용자 흐름 실행을**선택합니다.
1. 페이지 상단 에 있는 **실행 사용자 흐름** 제목 아래에 표시되는 하이퍼링크에 URL을 기록합니다. 이 URL은 사용자 흐름에 대해 잘 알려진 OpenID Connect 검색 끝점이며 Azure API 관리에서 인바운드 정책을 구성할 때 다음 섹션에서 사용합니다.

    ![Azure 포털의 지금 실행 페이지에서 잘 알려진 URI 하이퍼링크](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. 하이퍼링크를 선택하여 OpenID Connect 잘 알려진 구성 페이지를 찾아봅슬합니다.
1. 브라우저에서 열리는 페이지에서 `issuer` 다음과 같은 값을 기록합니다.

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Azure API 관리에서 API를 구성할 때 다음 섹션에서 이 값을 사용합니다.

이제 다음 섹션에서 사용할 URL두 개(OpenID Connect 잘 알려진 구성 끝점 URL 및 발급자 URI)가 기록되어야 합니다. 예를 들어:

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Azure API 관리에서 인바운드 정책 구성

이제 API 호출의 유효성을 검사하는 Azure API 관리에서 인바운드 정책을 추가할 준비가 되었습니다. 액세스 토큰에서 대상 및 발급자를 확인하는 [JWT 유효성 검사](../api-management/api-management-access-restriction-policies.md#ValidateJWT) 정책을 추가하면 유효한 토큰이 있는 API 호출만 허용되도록 할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure API Management 인스턴스를 찾습니다.
1. **API**를 선택합니다.
1. Azure AD B2C로 보호할 API를 선택합니다.
1. **디자인** 탭을 선택합니다.
1. **인바운드 처리에서** ** \< / ** 정책 코드 편집기를 열려면 선택합니다.
1. 정책 안에 `<validate-jwt>` 다음 `<inbound>` 태그를 배치합니다.

    1. 정책의 `url` 잘 알려진 `<openid-config>` 구성 URL로 요소의 값을 업데이트합니다.
    1. B2C 테넌트에서 이전에 만든 응용 프로그램의 응용 프로그램 ID(예: `<audience>` *webapp1)로*요소를 업데이트합니다.
    1. 이전에 `<issuer>` 기록한 토큰 발급자 끝점으로 요소를 업데이트합니다.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>보안 API 액세스 유효성 검사

인증된 호출자만 API에 액세스할 수 있도록 [Postman](https://www.getpostman.com/)을 사용하여 API를 호출하여 Azure API 관리 구성의 유효성을 검사할 수 있습니다.

API를 호출하려면 Azure AD B2C에서 발급한 액세스 토큰과 APIM 구독 키가 모두 필요합니다.

### <a name="get-an-access-token"></a>액세스 토큰 가져오기

먼저 Postman의 헤더에서 사용하려면 Azure AD `Authorization` B2C에서 발급한 토큰이 필요합니다. 필수 구성 조건 중 하나로 만들어야 하는 등록/로그인 사용자 흐름의 **지금 실행** 기능을 사용하여 하나를 얻을 수 있습니다.

1. [Azure 포털에서](https://portal.azure.com)Azure AD B2C 테넌트로 이동합니다.
1. **정책에서** **사용자 흐름(정책)을 선택합니다.**
1. *B2C_1_signupsignin1*등 기존 등록/로그인 사용자 흐름을 선택합니다.
1. **응용 프로그램의**경우 *웹 앱1을*선택합니다.
1. **회신 URL의**경우 를 선택합니다. `https://jwt.ms`
1. **사용자 흐름 실행을 선택합니다.**

    ![Azure 포털에서 사용자 흐름에 등록하려면 사용자 흐름 페이지 실행](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. 로그인 프로세스를 완료합니다. `https://jwt.ms`로 리디렉션되어야 합니다.
1. 브라우저에 표시되는 인코딩된 토큰 값을 기록합니다. Postman의 권한 부여 헤더에 이 토큰 값을 사용합니다.

    ![jwt.ms 에 표시되는 인코딩된 토큰 값](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>API 구독 키 받기

게시된 API를 호출하는 클라이언트 응용 프로그램(이 경우 Postman)은 HTTP 요청에 유효한 API 관리 구독 키를 API에 포함해야 합니다. 우체부 HTTP 요청에 포함할 구독 키를 얻으려면 다음을 수행하십시오.

1. Azure [포털에서](https://portal.azure.com)Azure API 관리 서비스 인스턴스로 검색합니다.
1. **구독을 선택합니다.**
1. 제품의 타원을 선택: **무제한,** 다음 **표시/숨기기 키를**선택합니다.
1. 제품의 **기본 키를** 기록합니다. Postman의 `Ocp-Apim-Subscription-Key` HTTP 요청에서 헤더에 이 키를 사용합니다.

![Azure 포털에서 선택된 키 표시/숨기기 권한이 있는 구독 키 페이지](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>보안 API 호출 테스트

액세스 토큰 및 APIM 구독 키가 기록되면 API에 대한 보안 액세스를 올바르게 구성했는지 여부를 테스트할 준비가 되었습니다.

1. `GET` [우체부에서](https://www.getpostman.com/)새 요청을 만듭니다. 요청 URL의 경우 게시한 API의 끝점을 필수 구성 조건 중 하나로 지정합니다. 예를 들어:

    `https://contosoapim.azure-api.net/conference/speakers`

1. 다음으로 다음 헤더를 추가합니다.

    | Key | 값 |
    | --- | ----- |
    | `Authorization` | 이전에 기록한 인코딩된 토큰 값("Bearer" 다음 공백 포함)이 접두사에 `Bearer ` 붙어 있습니다. |
    | `Ocp-Apim-Subscription-Key` | 이전에 기록한 APIM 구독 키 |

    **GET** 요청 URL 및 **헤더는** 다음과 유사하게 표시되어야 합니다.

    ![GET 요청 URL 및 헤더를 보여주는 우체부 UI](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Postman에서 **보내기** 단추를 선택하여 요청을 실행합니다. 모든 것을 올바르게 구성한 경우 회의 연사 모음과 함께 JSON 응답을 표시해야 합니다(여기에 잘린 그림 참조).

    ```JSON
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>안전하지 않은 API 호출 테스트

성공적인 요청을 한 후 실패 사례를 테스트하여 *잘못된* 토큰을 사용하여 API에 대한 호출이 예상대로 거부되었는지 확인합니다. 테스트를 수행하는 한 가지 방법은 토큰 값에 몇 개의 문자를 추가하거나 `GET` 변경한 다음 이전과 동일한 요청을 실행하는 것입니다.

1. 토큰 값에 여러 문자를 추가하여 잘못된 토큰을 시뮬레이션합니다. 예를 들어 토큰 값에 "잘못된"을 추가합니다.

    ![토큰에 추가된 잘못된 것을 보여주는 우체부 UI의 헤더 섹션](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. 요청을 실행하려면 **보내기** 단추를 선택합니다. 유효하지 않은 토큰의 경우 예상되는 `401` 결과는 승인되지 않은 상태 코드입니다.

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

`401` 상태 코드가 표시되면 Azure AD B2C에서 발급한 유효한 액세스 토큰을 가진 호출자만 Azure API 관리 API에 대한 성공적인 요청을 수행할 수 있는지 확인했습니다.

## <a name="support-multiple-applications-and-issuers"></a>여러 응용 프로그램 및 발급자 지원

여러 응용 프로그램은 일반적으로 단일 REST API와 상호 작용합니다. API가 여러 응용 프로그램에 대한 토큰을 수락하도록 하려면 APIM `<audiences>` 인바운드 정책의 요소에 해당 응용 프로그램 보안을 추가합니다.

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

마찬가지로 여러 토큰 발급자를 지원하려면 APIM 인바운드 `<issuers>` 정책의 요소에 끝점 URI를 추가합니다.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>b2clogin.com 마이그레이션

레거시 `login.microsoftonline.com` 끝점에서 발급된 토큰의 유효성을 검사하는 APIM API가 있는 경우 [API와 이를](b2clogin.md)호출하는 응용 프로그램을 마이그레이션하여 b2clogin.com.

이 일반 프로세스를 수행하여 단계적 마이그레이션을 수행할 수 있습니다.

1. b2clogin.com login.microsoftonline.com 발급된 토큰에 대한 APIM 인바운드 정책에 지원을 추가합니다.
1. 응용 프로그램을 한 번에 하나씩 업데이트하여 b2clogin.com 끝점에서 토큰을 가져옵니다.
1. 모든 응용 프로그램이 b2clogin.com 토큰을 올바르게 가져오면 API에서 login.microsoftonline.com 발급된 토큰에 대한 지원을 제거합니다.

다음 예제 APIM 인바운드 정책은 b2clogin.com login.microsoftonline.com 발행된 토큰을 수락하는 방법을 보여 줍니다. 또한 두 응용 프로그램의 API 요청을 지원합니다.

```XML
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>다음 단계

Azure API 관리 정책에 대한 자세한 내용은 [APIM 정책 참조 인덱스를](../api-management/api-management-policies.md)참조하십시오.

OWIN 기반 웹 API 마이그레이션에 대한 정보를 찾을 수 있으며 해당 응용 b2clogin.com 프로그램은 [OWIN 기반 웹 API를 마이그레이션하여 b2clogin.com.](multiple-token-endpoints.md)
