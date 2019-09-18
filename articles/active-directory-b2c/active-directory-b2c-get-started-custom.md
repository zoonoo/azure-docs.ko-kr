---
title: 사용자 지정 정책 시작-Azure Active Directory B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 시작 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b7eb004dbeba499e6f67f98165b72d7ec8615f1b
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065841"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책 시작

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[사용자 지정 정책은](active-directory-b2c-overview-custom.md) Azure Active Directory B2C (Azure AD B2C) 테 넌 트의 동작을 정의 하는 구성 파일입니다. 이 문서에서는 전자 메일 주소와 암호를 사용한 로콜 계정 등록 또는 로그인을 지원하는 사용자 지정 정책을 만듭니다. ID 공급자를 추가하기 위한 환경도 준비합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독에 연결된 [Azure AD B2C 테넌트](tutorial-create-tenant.md)가 아직 없으면 만들어야 합니다.
- Azure AD B2C와 통신할 수 있도록 만든 테 넌 트에 [응용 프로그램을 등록](tutorial-register-applications.md) 합니다.

## <a name="add-signing-and-encryption-keys"></a>서명 및 암호화 키 추가

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 테 넌 트가 포함 된 디렉터리를 선택 합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. 개요 페이지에서 **ID 경험 프레임워크**를 선택합니다.

### <a name="create-the-signing-key"></a>서명 키 만들기

1. **정책 키**, **추가**를 차례로 선택합니다.
2. **옵션**으로는 `Generate`를 선택합니다.
3. **이름**에 `TokenSigningKeyContainer`를 입력합니다. `B2C_1A_` 접두사가 자동으로 추가될 수 있습니다.
4. **키 유형**으로는 **RSA**를 선택합니다.
5. **키 사용**으로는 **서명**을 선택합니다.
6. **만들기**를 클릭합니다.

### <a name="create-the-encryption-key"></a>암호화 키 만들기

1. **정책 키**, **추가**를 차례로 선택합니다.
2. **옵션**으로는 `Generate`를 선택합니다.
3. **이름**에 `TokenEncryptionKeyContainer`를 입력합니다. `B2C_1A`_ 접두사가 자동으로 추가될 수 있습니다.
4. **키 유형**으로는 **RSA**를 선택합니다.
5. **키 사용**에는 **암호화**를 선택합니다.
6. **만들기**를 클릭합니다.

### <a name="create-the-facebook-key"></a>Facebook 키 만들기

[Facebook 애플리케이션 비밀](active-directory-b2c-setup-fb-app.md)이 이미 있을 경우 해당 비밀을 정책 키로 테넌트에 추가합니다. 그렇지 않으면 정책이 유효성 검사를 통과하도록 자리 표시자 값이 있는 키를 만들어야 합니다.

1. **정책 키**, **추가**를 차례로 선택합니다.
2. **옵션**으로는 `Manual`을 선택합니다.
3. **이름**에 `FacebookSecret`을 입력합니다. `B2C_1A_` 접두사가 자동으로 추가될 수 있습니다.
4. **비밀**에 developers.facebook.com의 Facebook 비밀을 입력하거나 자리 표시자인 `0`을 입력합니다. 이 값은 응용 프로그램 ID가 아닌 암호입니다.
5. **키 사용**으로는 **서명**을 선택합니다.
6. **만들기**를 클릭합니다.

## <a name="register-identity-experience-framework-applications"></a>Identity Experience Framework 애플리케이션 등록

Azure AD B2C를 사용하려면 사용자 가입 및 로그인에 사용되고 IdentityExperienceFramework 앱에서 위임된 권한을 갖고 있는 두 개의 애플리케이션 IdentityExperienceFramework(웹앱) 및 ProxyIdentityExperienceFramework(네이티브 앱)를 등록해야 합니다. 로컬 계정은 테넌트에만 존재합니다. 사용자는 고유한 이메일 주소/암호 조합으로 등록하여 테넌트에 등록된 애플리케이션에 액세스합니다.

### <a name="register-the-identityexperienceframework-application"></a>IdentityExperienceFramework 애플리케이션 등록

1. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스** 를 선택 합니다.
1. 검색 상자에 `Azure Active Directory`를 입력합니다.
1. 검색 결과에서 **Azure Active Directory**를 선택합니다.
1. 왼쪽 메뉴의 **관리** 에서 **앱 등록 (레거시)** 를 선택 합니다.
1. **새 애플리케이션 등록**을 선택합니다.
1. **이름**에 `IdentityExperienceFramework`를 입력합니다.
1. **애플리케이션 종류**로 **웹앱/API**를 선택합니다.
1. **로그온 URL**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`을 입력합니다. 여기서 `your-tenant-name`은 Azure AD B2C 테넌트 도메인 이름입니다. 이제 모든 URL은 [b2clogin.com](b2clogin.md)을 사용해야 합니다.
1. **만들기**를 클릭합니다. 생성된 애플리케이션 ID를 복사한 후 나중에 사용할 수 있도록 저장합니다.

### <a name="register-the-proxyidentityexperienceframework-application"></a>ProxyIdentityExperienceFramework 애플리케이션 등록

1. **앱 등록 (레거시)** 에서 **새 응용 프로그램 등록**을 선택 합니다.
2. **이름**에 `ProxyIdentityExperienceFramework`를 입력합니다.
3. **애플리케이션 종류**로 **네이티브**를 선택합니다.
4. **리디렉션 URI**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`을 입력합니다. 여기서 `your-tenant-name`는 Azure AD B2C 테넌트입니다.
5. **만들기**를 클릭합니다. 생성된 애플리케이션 ID를 복사한 후 나중에 사용할 수 있도록 저장합니다.
6. 설정 페이지에서 **필요한 권한**, **추가**를 차례로 선택합니다.
7. **API 선택**을 선택 하 고 **IdentityExperienceFramework**를 검색 한 다음 선택을 클릭 **합니다.**
9. **IdentityExperienceFramework 액세스** 옆의 확인란을 선택한 다음 **선택**, **완료**를 차례로 클릭합니다.
10. **권한 부여**을 선택하고 **예**를 선택하여 확인합니다.

## <a name="custom-policy-starter-pack"></a>사용자 지정 정책 시작 팩

사용자 지정 정책은 기술 프로필 및 사용자 경험를 정의 하기 위해 Azure AD B2C 테 넌 트에 업로드 하는 XML 파일 집합입니다. 신속 하 게 진행 하기 위해 몇 가지 미리 작성 된 정책과 함께 시작 팩을 제공 합니다. 이러한 각 시작 팩에는 설명 된 시나리오를 수행 하는 데 필요한 최소 수의 기술 프로필 및 사용자 경험 포함 되어 있습니다.

- **Localaccounts** -로컬 계정만 사용 하도록 설정 합니다.
- 사회 보장 **계정** -소셜 (또는 페더레이션) 계정만 사용할 수 있습니다.
- **Socialandlocalaccounts** -로컬 계정과 소셜 계정을 모두 사용할 수 있습니다.
- **SocialAndLocalAccountsWithMFA** -소셜, 로컬 및 multi-factor authentication 옵션을 사용 하도록 설정 합니다.

시작 팩 각각에는 다음이 포함됩니다.

- **기본 파일** -기본 파일을 수정 해야 합니다. 예제: *Trustframeworkbase.xml*
- **확장 파일** -이 파일은 대부분의 구성이 변경 되는 위치입니다. 예제: *Trustframeworkextensions.xml*
- **신뢰 당사자 파일** -응용 프로그램에서 호출 하는 작업 관련 파일입니다. 예를 들면 다음과 같습니다. *Signu.xml*, *profileedit*.xml, *passwordreset* .xml

