---
title: '자습서: 앱에 id 공급자 추가'
titleSuffix: Azure AD B2C
description: Azure Portal를 사용 하 여 Azure Active Directory B2C에서 응용 프로그램에 id 공급자를 추가 하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a49eae95628645f6586a637c103433b122b5d287
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950955"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C의 응용 프로그램에 id 공급자 추가

응용 프로그램에서 사용자가 다른 id 공급자를 사용 하 여 로그인 하도록 할 수 있습니다. *Id 공급자* 는 응용 프로그램에 인증 서비스를 제공 하는 동안 id 정보를 만들고, 유지 관리 하 고, 관리 합니다. Azure Portal를 사용 하 여 [사용자 흐름](active-directory-b2c-reference-policies.md) 에 Azure Active Directory B2C (Azure AD B2C)에서 지 원하는 id 공급자를 추가할 수 있습니다.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Id 공급자 응용 프로그램 만들기
> * 테 넌 트에 id 공급자 추가
> * 사용자 흐름에 id 공급자 추가

일반적으로 응용 프로그램에서 id 공급자를 하나만 사용 하지만 추가 옵션을 선택할 수 있습니다. 이 자습서에서는 응용 프로그램에 Azure AD id 공급자 및 Facebook id 공급자를 추가 하는 방법을 보여 줍니다. 이러한 id 공급자를 모두 응용 프로그램에 추가 하는 것은 선택 사항입니다. [Amazon](active-directory-b2c-setup-amzn-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md)또는 [Twitter](active-directory-b2c-setup-twitter-app.md)와 같은 다른 id 공급자를 추가할 수도 있습니다.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

사용자가 응용 프로그램에 등록 하 고 로그인 할 수 있도록 [사용자 흐름을 만듭니다](tutorial-create-user-flows.md) .

## <a name="create-applications"></a>Alkalmazások létrehozása

Id 공급자 응용 프로그램은 Azure AD B2C 테 넌 트와 통신할 수 있도록 식별자 및 키를 제공 합니다. 자습서의이 섹션에서는 식별자 및 키를 가져와서 테 넌 트에 id 공급자를 추가 하는 Facebook 응용 프로그램 및 Azure AD 응용 프로그램을 만듭니다. Id 공급자 중 하나만 추가 하는 경우 해당 공급자에 대 한 응용 프로그램을 만들어야 합니다.

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory 응용 프로그램 만들기

Azure AD에서 사용자에 대 한 로그인을 사용 하도록 설정 하려면 Azure AD 테 넌 트 내에서 응용 프로그램을 등록 해야 합니다. Azure AD 테 넌 트는 Azure AD B2C 테 넌 트와 동일 하지 않습니다.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 azure ad 테 넌 트가 포함 된 디렉터리를 선택 하 여 azure ad 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
1. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스** 를 선택한 다음 **앱 등록**를 검색 하 고 선택 합니다.
1. **새 등록**을 선택 합니다.
1. Adja meg az alkalmazás nevét. Például: `Azure AD B2C App`.
1. 이 응용 프로그램에 대해서 **만이 조직 디렉터리에서 선택한 계정** 에 동의 합니다.
1. **리디렉션 URI**의 경우 **웹** 의 값을 그대로 사용 하 고, `your-B2C-tenant-name`를 Azure AD B2C 테 넌 트의 이름으로 대체 하 여 모든 소문자에 다음 URL을 입력 합니다.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Például: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    이제 모든 Url에서 [b2clogin.com](b2clogin.md)를 사용 해야 합니다.

1. **등록**을 선택 하 고 이후 단계에서 사용 하는 **응용 프로그램 (클라이언트) ID** 를 기록 합니다.
1. 응용 프로그램 메뉴의 **관리** 에서 **인증서 & 암호**를 선택한 다음 **새 클라이언트 암호**를 선택 합니다.
1. 클라이언트 암호에 대 한 **설명을** 입력 합니다. Például: `Azure AD B2C App Secret`.
1. 만료 기간을 선택 합니다. 이 응용 프로그램의 경우 **1 년의**선택 항목을 수락 합니다.
1. **추가**를 선택한 다음 이후 단계에서 사용 하는 새 클라이언트 암호의 값을 기록 합니다.

