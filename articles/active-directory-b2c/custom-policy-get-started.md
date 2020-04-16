---
title: 사용자 지정 정책 시작
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 시작하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 856bd6c2a3546a438293e89a0b576e1392d9c6a5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407286"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책 시작

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[사용자 지정 정책은](custom-policy-overview.md) Azure Active Directory B2C(Azure AD B2C) 테넌트의 동작을 정의하는 구성 파일입니다. 이 문서에서는 전자 메일 주소와 암호를 사용한 로콜 계정 등록 또는 로그인을 지원하는 사용자 지정 정책을 만듭니다. ID 공급자를 추가하기 위한 환경도 준비합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 아직 없는 경우 Azure 구독에 연결된 [Azure AD B2C 테넌트를 만듭니다.](tutorial-create-tenant.md)
- Azure AD B2C와 통신할 수 있도록 만든 테넌트에 [응용 프로그램을 등록합니다.](tutorial-register-applications.md)
- [Facebook 계정으로 등록 및 로그인 설정의](identity-provider-facebook.md) 단계를 완료하여 Facebook 응용 프로그램을 구성합니다. 사용자 지정 정책을 사용하는 데 는 Facebook 응용 프로그램이 필요하지 않지만 이 연습에서는 사용자 지정 정책에서 소셜 로그인을 사용하도록 설정하는 데 사용됩니다.

## <a name="add-signing-and-encryption-keys"></a>서명 및 암호화 키 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure 포털에서 **Azure AD B2C를**검색하고 선택합니다.
1. 개요 페이지에서 **정책**에서 ID **환경 프레임워크를**선택합니다.

### <a name="create-the-signing-key"></a>서명 키 만들기

1. **정책 키**, **추가**를 차례로 선택합니다.
1. **옵션**으로는 `Generate`을 선택합니다.
1. **이름**에 `TokenSigningKeyContainer`를 입력합니다. `B2C_1A_` 접두사가 자동으로 추가될 수 있습니다.
1. **키 유형**으로는 **RSA**를 선택합니다.
1. **키 사용**으로는 **서명**을 선택합니다.
1. **만들기**를 선택합니다.

### <a name="create-the-encryption-key"></a>암호화 키 만들기

1. **정책 키**, **추가**를 차례로 선택합니다.
1. **옵션**으로는 `Generate`을 선택합니다.
1. **이름**에 `TokenEncryptionKeyContainer`를 입력합니다. `B2C_1A`_ 접두사가 자동으로 추가될 수 있습니다.
1. **키 유형**으로는 **RSA**를 선택합니다.
1. **키 사용**에는 **암호화**를 선택합니다.
1. **만들기**를 선택합니다.

### <a name="create-the-facebook-key"></a>Facebook 키 만들기

정책 키로 Facebook 애플리케이션의 [앱 시크릿을](identity-provider-facebook.md) 추가합니다. 이 문서의 필수 구성 조건의 일부로 만든 응용 프로그램의 앱 시크릿을 사용할 수 있습니다.

1. **정책 키**, **추가**를 차례로 선택합니다.
1. **옵션**으로는 `Manual`을 선택합니다.
1. **이름**에 `FacebookSecret`를 입력합니다. `B2C_1A_` 접두사가 자동으로 추가될 수 있습니다.
1. **비밀에서,** developers.facebook.com 페이스 북 응용 *프로그램의 응용 프로그램 비밀을* 입력합니다. 이 값은 응용 프로그램 ID가 아닌 비밀입니다.
1. **키 사용**으로는 **서명**을 선택합니다.
1. **만들기**를 선택합니다.

## <a name="register-identity-experience-framework-applications"></a>Identity Experience Framework 애플리케이션 등록

Azure AD B2C는 로컬 계정으로 사용자를 등록하고 로그인하는 데 사용하는 두 개의 응용 프로그램을 등록해야 *합니다.* *ProxyIdentityExperienceFramework* 사용자는 전자 메일 주소 또는 사용자 이름과 암호를 사용하여 테넌트 등록 응용 프로그램에 액세스하여 "로컬 계정"을 만들 수 있습니다. 로컬 계정은 Azure AD B2C 테넌트에만 존재합니다.

