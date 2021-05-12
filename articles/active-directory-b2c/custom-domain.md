---
title: Azure AD B2C 사용자 지정 도메인 사용
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 리디렉션 URL에 사용자 지정 도메인을 사용하도록 설정하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2de419885938b27ebce4a934db5ef966965b3dbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580167"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Azure Active Directory B2C에 사용자 지정 도메인 사용

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

이 문서에서는 Azure AD B2C(Azure Active Directory B2C)의 리디렉션 Url에 사용자 지정 도메인을 사용하도록 설정하는 방법을 설명합니다. 애플리케이션에 사용자 지정 도메인을 사용하면 보다 원활한 사용자 환경을 제공할 수 있습니다. 사용자의 관점에서 이들은 로그인 과정에서 Azure AD B2C 기본 도메인인 *<tenant-name>.b2clogin.com* 으로 리디렉션되지 않고 도메인에 그대로 있습니다.

![사용자 지정 도메인 사용자 환경](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>사용자 지정 도메인 개요

[Azure Front Door](https://azure.microsoft.com/services/frontdoor/)를 사용하여 Azure AD B2C에서 사용자 지정 도메인을 사용하도록 설정할 수 있습니다. Azure Front Door는 Microsoft 글로벌 경계 네트워크를 사용하여 빠르고, 안전하고, 스케일링 성능이 뛰어난 웹 애플리케이션을 만들기 위한 확장 가능한 글로벌 진입점입니다. Azure Front Door 뒤에 Azure AD B2C 콘텐츠를 렌더링한 다음, 애플리케이션 URL의 사용자 지정 도메인을 통해 콘텐츠를 제공하도록 Azure Front Door에서 옵션을 구성할 수 있습니다.

다음 다이어그램은 Azure Front Door 통합을 보여 줍니다.

1. 애플리케이션에서 사용자는 로그인 단추를 클릭하여 Azure AD B2C 로그인 페이지로 이동합니다. 이 페이지에서는 사용자 지정 도메인 이름을 지정합니다.
1. 웹 브라우저에서는 Azure Front Door IP 주소에 대한 사용자 지정 도메인 이름을 확인합니다. DNS를 확인하는 동안 사용자 지정 도메인 이름이 포함된 CNAME(정식 이름) 레코드는 Front Door 기본 프런트 엔드 호스트(예: `contoso.azurefd.net`)를 가리킵니다. 
1. 사용자 지정 도메인(예: `login.contoso.com`)으로 가는 트래픽은 지정된 Front Door 기본 프런트 엔드 호스트(`contoso.azurefd.net`)로 라우팅됩니다.
1. Azure Front Door가 Azure AD B2C `<tenant-name>.b2clogin.com` 기본 도메인을 사용하여 Azure AD B2C 콘텐츠를 호출합니다. Azure AD B2C 엔드포인트에 대한 요청은 원래 사용자 지정 도메인 이름이 있는 사용자 지정 HTTP 헤더를 포함합니다.
1. Azure AD B2C는 관련 콘텐츠와 원래 사용자 지정 도메인을 표시하여 요청에 응답합니다.

![사용자 지정 도메인 네트워킹 다이어그램](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> 브라우저에서 Azure Front Door로의 연결은 항상 IPv6 대신 IPv4를 사용해야 합니다.

사용자 지정 도메인을 사용하는 경우 다음을 고려합니다.

- 여러 사용자 지정 도메인을 설정할 수 있습니다. 지원되는 사용자 지정 도메인의 최대 개수에 대한 자세한 내용은 Azure AD B2C의 경우 [Azure AD 서비스 제한 및 제한 사항](../active-directory/enterprise-users/directory-service-limits-restrictions.md)을, Azure Front Door의 경우 [Azure 구독 및 서비스 제한, 할당량, 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits)을 참조하세요.
- Azure Front Door는 별도의 Azure 서비스이므로 추가 요금이 발생합니다. 자세한 내용은 [Front Door 가격 책정](https://azure.microsoft.com/pricing/details/frontdoor)을 참조하세요.
- Azure Front Door [웹 애플리케이션 방화벽](../web-application-firewall/afds/afds-overview.md)을 사용하려면 Azure AD B2C 사용자 흐름에서 방화벽 구성 및 규칙이 올바르게 작동하는지 확인해야 합니다.
- 사용자 지정 도메인을 구성한 후에도 사용자 지정 정책을 사용하고 [액세스를 차단](#block-access-to-the-default-domain-name)하지 않는 한 Azure AD B2C 기본 도메인 이름인 *<tenant-name>.b2clogin.com* 을 계속 사용할 수 있습니다.
- 여러 애플리케이션이 있는 경우 브라우저에서 현재 사용 중인 도메인 이름으로 Azure AD B2C 세션을 저장하므로 사용자 지정 도메인으로 애플리케이션을 모두 마이그레이션합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="add-a-custom-domain-name-to-your-tenant"></a>테넌트에 사용자 지정 도메인 이름 추가

[Azure AD에서 사용자 지정 도메인을 추가하고 유효성을 검사](../active-directory/fundamentals/add-custom-domain.md)하는 방법에 대한 지침을 따릅니다. 도메인을 확인한 후에는 만든 DNS TXT 레코드를 삭제합니다.

> [!IMPORTANT]
> 관련 단계를 수행하려면 **Azure AD B2C** 테넌트에 로그인하고 **Azure Active Directory** 서비스를 선택해야 합니다.

사용하려는 각 하위 도메인을 확인합니다. 최상위 도메인만 확인하는 것으로는 충분하지 않습니다. 예를 들어 *login.contoso.com* 및 *account.contoso.com* 을 사용하여 로그인하려면 최상위 도메인인 *contoso.com* 뿐만 아니라 하위 도메인을 모두 확인해야 합니다.  

## <a name="create-a-new-azure-front-door-instance"></a>Azure Front Door 인스턴스 만들기

프런트 엔드 호스트 및 회람 규칙에 대한 기본 설정을 사용하여 [애플리케이션에 대한 Front Door를 만드는](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application) 단계를 수행합니다. 

> [!IMPORTANT]
> 해당 단계에서 1단계로 Azure Portal에 로그인한 후 **디렉터리 + 구독** 을 선택하고 Azure Front Door에 사용할 Azure 구독이 있는 디렉터리를 선택합니다. 이 디렉터리가 Azure AD B2C 테넌트를 포함하는 디렉터리가 되어서는 ‘안 됩니다’. 

**백 엔드 추가** 단계에서 다음 설정을 사용합니다.

* **백 엔드 호스트 유형** 으로 **사용자 지정 호스트** 를 선택합니다.  
* **백 엔드 호스트 이름** 으로 Azure AD B2C 엔드포인트의 호스트 이름(<tenant-name>.b2clogin.com)을 선택합니다. 예를 들어 contoso.b2clogin.com을 입력합니다. 
* **백 엔드 호스트 헤더** 로 **백 엔드 호스트 이름** 으로 선택한 것과 동일한 값을 선택합니다.

![백 엔드 추가](./media/custom-domain/add-a-backend.png)

백 엔드 **풀** 에 **백 엔드** 를 추가한 후 **상태 프로브** 를 사용하지 않습니다.

![백 엔드 풀 추가](./media/custom-domain/add-a-backend-pool.png)

## <a name="set-up-your-custom-domain-on-azure-front-door"></a>Azure Front Door에 사용자 지정 도메인 설정

단계에 따라 [사용자 지정 도메인을 Front Door에 추가](../frontdoor/front-door-custom-domain.md)합니다. `CNAME`사용자 지정 도메인에 대한 레코드를 만들 때 이전에 [Azure AD에 사용자 지정 도메인 이름 추가](#add-a-custom-domain-name-to-your-tenant) 단계에서 확인한 사용자 지정 도메인 이름을 사용합니다. 

사용자 지정 도메인 이름을 확인한 후 **사용자 지정 도메인 이름 HTTPS** 를 선택합니다. 그런 다음 **인증서 관리 유형** 에서 [Front Door 관리](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door) 또는 [내 인증서 사용](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate)을 선택합니다. 

다음 스크린샷은 사용자 지정 도메인을 추가하고 Azure Front Door 인증서를 사용하여 HTTPS를 사용하도록 설정하는 방법을 보여 줍니다.

![Azure Front Door 사용자 지정 도메인 설정](./media/custom-domain/azure-front-door-add-custom-domain.png) 

## <a name="configure-cors"></a>CORS 구성

HTML 템플릿을 사용하여 [Azure AD B2C 사용자 인터페이스를 사용자 지정](customize-ui-with-html.md)하는 경우 사용자 지정 도메인을 사용하여 [CORS를 구성](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors)해야 합니다.

다음 단계를 수행하여 CORS(원본 간 리소스 공유)에 Azure Blob Storage를 구성합니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.
1. 메뉴에서 **CORS** 를 선택합니다.
1. **허용된 원본** 에 `https://your-domain-name`을 입력합니다. `your-domain-name`을 실제 도메인 이름으로 바꿉니다. 예들 들어 `https://login.contoso.com`입니다. 테넌트 이름을 입력할 때는 모두 소문자를 사용합니다.
1. **허용된 메소드** 에서 `GET`과 `OPTIONS`를 모두 선택합니다.
1. **허용된 헤더** 에 별표(*)를 입력합니다.
1. **노출된 헤더** 에 별표(*)를 입력합니다.
1. **최대 기간** 에 200을 입력합니다.
1. **저장** 을 선택합니다.

## <a name="configure-your-identity-provider"></a>ID 공급자 구성

사용자가 소셜 ID 공급자를 통한 로그인을 선택하면 Azure AD B2C는 권한 부여 요청을 시작하고 선택한 ID 공급자로 사용자를 이동하여 로그인 프로세스를 완료합니다. 권한 부여 요청은 `redirect_uri`를 Azure AD B2C 기본 도메인 이름으로 지정합니다. 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

외부 ID 공급자를 사용하여 로그인할 수 있도록 정책을 구성한 경우에는 OAuth 리디렉션 Uri를 사용자 지정 도메인으로 업데이트합니다. 대부분의 ID 공급자는 여러 리디렉션 Uri 등록을 허용합니다. Azure AD B2C 기본 도메인 이름을 사용하는 애플리케이션에 영향을 주지 않고 사용자 지정 정책을 테스트할 수 있도록 리디렉션 Uri를 바꾸는 것보다는 추가하는 것이 좋습니다. 

다음 리디렉션 URI에서 다음과 같이 바꿉니다.

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- **<custom-domain-name>** 을 사용자 지정 도메인 이름으로 바꿉니다.
- **<tenant-name>** 을 테넌트 이름 또는 테넌트 ID로 바꿉니다.

다음 예제에서는 유효한 OAuth 리디렉션 URI를 보여 줍니다.

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

[테넌트 ID](#optional-use-tenant-id)를 사용하도록 선택하는 경우 유효한 OAuth 리디렉션 URI는 다음과 같습니다.

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

[SAML ID 공급자](saml-identity-provider-technical-profile.md) 메타데이터는 다음과 같습니다.

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="test-your-custom-domain"></a>사용자 지정 도메인 테스트

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **정책** 에서 **사용자 흐름(정책)** 을 선택합니다.
1. 사용자 흐름을 선택한 다음 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 으로 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **클립보드로 복사** 를 클릭합니다.

    ![권한 부여 요청 URI 복사](./media/custom-domain/user-flow-run-now.png)

1. **사용자 흐름 엔드포인트 실행** URL에서 Azure AD B2C 도메인(<tenant-name>.b2clogin.com)을 사용자 지정 도메인으로 바꿉니다.  
    예를 들어 다음 식을 사용하는 대신

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    다음을 사용합니다.

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```
1. **사용자 흐름 실행** 을 선택합니다. Azure AD B2C 정책이 로드되어야 합니다.
1. 로컬 계정 및 소셜 계정을 모두 사용하여 로그인합니다.
1. 나머지 정책에 대한 테스트를 반복합니다.

## <a name="configure-your-application"></a>애플리케이션 구성 

사용자 지정 도메인을 구성하고 테스트한 후에는 Azure AD B2C 도메인 대신 호스트 이름으로 사용자 지정 도메인을 지정하는 URL을 로드하도록 애플리케이션을 업데이트할 수 있습니다. 

사용자 지정 도메인 통합은 Azure AD B2C 정책(사용자 흐름 또는 사용자 지정 정책)을 사용하여 사용자를 인증하는 인증 엔드포인트에 적용됩니다. 해당 엔드포인트는 다음과 같이 나타낼 수 있습니다.

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

다음을
- **custom-domain** 을 사용자 도메인으로
- **tenant-name** 을 테넌트 이름 또는 테넌트 ID로
- **policy-name** 을 정책 이름으로. [Azure AD B2C 정책에 대해 자세히 알아보세요](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 


[SAML 서비스 공급자](./saml-service-provider.md) 메타데이터는 다음과 같이 나타낼 수 있습니다. 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>(선택 사항) 필드 테넌트 ID 사용

URL의 “B2C”에 대한 모든 참조를 제거하기 위해 URL의 B2C 테넌트 이름을 테넌트 ID GUID로 바꿀 수 있습니다. Azure Portal의 B2C 개요 페이지에서 테넌트 ID GUID를 찾을 수 있습니다.
예를 들어 `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/`를 `https://account.contosobank.co.uk/<tenant ID GUID>/`로 변경하세요.

테넌트 이름 대신 테넌트 ID를 사용하도록 선택하는 경우 ID 공급자의 **OAuth 리디렉션 URI** 도 그에 따라 업데이트해야 합니다. 자세한 내용은 [ID 공급자 구성](#configure-your-identity-provider)을 참조하세요.

### <a name="token-issuance"></a>토큰 발급

토큰 발급자 이름(iss) 클레임은 사용 중인 사용자 지정 도메인에 따라 변경됩니다. 예를 들면 다음과 같습니다.

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>기본 도메인 이름에 대한 액세스 차단

사용자 지정 도메인을 추가하고 애플리케이션을 구성한 후에도 사용자는 <tenant-name>.b2clogin.com 도메인에 액세스할 수 있습니다. 액세스를 방지하기 위해 허용되는 도메인 목록에 대해 “호스트 이름” 권한 부여 요청을 확인하는 정책을 구성할 수 있습니다. 호스트 이름은 URL에 표시되는 도메인 이름입니다. 호스트 이름은 `{Context:HostName}` [클레임 확인자](claim-resolver-overview.md)를 통해 확인할 수 있습니다. 그런 다음 사용자 지정 오류 메시지를 표시할 수 있습니다. 

1. [GitHub](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name)에서 호스트 이름을 확인하는 조건부 액세스 정책의 예를 가져옵니다.
1. 각 파일에서 `yourtenant` 문자열을 Azure AD B2C 테넌트의 이름으로 바꿉니다. 예를 들어 B2C 테넌트의 이름이 *contosob2c* 인 경우 모든 `yourtenant.onmicrosoft.com` 인스턴스는 `contosob2c.onmicrosoft.com`이 됩니다.
1. 정책 파일, `B2C_1A_TrustFrameworkExtensions_HostName.xml`과 `B2C_1A_signup_signin_HostName.xml`을 차례대로 업로드합니다.

::: zone-end

## <a name="troubleshooting"></a>문제 해결

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C에서 페이지를 찾을 수 없다는 오류 반환

- **증상** - 사용자 지정 도메인을 구성한 후 사용자 지정 도메인을 사용하여 로그인하려고 하면 HTTP 404 오류 메시지가 표시됩니다.
- **가능한 원인** - 이 문제는 DNS 구성 또는 Azure Front Door 백 엔드 구성과 관련이 있을 수 있습니다. 
- **해결 방법**:  
    1. Azure AD B2C 테넌트에서 사용자 지정 도메인을 [등록하고 확인](#add-a-custom-domain-name-to-your-tenant)합니다.
    1. [사용자 지정 도메인](../frontdoor/front-door-custom-domain.md)을 올바르게 구성합니다. `CNAME`사용자 지정 도메인의 레코드는 Azure Front Door 기본 프런트 엔드 호스트(예: contoso.azurefd.net)를 가리켜야 합니다.
    1. [Azure Front Door 백 엔드 풀 구성](#set-up-your-custom-domain-on-azure-front-door)이 사용자 지정 도메인 이름을 설정하고 사용자 흐름 또는 사용자 지정 정책이 저장되는 테넌트를 가리키도록 합니다.

### <a name="identify-provider-returns-an-error"></a>ID 공급자가 오류 반환

- **증상** - 사용자 지정 도메인을 구성한 후 로컬 계정을 사용하여 로그인할 수 있습니다. 그러나 외부 [소셜 또는 엔터프라이즈 ID 공급자](add-identity-provider.md)의 자격 증명을 사용하여 로그인하는 경우 ID 공급자가 오류 메시지를 표시합니다.
- **가능한 원인** - Azure AD B2C에서 사용자가 페더레이션 ID 공급자를 사용하여 로그인하도록 하는 경우 Azure AD B2C는 리디렉션 URI를 지정합니다. 리디렉션 URI는 ID 공급자가 토큰을 반환하는 엔드포인트입니다. 리디렉션 URI는 애플리케이션이 권한 부여 요청에 사용하는 것과 동일한 도메인입니다. 리디렉션 URI가 아직 ID 공급자에 등록되지 않은 경우 새 리디렉션 URI를 신뢰하지 않을 수 있으며, 이로 인해 오류 메시지가 발생합니다. 
- **해결 방법** - [ID 공급자 구성](#configure-your-identity-provider)의 단계에 따라 새 리디렉션 URI를 추가합니다. 


## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>Azure Front Door 고급 구성(예: ‘웹 애플리케이션 방화벽 규칙’)을 사용할 수 있나요? 
  
Azure Front Door 고급 구성 설정은 공식적으로 지원되지 않지만 사용자의 책임으로 사용할 수 있습니다. 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>지금 실행을 사용하여 내 정책을 실행하려고 할 때 사용자 지정 도메인이 표시되지 않는 이유는 무엇인가요?

URL을 복사하고 도메인 이름을 수동으로 변경한 다음 브라우저에 다시 붙여넣으세요.

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>Azure AD B2C에 어떤 IP 주소가 제공되나요? 사용자의 IP 주소인가요? 아니면 Azure Front Door IP 주소인가요?

Azure Front Door는 사용자의 원래 IP 주소를 전달합니다. 이는 감사 보고나 사용자 지정 정책에 표시되는 IP 주소입니다.

### <a name="can-i-use-a-third-party-web-application-firewall-waf-with-b2c"></a>B2C에 타사 WAF(웹 애플리케이션 방화벽)를 사용할 수 있나요?

Azure Front Door 앞에서 사용자 고유의 웹 애플리케이션 방화벽을 사용하려면 구성 후 Azure AD B2C 사용자 흐름에서 모든 것이 올바르게 작동하는지 검사해야 합니다.

## <a name="next-steps"></a>다음 단계

[OAuth 권한 부여 요청](protocols-overview.md)에 대해 알아봅니다.