### <a name="create-a-facebook-application"></a>Facebook 응용 프로그램 만들기

Azure AD B2C에서 Facebook 계정을 id 공급자로 사용 하려면 Facebook에서 응용 프로그램을 만들어야 합니다. Facebook 계정이 아직 없는 경우 [https://www.facebook.com/](https://www.facebook.com/)에서 다운로드할 수 있습니다.

1. Facebook 계정 자격 증명을 사용 하 여 [개발자 용 facebook](https://developers.facebook.com/) 에 로그인 합니다.
1. 아직 수행 하지 않은 경우 Facebook 개발자로 등록 해야 합니다. 이렇게 하려면 페이지의 오른쪽 위 모서리에서 **시작** 을 선택 하 고 Facebook의 정책에 동의 하 고 등록 단계를 완료 합니다.
1. **내 앱** 을 선택 하 고 **앱 만들기**를 선택 합니다.
1. **표시 이름** 및 유효한 **연락처 전자 메일**을 입력 합니다.
1. **앱 ID 만들기**를 클릭 합니다. Facebook 플랫폼 정책을 수락 하 고 온라인 보안 검사를 완료 해야 할 수 있습니다.
1. **기본** > **설정** 을 선택 합니다.
1. **범주**를 선택 합니다 (예: `Business and Pages`). 이 값은 Facebook에 필요 하지만 Azure AD B2C에서 사용 되지 않습니다.
1. 페이지 맨 아래에서 **플랫폼 추가**를 선택 하 고 **웹 사이트**를 선택 합니다.
1. **사이트 URL**에 `your-tenant-name`를 테 넌 트의 이름으로 바꾸는 `https://your-tenant-name.b2clogin.com/`를 입력 합니다.
1. **개인 정보 취급 방침 url**에 대 한 url을 입력 합니다 (예: `http://www.contoso.com/`). 개인 정보 취급 방침 URL은 응용 프로그램에 대 한 개인 정보를 제공 하기 위해 유지 관리 하는 페이지입니다.
1. **변경 내용 저장**을 선택 합니다.
1. 페이지 맨 위에서 **앱 ID**값을 기록 합니다.
1. **앱 비밀**옆의 값 **표시** 및 기록을 선택 합니다. 앱 ID 및 앱 암호를 모두 사용 하 여 테 넌 트에서 Facebook을 id 공급자로 구성 합니다. **앱 비밀** 은 안전 하 게 저장 해야 하는 중요 한 보안 자격 증명입니다.
1. **제품**옆에 있는 더하기 기호를 선택 하 고 **Facebook 로그인**아래에서 **설정**을 선택 합니다.
1. 왼쪽 메뉴의 **Facebook 로그인** 아래에서 **설정**을 선택 합니다.
1. **유효한 OAuth 리디렉션 uri**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`을 입력 합니다. `your-tenant-name`을 테 넌 트의 이름으로 바꿉니다. 페이지 맨 아래에 있는 **변경 내용 저장** 을 선택 합니다.
1. Facebook 응용 프로그램을 Azure AD B2C 사용할 수 있도록 하려면 페이지의 오른쪽 위에 있는 **상태** 선택기를 클릭 하 여 응용 프로그램을 **공개로 설정한** 다음 **확인**을 클릭 합니다. 이 시점에서 상태가 **개발** 에서 **라이브**로 변경 됩니다.

## <a name="add-the-identity-providers"></a>Id 공급자 추가

추가 하려는 id 공급자에 대 한 응용 프로그램을 만든 후에는 테 넌 트에 id 공급자를 추가 합니다.

### <a name="add-the-azure-active-directory-identity-provider"></a>Azure Active Directory id 공급자 추가

1. Azure AD B2C 테 넌 트가 포함 된 디렉터리를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 선택 합니다.
1. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스** 를 선택한 다음 **Azure AD B2C**를 검색 하 고 선택 합니다.
1. **Id 공급자**를 선택한 다음 **New openid connect Connect 공급자**를 선택 합니다.
1. **이름을**입력 합니다. 예를 들어 *Contoso AZURE AD*를 입력 합니다.
1. **메타 데이터 url**에 대해 `your-AD-tenant-domain`를 Azure AD 테 넌 트의 도메인 이름으로 바꾸는 다음 url을 입력 합니다.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Például: `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. **클라이언트 id**에 대해 이전에 기록한 응용 프로그램 id를 입력 합니다.
1. **클라이언트 암호**에 대해 이전에 기록한 클라이언트 암호를 입력 합니다.
1. **범위**, **응답 유형**및 **응답 모드**에 대 한 기본값을 그대로 둡니다.
1. 필드 **Domain_hint**에 대 한 값을 입력 합니다. 예를 들면 *ContosoAD*입니다. [도메인 힌트](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) 는 응용 프로그램의 인증 요청에 포함 된 지시문입니다. 사용자를 페더레이션 IdP 로그인 페이지로 가속화 하는 데 사용할 수 있습니다. 또는 다중 테 넌 트 응용 프로그램에서 사용자를 테 넌 트의 브랜드 Azure AD 로그인 페이지로 바로 가속화 하는 데 사용할 수 있습니다.
1. **Id 공급자 클레임 매핑**아래에서 다음 클레임 매핑 값을 입력 합니다.

    * **사용자 ID**: *oid*
    * **표시 이름**: *이름*
    * **지정 된 이름**: *given_name*
    * **성**: *family_name*
    * **전자 메일**: *unique_name*

1. Kattintson a **Mentés** gombra.

### <a name="add-the-facebook-identity-provider"></a>Facebook id 공급자 추가

1. **Id 공급자**를 선택한 다음 **Facebook**을 선택 합니다.
1. **이름을**입력 합니다. 예: *Facebook*.
1. **클라이언트 ID**에 대해 이전에 만든 Facebook 응용 프로그램의 앱 ID를 입력 합니다.
1. **클라이언트 암호**의 경우 기록한 앱 암호를 입력 합니다.
1. Kattintson a **Mentés** gombra.

## <a name="update-the-user-flow"></a>사용자 흐름 업데이트

필수 구성 요소의 일부로 완료 한 자습서에서는 등록 및 로그인에 *B2C_1_signupsignin1*사용자 흐름을 만들었습니다. 이 섹션에서는 *B2C_1_signupsignin1* 사용자 흐름에 id 공급자를 추가 합니다.

1. **사용자 흐름 (정책)** 을 선택 하 고 *B2C_1_signupsignin1* 사용자 흐름을 선택 합니다.
2. **Id 공급자**를 선택 하 고 추가한 **Facebook** 및 **Contoso Azure AD** id 공급자를 선택 합니다.
3. Kattintson a **Mentés** gombra.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 만든 사용자 흐름의 개요 페이지에서 **사용자 흐름 실행**을 선택 합니다.
1. **응용 프로그램**의 경우 이전에 등록 한 *webapp1* 이라는 웹 응용 프로그램을 선택 합니다. **회신 URL** 에 `https://jwt.ms`표시 되어야 합니다.
1. **사용자 흐름 실행**을 선택 하 고 이전에 추가한 id 공급자를 사용 하 여 로그인 합니다.
1. 추가한 다른 id 공급자에 대해 1-3 단계를 반복 합니다.

로그인 작업이 성공 하면 다음과 같이 디코딩된 토큰을 표시 하는 `https://jwt.ms`으로 리디렉션됩니다.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>Következő lépések

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Id 공급자 응용 프로그램 만들기
> * 테 넌 트에 id 공급자 추가
> * 사용자 흐름에 id 공급자 추가

다음으로, 응용 프로그램에서 id 환경의 일부로 사용자에 게 표시 되는 페이지의 UI를 사용자 지정 하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C에서 응용 프로그램의 사용자 인터페이스 사용자 지정](tutorial-customize-ui.md)