Azure AD B2C 테넌트에 이러한 두 응용 프로그램을 한 번만 등록해야 합니다.

### <a name="register-the-identityexperienceframework-application"></a>IdentityExperienceFramework 애플리케이션 등록

Azure AD B2C 테넌트에 응용 프로그램을 등록하려면 **앱 등록(레거시)** 환경 또는 새 통합 **앱 등록(미리 보기)** 환경을 사용할 수 있습니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[애플리케이션](#tab/applications/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Portal에서 **Azure Active Directory**를 검색하고 선택합니다.
1. Azure **Active Directory** 개요 메뉴에서 **관리**에서 **앱 등록(레거시)을 선택합니다.**
1. **새 애플리케이션 등록**을 선택합니다.
1. **이름**에 `IdentityExperienceFramework`를 입력합니다.
1. **애플리케이션 종류**로 **웹앱/API**를 선택합니다.
1. **로그온 URL**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`을 입력합니다. 여기서 `your-tenant-name`은 Azure AD B2C 테넌트 도메인 이름입니다. 이제 모든 URL은 [b2clogin.com](b2clogin.md)을 사용해야 합니다.
1. **만들기**를 선택합니다. 생성된 애플리케이션 ID를 복사한 후 나중에 사용할 수 있도록 저장합니다.

#### <a name="app-registrations-preview"></a>[앱 등록(미리 보기)](#tab/app-reg-preview/)

1. **앱 등록(미리 보기)** 을 선택한 다음, **새 등록**을 선택합니다.
1. **이름**에 `IdentityExperienceFramework`를 입력합니다.
1. **지원되는 계정 유형에서** **이 조직 디렉터리에서 만 계정을**선택합니다.
1. **리디렉션 URI에서** **웹을**선택한 다음 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`Azure `your-tenant-name` AD B2C 테넌트 도메인 이름을 입력합니다.
1. **사용 권한** 아래에서 *openid 및 offline_access 권한에 대한 관리자 동의 허용* 확인란을 선택합니다.
1. **등록**을 선택합니다.
1. 이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID**를 기록합니다.

다음으로 범위를 추가하여 API를 노출합니다.

1. **관리**에서 **API 표시**를 선택합니다.
1. **범위 추가를**선택한 다음 **저장을** 선택하고 기본 응용 프로그램 ID URI를 계속 수락합니다.
1. 다음 값을 입력하여 Azure AD B2C 테넌트에서 사용자 지정 정책 실행을 허용하는 범위를 만듭니다.
    * **범위 이름**:`user_impersonation`
    * **관리자 동의 표시 이름**:`Access IdentityExperienceFramework`
    * **관리자 동의 설명**:`Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. **범위 추가**를 선택합니다.

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>ProxyIdentityExperienceFramework 애플리케이션 등록

#### <a name="applications"></a>[애플리케이션](#tab/applications/)

1. **앱 등록(레거시)에서** **새 응용 프로그램 등록을**선택합니다.
1. **이름**에 `ProxyIdentityExperienceFramework`를 입력합니다.
1. **애플리케이션 종류**로 **네이티브**를 선택합니다.
1. **URI 리디렉션의**경우 `myapp://auth`을 입력합니다.
1. **만들기**를 선택합니다. 생성된 애플리케이션 ID를 복사한 후 나중에 사용할 수 있도록 저장합니다.
1. **설정을**선택한 다음 필수 권한을 선택한 다음 **에 추가를** **선택합니다.**
1. **API 선택을**선택하고 **IdExperienceFramework를**검색하고 선택한 다음 **을 클릭합니다.**
1. **IdentityExperienceFramework 액세스** 옆의 확인란을 선택한 다음 **선택**, **완료**를 차례로 클릭합니다.
1. **권한 부여를**선택한 다음 **예**를 선택하여 확인합니다.

#### <a name="app-registrations-preview"></a>[앱 등록(미리 보기)](#tab/app-reg-preview/)

1. **앱 등록(미리 보기)** 을 선택한 다음, **새 등록**을 선택합니다.
1. **이름**에 `ProxyIdentityExperienceFramework`를 입력합니다.
1. **지원되는 계정 유형에서** **이 조직 디렉터리에서 만 계정을**선택합니다.
1. **리디렉션 URI** 아래에서 드롭다운을 사용하여 **퍼블릭 클라이언트/네이티브(모바일 및 데스크톱)** 를 선택합니다.
1. **URI 리디렉션의**경우 `myapp://auth`을 입력합니다.
1. **사용 권한** 아래에서 *openid 및 offline_access 권한에 대한 관리자 동의 허용* 확인란을 선택합니다.
1. **등록**을 선택합니다.
1. 이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID**를 기록합니다.

그런 다음 응용 프로그램을 공용 클라이언트로 처리해야 한다고 지정합니다.

1. **관리**에서 **인증**을 선택합니다.
1. **새 환경을 체험해 보세요.**(표시되는 경우)를 선택합니다.
1. **고급 설정에서** **응용 프로그램을 공용 클라이언트로 처리(예** 선택)를 사용하도록 설정합니다. **Yes**
1. **저장**을 선택합니다.

이제 *IdentityExperienceFramework* 등록의 앞에서 노출한 API 범위에 대한 권한을 부여합니다.

1. **관리** 아래에서 **API 권한**을 선택합니다.
1. **구성된 사용 권한** 아래에서 **권한 추가**를 선택합니다.
1. 내 **API** 탭을 선택한 다음 **IdExperienceFramework** 응용 프로그램을 선택합니다.
1. **사용 권한에서**이전에 정의한 **user_impersonation** 범위를 선택합니다.
1. **권한 추가**를 선택합니다. 안내에 따라 몇 분 정도 기다린 후 다음 단계를 진행하세요.
1. **(테넌트 이름)에 대한 관리자 동의 허용**을 선택합니다.
1. 현재 로그인된 관리자 계정을 선택하거나 Azure AD B2C 테넌트에서 최소한 *클라우드 애플리케이션 관리자* 역할이 할당된 계정으로 로그인합니다.
1. **수락**을 선택합니다.
1. **새로 고침**을 선택한 다음, 두 범위 모두 **상태** 아래에 "...에 대해 허용됨"이 표시되는지 확인합니다. 권한이 전파되려면 몇 분 정도 걸릴 수 있습니다.

* * *

## <a name="custom-policy-starter-pack"></a>사용자 지정 정책 스타터 팩

사용자 지정 정책은 Azure AD B2C 테넌트에 업로드하여 기술 프로필 및 사용자 여정을 정의하는 XML 파일 집합입니다. 우리는 당신이 빨리 갈 수 있도록 몇 가지 미리 구축 된 정책과 스타터 팩을 제공합니다. 이러한 각 스타터 팩에는 설명된 시나리오를 달성하는 데 필요한 가장 적은 수의 기술 프로필 및 사용자 여정이 포함되어 있습니다.

- **LocalAccounts** - 로컬 계정만 사용할 수 있습니다.
- **SocialAccounts** - 소셜(또는 페더레이션된) 계정만 사용할 수 있습니다.
- **SocialAndLocalAccounts** - 로컬 및 소셜 계정을 모두 사용할 수 있습니다.
- **SocialAndLocalAccountsWithMFA** - 소셜, 로컬 및 다단계 인증 옵션을 활성화합니다.

시작 팩 각각에는 다음이 포함됩니다.

- **기본 파일** - 베이스에 몇 가지 수정이 필요합니다. 예: *트러스트프레임워크베이스.xml*
- **확장 파일** - 이 파일은 대부분의 구성 변경이 이루어지는 곳입니다. 예: *트러스트프레임워크익스텐션.xml*
- **파티 파일 사용** - 응용 프로그램에서 호출한 작업별 파일입니다. 예: *SignUpOrSignin.xml,* *ProfileEdit.xml,* *PasswordReset.xml*

이 문서에서는 **SocialAndLocalAccounts** 시작 팩에서 XML 사용자 지정 정책 파일을 편집합니다. XML 편집기가 필요한 경우 간단한 플랫폼 간 편집기인 [Visual Studio 코드를](https://code.visualstudio.com/download)사용해 보십시오.

### <a name="get-the-starter-pack"></a>스타터 팩 받기

GitHub에서 사용자 지정 정책 시작 팩을 가져옵니다.

1. [.zip 파일을 다운로드하거나](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) 리포지토리를 복제합니다.

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. **SocialAndLocalAccounts** 디렉터리에 있는 모든 파일에서 문자열을 `yourtenant` Azure AD B2C 테넌트의 이름으로 바꿉니다.

    예를 들어 B2C 테넌트의 이름이 *contosotenant인*경우 `yourtenant.onmicrosoft.com` `contosotenant.onmicrosoft.com`의 모든 인스턴스가 됩니다.

### <a name="add-application-ids-to-the-custom-policy"></a>사용자 지정 정책에 애플리케이션 ID 추가

확장 파일 *TrustFrameworkExtensions.xml*에 애플리케이션 ID를 추가합니다.

1. 열고 `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** 요소를 찾을 `<TechnicalProfile Id="login-NonInteractive">`수 있습니다.
1. 두 인스턴스를 `IdentityExperienceFrameworkAppId` 모두 이전에 만든 IdentityExperienceFramework 응용 프로그램의 응용 프로그램 ID로 바꿉습니다.
1. 두 인스턴스를 `ProxyIdentityExperienceFrameworkAppId` 이전에 만든 프록시IdentityExperienceFramework 응용 프로그램의 응용 프로그램 ID로 바꿉습니다.
1. 파일을 저장합니다.

## <a name="upload-the-policies"></a>정책 업로드

1. Azure 포털의 B2C 테넌트에서 **ID 환경 프레임워크** 메뉴 항목을 선택합니다.
1. **사용자 지정 정책 업로드를 선택합니다.**
1. 이 순서로 정책 파일을 업로드합니다.
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *가입OrSignin.xml*
    1. *프로파일편집.xml*
    1. *암호 Reset.xml*

파일을 업로드할 때 Azure는 각각에 접두사를 `B2C_1A_` 추가합니다.

> [!TIP]
> XML 편집기에서 유효성 검사를 지원하는 `TrustFrameworkPolicy_0.3.0.0.xsd` 경우 시작 팩의 루트 디렉터리에 있는 XML 스키마에 대해 파일의 유효성을 검사합니다. 업로드하기 전에 XML 스키마 유효성 검사가 오류를 식별합니다.

## <a name="test-the-custom-policy"></a>사용자 지정 정책 테스트

1. **사용자 지정 정책에서** **B2C_1A_signup_signin**선택합니다.
1. 사용자 지정 정책의 개요 페이지에서 **응용 프로그램 선택하려면** 이전에 등록한 *webapp1이라는* 웹 응용 프로그램을 선택합니다.
1. **회신 URL이** 있는지 `https://jwt.ms`확인합니다.
1. **지금 실행**을 선택합니다.
1. 이메일 주소를 사용하여 등록합니다.
1. 지금 다시 **실행을** 선택합니다.
1. 동일한 계정으로 로그인하여 올바르게 구성되었는지 확인합니다.

## <a name="add-facebook-as-an-identity-provider"></a>Facebook을 ID 공급자로 추가

[필수 구성 에서](#prerequisites)언급 했듯이 *Facebook은* 사용자 지정 정책을 사용할 필요가 없지만 사용자 지정 정책에서 페더레이션 된 소셜 로그인을 활성화하는 방법을 보여 주는 데 사용됩니다.

1. 파일에서 Facebook 응용 프로그램 `client_id` ID의 값을 바꿉입니다. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`**

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. 테넌트에 *TrustFrameworkExtensions.xml* 파일을 업로드합니다.
1. **사용자 지정 정책에서** **B2C_1A_signup_signin**선택합니다.
1. **지금 실행을** 선택하고 Facebook을 선택하여 Facebook에 로그인하고 사용자 지정 정책을 테스트합니다.

## <a name="next-steps"></a>다음 단계

다음으로 Azure Active Directory(Azure AD)를 ID 공급자로 추가해 보십시오. 이 시작 가이드에 사용된 기본 파일에는 Azure AD와 같은 다른 ID 공급자를 추가하는 데 필요한 일부 콘텐츠가 이미 포함되어 있습니다.

Azure AD를 ID 공급자로 설정하고 [등록하고 Active Directory B2C 사용자 지정 정책을 사용하여 Azure Active Directory 계정으로 로그인하는](identity-provider-azure-ad-single-tenant-custom.md)것에 대한 자세한 내용은 참조하세요.
