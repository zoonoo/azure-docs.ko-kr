---
title: Azure AD B2C 사용자 지정 도메인 사용
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에 대 한 리디렉션 Url에서 사용자 지정 도메인을 사용 하도록 설정 하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580167"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Azure Active Directory B2C에 대 한 사용자 지정 도메인 사용

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

이 문서에서는 Azure Active Directory B2C (Azure AD B2C)에 대 한 리디렉션 Url에서 사용자 지정 도메인을 사용 하도록 설정 하는 방법을 설명 합니다. 응용 프로그램과 함께 사용자 지정 도메인을 사용 하면 보다 원활한 사용자 환경을 제공 합니다. 사용자의 관점에서 보면 Azure AD B2C 기본 도메인 *<테 넌 트 이름>. b2clogin.com* 로 리디렉션하는 대신 로그인 프로세스 중에 도메인에 남아 있습니다.

![사용자 지정 도메인 사용자 환경](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>사용자 지정 도메인 개요

[Azure Front 도어](https://azure.microsoft.com/services/frontdoor/)를 사용 하 여 Azure AD B2C에 대 한 사용자 지정 도메인을 사용 하도록 설정할 수 있습니다. Azure 전면 도어는 Microsoft 글로벌에 지 네트워크를 사용 하 여 빠르고, 안전 하 고, 확장성이 뛰어난 웹 응용 프로그램을 만들기 위한 확장 가능한 글로벌 진입점입니다. Azure Front 도어 뒤에 Azure AD B2C 콘텐츠를 렌더링 한 다음, Azure Front 도어에서 옵션을 구성 하 여 응용 프로그램 URL의 사용자 지정 도메인을 통해 콘텐츠를 제공할 수 있습니다.

다음 다이어그램은 Azure Front 도어 통합을 보여 줍니다.

1. 응용 프로그램에서 사용자는 로그인 단추를 클릭 하 여 Azure AD B2C 로그인 페이지로 이동 합니다. 이 페이지에서는 사용자 지정 도메인 이름을 지정 합니다.
1. 웹 브라우저에서 사용자 지정 도메인 이름을 Azure Front 도어 IP 주소로 확인 합니다. DNS 확인을 수행 하는 동안 사용자 지정 도메인 이름이 포함 된 CNAME (정식 이름) 레코드는 전면 도어 기본 프런트 엔드 호스트 (예:)를 가리킵니다 `contoso.azurefd.net` . 
1. 사용자 지정 도메인으로 주소가 지정 된 트래픽 (예: `login.contoso.com` )은 지정 된 전면 도어 기본 프런트 엔드 호스트 ()로 라우팅됩니다 `contoso.azurefd.net` .
1. Azure Front 도어가 Azure AD B2C 기본 도메인을 사용 하 여 Azure AD B2C 콘텐츠를 호출 `<tenant-name>.b2clogin.com` 합니다. Azure AD B2C 끝점에 대 한 요청은 원래 사용자 지정 도메인 이름을 포함 하는 사용자 지정 HTTP 헤더를 포함 합니다.
1. Azure AD B2C는 관련 콘텐츠와 원래 사용자 지정 도메인을 표시 하 여 요청에 응답 합니다.

![사용자 지정 도메인 네트워킹 다이어그램](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> 브라우저에서 Azure Front 도어로의 연결은 항상 i p v 6 대신 IPv4를 사용 해야 합니다.

사용자 지정 도메인을 사용 하는 경우 다음을 고려 하십시오.

- 여러 사용자 지정 도메인을 설정할 수 있습니다. 지원 되는 사용자 지정 도메인의 최대 수에 대 한 자세한 내용은 azure [AD 서비스 제한 및 제한](../active-directory/enterprise-users/directory-service-limits-restrictions.md) Azure AD B2C 및 azure [구독 및 서비스 제한, 할당량](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits) 및 azure Front 문에 대 한 제약 조건을 참조 하세요.
- Azure Front 도어가 별도의 Azure 서비스 이므로 추가 요금이 발생 합니다. 자세한 내용은 [Front 도어 가격 책정](https://azure.microsoft.com/pricing/details/frontdoor)을 참조 하세요.
- Azure Front 도어 [웹 응용 프로그램 방화벽](../web-application-firewall/afds/afds-overview.md)을 사용 하려면 방화벽 구성을 확인 하 고 Azure AD B2C 사용자 흐름에서 규칙이 올바르게 작동 하는지 확인 해야 합니다.
- 사용자 지정 도메인을 구성한 후에도 사용자는 사용자 지정 정책을 사용 하지 않고 [액세스를 차단](#block-access-to-the-default-domain-name)하는 경우를 제외 하 고 사용자 지정 도메인 이름 Azure AD B2C 기본 도메인 이름 *><* 에 액세스할 수 있습니다.
- 여러 응용 프로그램이 있는 경우 브라우저에서 현재 사용 중인 도메인 이름으로 Azure AD B2C 세션을 저장 하므로 사용자 지정 도메인으로 모두 마이그레이션합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="add-a-custom-domain-name-to-your-tenant"></a>테 넌 트에 사용자 지정 도메인 이름 추가

[AZURE AD에서 사용자 지정 도메인을 추가 하 고 유효성을 검사](../active-directory/fundamentals/add-custom-domain.md)하는 방법에 대 한 지침을 따르세요. 도메인을 확인 한 후에는 만든 DNS TXT 레코드를 삭제 합니다.

> [!IMPORTANT]
> 이러한 단계를 수행 하려면 **Azure AD B2C** 테 넌 트에 로그인 하 고 **Azure Active Directory** 서비스를 선택 해야 합니다.

사용 하려는 각 하위 도메인을 확인 합니다. 최상위 도메인만 확인 하면 충분 하지 않습니다. 예를 들어 *login.contoso.com* 및 *account.contoso.com* 를 사용 하 여 로그인 할 수 있으려면 최상위 도메인 *contoso.com* 뿐만 아니라 하위 도메인을 모두 확인 해야 합니다.  

## <a name="create-a-new-azure-front-door-instance"></a>새 Azure Front 도어 인스턴스 만들기

프런트 엔드 호스트 및 라우팅 규칙에 대 한 기본 설정을 사용 하 여 [응용 프로그램에 대 한 프런트 도어를 만드는](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application) 단계를 수행 합니다. 

> [!IMPORTANT]
> 이러한 단계에서 1 단계에서 Azure Portal에 로그인 한 후 **디렉터리 + 구독** 을 선택 하 고 Azure Front 문에 사용할 azure 구독을 포함 하는 디렉터리를 선택 합니다. Azure AD B2C 테 넌 트를 포함 하는 *디렉터리가 되어서는 안 됩니다.* 

**백 엔드 추가** 단계에서 다음 설정을 사용 합니다.

* **백 엔드 호스트 유형** 으로 **사용자 지정 호스트** 를 선택 합니다.  
* **백 엔드 호스트 이름** 에 대해 Azure AD B2C 끝점에 대 한 호스트 이름 <테 넌 트 이름>. b2clogin.com를 선택 합니다. 예를 들어 contoso.b2clogin.com을 입력합니다. 
* **백 엔드 호스트 헤더** 에 대해 **백 엔드 호스트 이름** 에 대해 선택한 것과 동일한 값을 선택 합니다.

![백 엔드 추가](./media/custom-domain/add-a-backend.png)

백 엔드 **풀** 에 **백 엔드** 를 추가한 후 **상태 프로브** 를 사용 하지 않도록 설정 합니다.

![백 엔드 풀 추가](./media/custom-domain/add-a-backend-pool.png)

## <a name="set-up-your-custom-domain-on-azure-front-door"></a>Azure Front 문에 사용자 지정 도메인 설정

단계에 따라 [사용자 지정 도메인을 Front 문에 추가](../frontdoor/front-door-custom-domain.md)합니다. `CNAME`사용자 지정 도메인에 대 한 레코드를 만들 때 이전에 [Azure AD에 사용자 지정 도메인 이름 추가](#add-a-custom-domain-name-to-your-tenant) 단계에서 확인 한 사용자 지정 도메인 이름을 사용 합니다. 

사용자 지정 도메인 이름이 확인 된 후 **사용자 지정 도메인 이름 HTTPS** 를 선택 합니다. 그런 다음 **인증서 관리 유형** 아래에서 [Front 도어 관리](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door)를 선택 하거나 [내 인증서를 사용](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate)합니다. 

다음 스크린샷은 사용자 지정 도메인을 추가 하 고 Azure Front 도어 인증서를 사용 하 여 HTTPS를 사용 하도록 설정 하는 방법을 보여 줍니다.

![Azure Front 도어 사용자 지정 도메인 설정](./media/custom-domain/azure-front-door-add-custom-domain.png) 

## <a name="configure-cors"></a>CORS 구성

HTML 템플릿을 사용 하 여 [Azure AD B2C 사용자 인터페이스를 사용자](customize-ui-with-html.md) 지정 하는 경우 사용자 지정 도메인을 사용 하 여 [CORS를 구성](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors) 해야 합니다.

다음 단계를 수행 하 여 원본 간 리소스 공유에 대 한 Azure Blob storage를 구성 합니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.
1. 메뉴에서 **CORS** 를 선택합니다.
1. **허용된 원본** 에 `https://your-domain-name`을 입력합니다. `your-domain-name`을 도메인 이름으로 바꿉니다. 예: `https://login.contoso.com` 모든 소문자를 사용 하 여 테 넌 트 이름을 입력 합니다.
1. **허용된 메소드** 에서 `GET`과 `OPTIONS`를 모두 선택합니다.
1. **허용된 헤더** 에 별표(*)를 입력합니다.
1. **노출된 헤더** 에 별표(*)를 입력합니다.
1. **최대 기간** 에 200을 입력합니다.
1. **저장** 을 선택합니다.

## <a name="configure-your-identity-provider"></a>Id 공급자 구성

사용자가 소셜 id 공급자를 사용 하 여 로그인 하도록 선택 하면 Azure AD B2C 권한 부여 요청을 시작 하 고 선택한 id 공급자로 사용자를 이동 하 여 로그인 프로세스를 완료 합니다. 권한 부여 요청은 `redirect_uri` Azure AD B2C 기본 도메인 이름으로를 지정 합니다. 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

외부 id 공급자를 사용 하 여 로그인 할 수 있도록 정책을 구성한 경우에는 사용자 지정 도메인을 사용 하 여 OAuth 리디렉션 Uri를 업데이트 합니다. 대부분의 id 공급자를 사용 하면 여러 리디렉션 Uri를 등록할 수 있습니다. 기본 도메인 이름을 Azure AD B2C 사용 하는 응용 프로그램에 영향을 주지 않고 사용자 지정 정책을 테스트할 수 있도록 리디렉션 Uri를 대체 하는 대신 추가 하는 것이 좋습니다. 

다음 리디렉션 URI에서:

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- **<사용자** 지정 도메인 이름>를 사용자 지정 도메인 이름으로 바꿉니다.
- **<테 넌 트 이름>** 를 테 넌 트의 이름 또는 테 넌 트 ID로 바꿉니다.

다음 예제에서는 유효한 OAuth 리디렉션 URI를 보여 줍니다.

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

[테 넌 트 ID](#optional-use-tenant-id)를 사용 하도록 선택 하는 경우 유효한 OAUTH 리디렉션 URI는 다음과 같습니다.

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

[SAML id 공급자](saml-identity-provider-technical-profile.md) 메타 데이터는 다음과 같습니다.

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="test-your-custom-domain"></a>사용자 지정 도메인 테스트

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **정책** 에서 **사용자 흐름(정책)** 을 선택합니다.
1. 사용자 흐름을 선택한 다음 **사용자 흐름 실행** 을 선택 합니다.
1. **애플리케이션** 으로 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **클립보드로 복사를** 클릭 합니다.

    ![권한 부여 요청 URI 복사](./media/custom-domain/user-flow-run-now.png)

1. **사용자 흐름 끝점 URL 실행** 에서 Azure AD B2C 도메인 (<테 넌 트 이름>. b2clogin.com)을 사용자 지정 도메인으로 바꿉니다.  
    예를 들어 다음 식을 사용하는 대신

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    사용

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```
1. **사용자 흐름 실행** 을 선택합니다. Azure AD B2C 정책이 로드 되어야 합니다.
1. 로컬 계정과 소셜 계정 모두를 사용 하 여 로그인 합니다.
1. 나머지 정책으로 테스트를 반복 합니다.

## <a name="configure-your-application"></a>애플리케이션 구성 

사용자 지정 도메인을 구성 하 고 테스트 한 후에는 Azure AD B2C 도메인 대신 호스트 이름으로 사용자 지정 도메인을 지정 하는 URL을 로드 하도록 응용 프로그램을 업데이트할 수 있습니다. 

사용자 지정 도메인 통합은 Azure AD B2C 정책 (사용자 흐름 또는 사용자 지정 정책)을 사용 하 여 사용자를 인증 하는 인증 끝점에 적용 됩니다. 이러한 끝점은 다음과 같습니다.

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

다음을
- 사용자 지정 도메인을 사용 하는 **사용자 지정 도메인**
- 테 넌 트 이름 또는 테 넌 트 ID를 사용 하는 **테 넌 트 이름**
- **정책** 이름 (정책 이름 포함) [Azure AD B2C 정책에 대해 자세히 알아보세요](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 


[SAML 서비스 공급자](./saml-service-provider.md) 메타 데이터는 다음과 같을 수 있습니다. 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>필드 테 넌 트 ID 사용

Url의 "B2C"에 대 한 모든 참조를 제거 하기 위해 URL의 B2C 테 넌 트 이름을 테 넌 트 ID GUID로 바꿀 수 있습니다. Azure Portal의 B2C 개요 페이지에서 테 넌 트 ID GUID를 찾을 수 있습니다.
예를 들어 `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/`를 `https://account.contosobank.co.uk/<tenant ID GUID>/`로 변경하세요.

테 넌 트 이름 대신 테 넌 트 ID를 사용 하도록 선택 하는 경우 id 공급자 **OAuth 리디렉션 uri** 를 적절 하 게 업데이트 해야 합니다. 자세한 내용은 [id 공급자 구성](#configure-your-identity-provider)을 참조 하세요.

### <a name="token-issuance"></a>토큰 발급

Iss (토큰 발급자 이름) 클레임은 사용 중인 사용자 지정 도메인에 따라 변경 됩니다. 예를 들면 다음과 같습니다.

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>기본 도메인 이름에 대 한 액세스 차단

사용자 지정 도메인을 추가 하 고 응용 프로그램을 구성한 후에도 사용자는 <테 넌 트 이름>. b2clogin.com 도메인에 액세스할 수 있습니다. 액세스를 방지 하기 위해 허용 되는 도메인 목록에 대해 "호스트 이름" 권한 부여 요청을 확인 하는 정책을 구성할 수 있습니다. 호스트 이름은 URL에 표시 되는 도메인 이름입니다. 호스트 이름은 `{Context:HostName}` [클레임 확인자](claim-resolver-overview.md)를 통해 사용할 수 있습니다. 그런 다음 사용자 지정 오류 메시지를 표시할 수 있습니다. 

1. [GitHub](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name)에서 호스트 이름을 확인 하는 조건부 액세스 정책의 예를 가져옵니다.
1. 각 파일에서 `yourtenant` 문자열을 Azure AD B2C 테넌트의 이름으로 바꿉니다. 예를 들어 B2C 테넌트의 이름이 *contosob2c* 인 경우 모든 `yourtenant.onmicrosoft.com` 인스턴스는 `contosob2c.onmicrosoft.com`이 됩니다.
1. 다음 순서로 정책 파일을 업로드 `B2C_1A_TrustFrameworkExtensions_HostName.xml` `B2C_1A_signup_signin_HostName.xml` 합니다.

::: zone-end

## <a name="troubleshooting"></a>문제 해결

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C 페이지를 찾을 수 없음 오류를 반환 합니다.

- **증상** -사용자 지정 도메인을 구성한 후 사용자 지정 도메인을 사용 하 여 로그인 하려고 하면 HTTP 404 오류 메시지가 표시 됩니다.
- **가능한 원인** -이 문제는 DNS 구성 또는 Azure Front 도어 백 엔드 구성과 관련이 있을 수 있습니다. 
- **해결 방법**:  
    1. Azure AD B2C 테 넌 트에서 사용자 지정 도메인을 [등록 하 고 성공적으로 확인](#add-a-custom-domain-name-to-your-tenant) 했는지 확인 합니다.
    1. [사용자 지정 도메인이](../frontdoor/front-door-custom-domain.md) 올바르게 구성 되어 있는지 확인 합니다. `CNAME`사용자 지정 도메인에 대 한 레코드는 Azure Front 도어 기본 프런트 엔드 호스트 (예: contoso.azurefd.net)를 가리켜야 합니다.
    1. [Azure 전면 도어 백 엔드 풀 구성이](#set-up-your-custom-domain-on-azure-front-door) 사용자 지정 도메인 이름을 설정 하는 테 넌 트를 가리키고 사용자 흐름 또는 사용자 지정 정책이 저장 되도록 합니다.

### <a name="identify-provider-returns-an-error"></a>공급자 식별에서 오류를 반환 합니다.

- **증상** -사용자 지정 도메인을 구성한 후 로컬 계정을 사용 하 여 로그인 할 수 있습니다. 그러나 외부 [소셜 또는 엔터프라이즈 id 공급자](add-identity-provider.md)의 자격 증명을 사용 하 여 로그인 하는 경우 id 공급자는 오류 메시지를 표시 합니다.
- **가능한 원인** -사용자가 페더레이션 id 공급자를 사용 하 여 로그인 하는 경우 리디렉션 URI를 지정 하는 Azure AD B2C. 리디렉션 URI는 id 공급자가 토큰을 반환 하는 끝점입니다. 리디렉션 URI는 응용 프로그램에서 권한 부여 요청과 함께 사용 하는 것과 동일한 도메인입니다. 리디렉션 URI가 아직 id 공급자에 등록 되지 않은 경우 새 리디렉션 URI를 신뢰 하지 않을 수 있으며,이로 인해 오류 메시지가 발생 합니다. 
- **해결 방법** - [id 공급자 구성](#configure-your-identity-provider) 의 단계에 따라 새 리디렉션 URI를 추가 합니다. 


## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>Azure Front 도어 고급 구성 (예: *웹 응용 프로그램 방화벽 규칙*)을 사용할 수 있나요? 
  
Azure Front 도어 고급 구성 설정은 공식적으로 지원 되지 않지만 사용자의 위험에 따라 사용할 수 있습니다. 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>지금 실행을 사용 하 여 내 정책을 실행 하려고 하면 사용자 지정 도메인이 표시 되지 않는 이유는 무엇 인가요?

URL을 복사 하 고 도메인 이름을 수동으로 변경한 다음 브라우저에 다시 붙여넣습니다.

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>Azure AD B2C에 제공 되는 IP 주소는 무엇 인가요? 사용자의 IP 주소 또는 Azure Front 도어 IP 주소

Azure Front 도어가 사용자의 원래 IP 주소를 전달 합니다. 감사 보고 나 사용자 지정 정책에 표시 되는 IP 주소입니다.

### <a name="can-i-use-a-third-party-web-application-firewall-waf-with-b2c"></a>B2C에서 타사 WAF (웹 응용 프로그램 방화벽)를 사용할 수 있나요?

Azure Front 도어 앞에서 사용자 고유의 웹 응용 프로그램 방화벽을 사용 하려면 Azure AD B2C 사용자 흐름에서 모든 것이 올바르게 작동 하는지 구성 하 고 유효성을 검사 해야 합니다.

## <a name="next-steps"></a>다음 단계

[OAuth 권한 부여 요청](protocols-overview.md)에 대해 알아봅니다.