이 문서에서는 **Socialandlocalaccounts** 시작 팩에서 XML 사용자 지정 정책 파일을 편집 합니다. XML 편집기가 필요한 경우 간단한 크로스 플랫폼 편집기를 [Visual Studio Code](https://code.visualstudio.com/download)사용해 보세요.

### <a name="get-the-starter-pack"></a>시작 팩 가져오기

GitHub에서 사용자 지정 정책 시작 팩을 가져온 다음, Azure AD B2C 테 넌 트 이름으로 SocialAndLocalAccounts 스타터 pack의 XML 파일을 업데이트 합니다.

1. [.Zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) 하거나 리포지토리를 복제 합니다.

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. 모든 파일의 `yourtenant` **socialandlocalaccounts** 디렉터리에서 문자열을 Azure AD B2C 테 넌 트의 이름으로 바꿉니다.

    예를 들어 B2C 테 넌 트의 이름이 *contosotenant*인 경우의 `yourtenant.onmicrosoft.com` `contosotenant.onmicrosoft.com`모든 인스턴스는가 됩니다.

### <a name="add-application-ids-to-the-custom-policy"></a>사용자 지정 정책에 애플리케이션 ID 추가

확장 파일 *TrustFrameworkExtensions.xml*에 애플리케이션 ID를 추가합니다.

1. 을 `SocialAndLocalAccounts/` 열고 **`TrustFrameworkExtensions.xml`** 요소`<TechnicalProfile Id="login-NonInteractive">`를 찾습니다.
1. 의 `IdentityExperienceFrameworkAppId` 두 인스턴스를 앞에서 만든 IdentityExperienceFramework 응용 프로그램의 응용 프로그램 ID로 바꿉니다.
1. 의 `ProxyIdentityExperienceFrameworkAppId` 두 인스턴스를 앞에서 만든 ProxyIdentityExperienceFramework 응용 프로그램의 응용 프로그램 ID로 바꿉니다.
1. 파일을 저장합니다.

## <a name="upload-the-policies"></a>정책 업로드

1. Azure Portal에서 B2C 테 넌 트에서 **Id 경험 프레임 워크** 메뉴 항목을 선택 합니다.
1. **사용자 지정 정책 업로드**를 선택 합니다.
1. 이 순서로 정책 파일을 업로드 합니다.
    1. *Trustframeworkbase.xml*
    1. *Trustframeworkextensions.xml*
    1. *Signu.xml*
    1. *ProfileEdit .xml*
    1. *PasswordReset .xml*

파일을 업로드 하면 Azure에서 각에 접두사 `B2C_1A_` 를 추가 합니다.

> [!TIP]
> Xml 편집기에서 유효성 검사를 지 원하는 경우 시작 팩의 `TrustFrameworkPolicy_0.3.0.0.xsd` 루트 디렉터리에 있는 xml 스키마에 대해 파일의 유효성을 검사 합니다. 업로드하기 전에 XML 스키마 유효성 검사가 오류를 식별합니다.

## <a name="test-the-custom-policy"></a>사용자 지정 정책 테스트

1. **사용자 지정 정책**에서 **B2C_1A_signup_signin**를 선택 합니다.
1. 사용자 지정 정책의 개요 페이지에서 **응용 프로그램 선택** 에 대해 이전에 등록 한 *webapp1* 이라는 웹 응용 프로그램을 선택 합니다.
1. **회신 URL** 이 `https://jwt.ms`인지 확인 합니다.
1. **지금 실행**을 선택합니다.
1. 전자 메일 주소를 사용 하 여 등록 합니다.
1. **지금 실행** 을 선택 합니다.
1. 동일한 계정으로 로그인하여 올바르게 구성되었는지 확인합니다.

## <a name="add-facebook-as-an-identity-provider"></a>Facebook을 ID 공급자로 추가

1. Facebook [계정을 사용 하 여 등록 및 로그인 설정](active-directory-b2c-setup-fb-app.md) 의 단계를 완료 하 여 facebook 응용 프로그램을 구성 합니다.
1. 파일에서 의`client_id` 값을 Facebook 응용 프로그램 ID로 바꿉니다. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`**

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. 테넌트에 *TrustFrameworkExtensions.xml* 파일을 업로드합니다.
1. **사용자 지정 정책**에서 **B2C_1A_signup_signin**를 선택 합니다.
1. **지금 실행** 을 선택 하 고 facebook을 선택 하 여 facebook에 로그인 하 고 사용자 지정 정책을 테스트 합니다. 또는 등록 된 응용 프로그램에서 직접 정책을 호출 합니다.

## <a name="next-steps"></a>다음 단계

다음으로, id 공급자로 Azure Active Directory (Azure AD)를 추가 해 봅니다. 이 시작 가이드에 사용 된 기본 파일에는 Azure AD와 같은 다른 id 공급자를 추가 하는 데 필요한 일부 콘텐츠가 이미 포함 되어 있습니다.

및 id 공급자로 Azure AD를 설정 하는 방법에 대 한 자세한 내용은 [Active Directory B2C 사용자 지정 정책을 사용 하 여 Azure Active Directory 계정으로 등록 및 로그인 설정](active-directory-b2c-setup-aad-custom.md)을 참조 하세요.
