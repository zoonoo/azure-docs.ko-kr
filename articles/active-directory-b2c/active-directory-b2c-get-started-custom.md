---
title: Azure Active Directory B2C에서 사용자 지정 정책 시작 | Microsoft Docs
description: Azure Active Directory B2C 사용자 지정 정책을 시작하는 방법
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b4ff8b607f9fded02a519b5f2a3abdfeedf93d88
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181784"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책 시작

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[사용자 지정 정책](active-directory-b2c-overview-custom.md)은 Azure AD(Azure Active Directory) B2C 테넌트의 동작을 정의하는 구성 파일입니다. 이 문서에서는 전자 메일 주소와 암호를 사용한 로콜 계정 등록 또는 로그인을 지원하는 사용자 지정 정책을 만듭니다. 또한 Facebook 또는 Azure Active Directory와 같은 ID 공급자를 추가할 수 있도록 환경을 준비합니다.

## <a name="prerequisites"></a>필수 조건

Azure 구독에 연결된 [Azure AD B2C 테넌트](tutorial-create-tenant.md)가 아직 없으면 만들어야 합니다.

## <a name="add-signing-and-encryption-keys"></a>서명 및 암호화 키 추가

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다. 

    ![Azure AD B2C 테넌트로 전환](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. 개요 페이지에서 **ID 경험 프레임워크 - 미리 보기**를 선택합니다.

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

[Facebook 응용 프로그램 비밀](active-directory-b2c-setup-fb-app.md)이 이미 있을 경우 해당 비밀을 정책 키로 테넌트에 추가합니다. 그렇지 않으면 정책이 유효성 검사를 통과하도록 자리 표시자 값이 있는 키를 만들어야 합니다.

1. **정책 키**, **추가**를 차례로 선택합니다.
2. **옵션**으로는 `Manual`을 선택합니다.
3. **이름**에 `FacebookSecret`을 입력합니다. `B2C_1A_` 접두사가 자동으로 추가될 수 있습니다.
4. **비밀**에 developers.facebook.com의 Facebook 비밀을 입력하거나 자리 표시자인 `0`을 입력합니다. 이 값은 응용 프로그램 ID가 아닌 비밀입니다.
5. **키 사용**으로는 **서명**을 선택합니다.
6. **만들기**를 클릭합니다.

## <a name="register-applications"></a>응용 프로그램 등록

Azure AD B2C에서는 사용자 등록 및 로그인에 사용되는 두 개의 응용 프로그램, 즉 IdentityExperienceFramework(웹앱) 및 IdentityExperienceFramework 앱에서 위임된 권한이 있는 ProxyIdentityExperienceFramework(네이티브 앱)를 등록해야 합니다. 로컬 계정은 테넌트에만 존재합니다. 사용자는 고유한 이메일 주소/암호 조합으로 등록하여 테넌트에 등록된 애플리케이션에 액세스합니다.

### <a name="register-the-identityexperienceframework-application"></a>IdentityExperienceFramework 응용 프로그램 등록

1. Azure Portal의 왼쪽 위 모서리에서 **모든 서비스**를 선택하고 **Azure Active Directory**를 검색하여 선택한 다음 **앱 등록**을 선택합니다.
2. **새 응용 프로그램 등록**을 선택합니다.
3. **이름**에 `IdentityExperienceFramework`를 입력합니다.
4. **응용 프로그램 종류**로 **웹앱/API**를 선택합니다.
5. **로그온 URL**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`을 입력합니다. 여기서 `your-tenant-name`은 Azure AD B2C 테넌트 도메인 이름입니다.
6. **만들기**를 클릭합니다. 
7. 생성된 응용 프로그램 ID를 복사한 후 나중에 사용할 수 있도록 저장합니다.

### <a name="register-the-proxyidentityexperienceframework-application"></a>ProxyIdentityExperienceFramework 응용 프로그램 등록

1. **앱 등록**, **새 응용 프로그램 등록**을 차례로 선택합니다.
2. **이름**에 `ProxyIdentityExperienceFramework`를 입력합니다.
3. **응용 프로그램 종류**로 **네이티브**를 선택합니다.
4. **리디렉션 URI**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`을 입력합니다. 여기서 `yourtenant`는 Azure AD B2C 테넌트입니다.
5. **만들기**를 클릭합니다. 생성된 응용 프로그램 ID를 복사한 후 나중에 사용할 수 있도록 저장합니다.
6. 설정 페이지에서 **필요한 권한**, **추가**를 차례로 선택합니다.
7. **API 선택**을 선택합니다.
8. **IdentityExperienceFramework**를 검색하여 선택하고 **선택**을 클릭합니다.
9. **IdentityExperienceFramework 액세스** 옆의 확인란을 선택한 다음 **선택**, **완료**를 차례로 클릭합니다.
10. **권한 부여**을 선택하고 **예**를 선택하여 확인합니다.

## <a name="download-starter-pack-and-modify-policies"></a>시작 팩 다운로드 및 정책 수정

사용자 지정 정책은 Azure AD B2C 테넌트에 업로드해야 하는 일련의 XML 파일입니다. 작업을 빠르게 시작할 수 있도록 이러한 파일이 포함된 시작 팩이 제공됩니다. 다음 목록의 시작 팩 각각에는 설명한 시나리오를 수행하는 데 필요한 최소한의 기술 프로필 및 사용자 경험이 포함되어 있습니다.

- LocalAccounts - 로컬 계정만 사용할 수 있습니다.
- SocialAccounts - 소셜(또는 페더레이션) 계정만 사용할 수 있습니다.
- SocialAndLocalAccounts - 로컬 계정과 소셜 계정을 모두 사용할 수 있습니다.
- SocialAndLocalAccountsWithMFA - 소셜, 로컬 및 Multi-Factor Authentication 옵션을 사용할 수 있습니다.

시작 팩 각각에는 다음이 포함됩니다.

- 기본 파일. 기본 파일에 몇 가지 수정이 필요합니다.
* 확장 파일.  이 파일은 구성이 대부분 변경되었습니다.
* 신뢰 당사자 파일. 응용 프로그램에서 호출하는 작업 관련 파일입니다.

>[!NOTE]
>XML 편집기에서 유효성 검사를 지원하는 경우 시작 팩의 루트 디렉터리에 있는 TrustFrameworkPolicy_0.3.0.0.xsd XML 스키마에 대해 파일의 유효성을 검사합니다. 업로드하기 전에 XML 스키마 유효성 검사가 오류를 식별합니다.

1. [.zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)하거나 다음 코드를 실행합니다.

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

2. SocialAndLocalAccounts 폴더에서 `yourtenant.onmicrosoft.com`을 실제 테넌트 이름으로 바꿔 모든 파일을 편집합니다. 예: `contosoTenant.onmicrosoft.com` XML 편집기가 필요하면 간단한 플랫폼 간 편집기인 [Visual Studio Code를 사용해 보세요](https://code.visualstudio.com/download).

### <a name="add-application-ids-to-the-custom-policy"></a>사용자 지정 정책에 응용 프로그램 ID 추가

확장 파일 *TrustFrameworkExtensions.xml*에 응용 프로그램 ID를 추가합니다.

1. *TrustFrameworkExtensions.xml* 파일을 열어 `<TechnicalProfile Id="login-NonInteractive">` 요소를 찾습니다.
2. `IdentityExperienceFrameworkAppId`의 두 인스턴스를 이전에 만든 ID 경험 프레임워크의 응용 프로그램 ID로 바꿉니다. `ProxyIdentityExperienceFrameworkAppId`의 두 인스턴스를 이전에 만든 프록시 ID 경험 프레임워크 응용 프로그램의 응용 프로그램 ID로 바꿉니다. 다음 예제에서는 변경 후의 **login-NonInteractive** 기술 프로필을 보여 줍니다.

    ![응용 프로그램 ID](./media/active-directory-b2c-get-started-custom/login-NonInteractive.png)

3. 확장 파일을 저장합니다.

## <a name="upload-the-policies"></a>정책 업로드

1. ID 경험 프레임워크의 사용자 지정 정책 페이지에서 **정책 업로드**를 선택합니다.
1. *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml* 파일을 이 순서대로 업로드합니다. 파일이 업로드되면 정책 파일의 이름 앞에 `B2C_1A_`가 추가됩니다.

## <a name="test-the-custom-policy"></a>사용자 지정 정책 테스트

1. 사용자 지정 정책 페이지에서 **B2C_1A_signup_signin**을 선택합니다. 
2. **지금 실행**을 선택합니다.

3. 전자 메일 주소를 사용하여 등록할 수 있습니다.

4. 동일한 계정으로 로그인하여 올바르게 구성되었는지 확인합니다.

## <a name="add-facebook-as-an-identity-provider"></a>Facebook을 ID 공급자로 추가

1. [Facebook 응용 프로그램](active-directory-b2c-setup-fb-app.md)을 구성합니다.
2. *TrustFrameworkExtensions.xml* 파일에서 `client_id`의 값을 Facebook 응용 프로그램 ID로 바꿉니다.

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
3. 테넌트에 *TrustFrameworkExtensions.xml* 파일을 업로드합니다.
4. **지금 실행**을 사용하여 테스트하거나 등록된 응용 프로그램에서 바로 정책을 호출합니다.

## <a name="next-steps"></a>다음 단계

- Azure Active Directory를 ID 공급자로 추가합니다. 이 시작 가이드에서 사용된 기본 파일에는 이미 다른 ID 공급자를 추가하는 데 필요한 내용 일부가 포함되어 있습니다. 로그인 설정에 대한 자세한 내용은 [Active Directory B2C 사용자 지정 정책을 사용하여 Azure Active Directory 계정을 통한 등록 및 로그인 설정](active-directory-b2c-setup-aad-custom.md) 문서를 참조하세요.
