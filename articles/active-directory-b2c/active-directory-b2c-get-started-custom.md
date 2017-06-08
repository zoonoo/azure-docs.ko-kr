---
title: "Azure Active Directory B2C: 사용자 지정 정책 시작 | Microsoft Docs"
description: "Azure Active Directory B2C 사용자 지정 정책을 시작하는 방법"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: joroja;parahk;gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 42824fe10e635257681f62ab1fec9b47abd4294a
ms.contentlocale: ko-kr
ms.lasthandoff: 05/12/2017

---
# <a name="azure-active-directory-b2c-getting-started-with-custom-policies"></a>Azure Active Directory B2C: 사용자 지정 정책 시작

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서의 단계를 완료한 후에 사용자 지정 정책은 전자 메일 및 암호를 사용하는 "로컬 계정" 등록 또는 로그인을 지원합니다. 또한 추가 ID 공급자(예: Facebook 또는 Azure AD)를 추가하기 위한 환경을 준비합니다.  Azure AD B2C의 ID 경험 프레임워크 및 여러 필수적인 개념의 사용을 시작하기 전에 이러한 단계를 완료하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 조건

계속하기 전에 Azure AD B2C 테넌트가 있는지를 확인합니다. Azure AD B2C 테넌트는 모든 사용자, 앱, 정책 등의 컨테이너입니다. Azure AD B2C 테넌트가 없는 경우 [만들어야](active-directory-b2c-get-started.md) 합니다. 모든 개발자 분들에게 계속하기 전에 Azure AD B2C 기본 제공 정책 연습을 완료하고 기본 제공 정책을 사용하여 응용 프로그램을 구성할 것을 적극 권장합니다.  정책 이름을 조금만 변경하여 사용자 지정 정책을 호출하면 응용 프로그램이 두 가지 유형의 정책과 함께 작동합니다.

사용자 지정 정책 편집에 대한 액세스 권한을 얻으려면 테넌트에 유효한 Azure 구독이 연결되어 있어야 합니다.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>B2C 테넌트에 사용자 지정 정책에서 사용할 서명 및 암호화 키 추가

1. Azure AD B2C 테넌트 설정에서 **ID 경험 프레임워크** 블레이드로 이동합니다.
2. **정책 키**를 선택하여 테넌트에 사용 가능한 키를 봅니다.
3. `B2C_1A_TokenSigningKeyContainer`가 없을 경우 만듭니다.
 * **추가**를 클릭합니다.
 * 옵션 > `Generate`
 * 이름 > `TokenSigningKeyContainer` B2C_1A_ 접두사가 자동으로 추가될 수 있습니다.
 * 키 유형 > `RSA`
 * 날짜 - 기본값 사용
 * 키 사용 > `Signature`
 * **만들기**
4. `B2C_1A_TokenEncryptionKeyContainer`가 없을 경우 만듭니다.
 * **추가**를 클릭합니다.
 * 옵션 > `Generate`
 * 이름 > `TokenEncryptionKeyContainer` B2C_1A_ 접두사가 자동으로 추가될 수 있습니다.
 * 키 유형 > `RSA`
 * 날짜 - 기본값 사용
 * 키 사용 > `Encryption`
 * **만들기**
5. `B2C_1A_FacebookSecret`를 만듭니다. Facebook 응용 프로그램 비밀이 이미 있을 경우 해당 비밀을 정책 키로 테넌트에 추가합니다.  그렇지 않으면 정책이 유효성 검사를 통과하도록 자리 표시자 값으로 키를 만들어야 합니다.
 * **추가**를 클릭합니다.
 * 옵션 > `Manual`
 * 이름 > `FacebookSecret` B2C_1A_ 접두사가 자동으로 추가될 수 있습니다.
 * 비밀 > developers.facebook.com의 FacebookSecret 또는 자리 표시자인 "0"을 입력합니다. *Facebook 앱 ID가 아닙니다.*
 * 키 사용 > 서명
 * **만들기**를 클릭하고 생성되었는지 확인합니다.

## <a name="register-identity-experience-framework-applications"></a>ID 경험 프레임워크 응용 프로그램 등록

Azure AD B2C는 등록 및 로그인 사용자에 대한 엔진에 의해 사용되는 두 개의 추가 응용 프로그램을 등록해야 합니다.

>[!NOTE]
>로컬 계정을 사용하여 로그인을 지원하는 IdentityExperienceFramework(웹앱) 및 IdentityExperienceFramework 앱에서 권한을 위임받은 ProxyIdentityExperienceFramework(네이티브 앱)라는 두 개의 응용 프로그램을 만들어야 합니다. 로컬 계정은 테넌트에만 존재합니다. 최종 사용자는 고유한 전자 메일:암호 조합으로 등록하여 테넌트에 등록된 응용 프로그램에 액세스합니다.

### <a name="create-the-identityexperienceframework-application"></a>IdentityExperienceFramework 응용 프로그램 만들기

1. [Azure Portal](https://portal.azure.com)에서 [Azure AD B2C 테넌트의 컨텍스트로 전환합니다](active-directory-b2c-navigate-to-b2c-context.md).
1. `Azure Active Directory` 블레이드(Azure AD B2C 블레이드 아님)를 엽니다. **> 추가 서비스**를 클릭하여 찾을 수 있습니다.
1. **앱 등록**을 선택합니다.
1. **+ 새 응용 프로그램 등록**을 클릭합니다.
   * 이름: `IdentityExperienceFramework`
   * 응용 프로그램 형식: `Web app/API`
   * 로그온 URL: `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`. 여기서 `yourtenant`는 Azure AD B2C 테넌트 도메인 이름입니다.
1. **만들기**를 클릭합니다.
1. 생성이 완료되면 새로 만든 응용 프로그램 `IdentityExperienceFramework`를 선택하고 **속성**을 클릭한 후 응용 프로그램 ID를 복사하고 나중에 사용하기 위해 저장합니다.

### <a name="create-the-proxy-identity-experience-framework-application"></a>프록시 ID 경험 프레임워크 응용 프로그램 만들기

1. **앱 등록**을 선택합니다.
1. **+ 새 응용 프로그램 등록**을 클릭합니다.
   * 이름: `ProxyIdentityExperienceFramework`
   * 응용 프로그램 형식: `Native`
   * 로그온 URL: `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`. 여기서 `yourtenant`는 Azure AD B2C 테넌트입니다.
1. **만들기**를 클릭합니다.
1. 생성이 완료되면 응용 프로그램 `ProxyIdentityExperienceFramework`를 선택하고 **속성**을 클릭한 후 응용 프로그램 ID를 복사하고 나중에 사용하기 위해 저장합니다.
1. **필요한 사용 권한**, **+ 추가** 및 **API 선택**을 차례로 선택합니다.
1. 이름인 `IdentityExperienceFramework`을 검색하고 결과에서 IdentityExperienceFramework를 선택한 다음 **선택**을 클릭합니다.
1. `Access IdentityExperienceFramework` 옆의 확인란을 선택하고 **선택**을 클릭합니다.
1. **Done**을 클릭합니다.
1. **사용 권한 부여**를 클릭하고 **예**를 눌러 확인합니다.

## <a name="download-starter-pack-and-modify-policies"></a>시작 팩 다운로드 및 정책 수정

사용자 지정 정책은 Azure AD B2C 테넌트에 업로드해야 하는 일련의 XML 파일입니다. Microsoft는 사용자의 신속한 진행을 위해 시작 팩을 제공합니다. 아래의 각 시작 팩에서 설명된 대로 시나리오를 구현하려면 최소 기술 프로필 및 사용자 경험이 필요합니다.
 * LocalAccounts - 로컬 계정만 사용할 수 있습니다.
 * SocialAccounts - 소셜(또는 페더레이션) 계정만 사용할 수 있습니다.
 * **SocialAndLocalAccounts** - 이 연습에 사용할 예정입니다.
 * SocialAndLocalAccountsWithMFA - 여기에는 소셜, 로컬 및 MFA 옵션이 포함됩니다.

각 시작 팩에 포함된 항목은 다음과 같습니다.

* 정책의 [기본 파일](active-directory-b2c-overview-custom.md#policy-files) 기본 파일에 몇 가지 수정이 필요합니다.
* 정책의 [확장 파일](active-directory-b2c-overview-custom.md#policy-files)  이 파일은 구성이 대부분 변경되었습니다.
* [신뢰 당사자 파일](active-directory-b2c-overview-custom.md#policy-files) 응용 프로그램에서 호출하는 작업 관련 파일입니다.

>[!NOTE]
>XML 편집기에서 유효성 검사를 지원하는 경우 시작 팩의 루트 폴더에 있는 `TrustFrameworkPolicy_0.3.0.0.xsd` XML 스키마 파일에 대해 패일의 유효성을 검사하려고 합니다. 업로드하기 전에 XML 스키마 유효성 검사가 오류를 식별합니다.

이제 시작하겠습니다.

1. GitHub에서 "active-directory-b2c-custom-policy-starterpack"을 다운로드합니다.  [Zip 다운로드](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) 또는 실행

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. `SocialAndLocalAccounts` 폴더를 엽니다.  이 폴더의 기본 파일(`TrustFrameworkBase.xml`)은 로컬 및 사회/회사 계정 모두에 필요한 콘텐츠를 포함합니다. 소셜 콘텐츠는 로컬 계정을 가져오고 실행하기 위한 단계를 방해하지 않습니다.
3. `TrustFrameworkBase.xml`을(를) 엽니다.  XML 편집기가 필요할 경우 간단한 크로스 플랫폼 편집기인 [Visual Studio Code를 사용](https://code.visualstudio.com/download)해 보세요.
4. 루트 `TrustFrameworkPolicy` 요소에서 `TenantId` 및 `PublicPolicyUri` 특성을 업데이트하여 `yourtenant.onmicrosoft.com`을 Azure AD B2C 테넌트의 도메인 이름으로 바꿉니다.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
>[!NOTE]
>`PolicyId`는 포털에 표시되는 정책 이름이며, 다른 정책 파일에서 이 정책 파일을 참조할 때 사용되는 이름입니다.

5. 파일을 저장합니다.
6. `TrustFrameworkExtensions.xml`을 열고 `yourtenant.onmicrosoft.com`을 Azure AD B2C 테넌트로 바꿔서 동일하게 두 부분을 변경합니다. `<TenantId>` 요소에서 동일한 부분을 바꿔서 총 3번의 변경을 수행합니다.  파일을 저장합니다.
7. `SignUpOrSignIn.xml`을 열고 세 위치의 `yourtenant.onmicrosoft.com`을 Azure AD B2C 테넌트로 바꿔서 동일하게 변경합니다. 파일을 저장합니다.
8. 암호 재설정 및 프로필 편집 파일을 열고 각 파일의 세 위치에서 `yourtenant.onmicrosoft.com`을 Azure AD B2C 테넌트로 바꿔서 동일하게 변경합니다. 파일을 저장합니다.

### <a name="add-the-application-ids-to-your-custom-policy"></a>사용자 지정 정책에 응용 프로그램 ID 추가
확장 파일(`TrustFrameworkExtensions.xml`)에 응용 프로그램 ID를 추가합니다.

1. 확장 파일(`TrustFrameworkExtensions.xml`)에서 `<TechnicalProfile Id="login-NonInteractive">` 요소를 찾습니다.
2. `IdentityExperienceFrameworkAppId`의 두 인스턴스를 이전에 만든 ID 경험 프레임워크의 응용 프로그램 ID로 바꿉니다. 다음은 예제입니다.

```xml
<Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
```

3. `ProxyIdentityExperienceFrameworkAppId`의 두 인스턴스를 이전에 만든 프록시 ID 경험 프레임워크의 응용 프로그램 ID로 바꿉니다.
4. 확장 파일을 저장합니다.

## <a name="upload-the-policies-to-your-tenant"></a>테넌트에 정책 업로드

1. [Azure Portal](https://portal.azure.com)에서 [Azure AD B2C 테넌트의 컨텍스트로 전환](active-directory-b2c-navigate-to-b2c-context.md)하고 Azure AD B2C 블레이드를 엽니다.
2. **ID 경험 프레임워크**를 클릭합니다.
3. **정책 업로드**를 선택하여 정책 파일을 업로드합니다.

    >[!WARNING]
    >다음과 같은 순서로 사용자 지정 정책 파일을 업로드해야 합니다.

1. `TrustFrameworkBase.xml`을 업로드합니다.
2. `TrustFrameworkExtensions.xml`을 업로드합니다.
3. `SignUpOrSignin.xml`을 업로드합니다.
4. 다른 정책 파일을 업로드합니다.

파일이 업로드되면 정책 파일의 이름 앞에 `B2C_1A_`가 추가됩니다.

## <a name="test-the-custom-policy-using-run-now"></a>"지금 실행"을 사용하여 사용자 지정 정책 테스트

1. **Azure AD B2C 설정**을 열고 **ID 경험 프레임워크**로 이동합니다.

>[!NOTE]
>**지금 실행**을 사용하려면 하나 이상의 응용 프로그램이 테넌트에 사전 등록되어 있어야 합니다. 기본 제공 정책 및 사용자 지정 정책을 모두 호출하려면 B2C 또는 ID 경험 프레임워크에서 **응용 프로그램** 메뉴 선택 항목을 사용하여 B2C 테넌트에 응용 프로그램을 등록해야 합니다. 응용 프로그램당 한 번만 등록하면 됩니다. 

Azure AD B2C [시작](active-directory-b2c-get-started.md) 또는 [응용 프로그램 등록](active-directory-b2c-app-registration.md) 문서에서 응용 프로그램을 등록하는 방법을 알아보세요.  

2. 업로드한 RP(신뢰 당사자) 사용자 지정 정책 `B2C_1A_signup_signin`을 선택하고 **지금 실행** 단추를 클릭합니다.


3. 전자 메일 주소를 사용하여 등록할 수 있습니다.
4. 동일한 계정으로 로그인하여 올바르게 구성되어 있는지 확인합니다.

>[!NOTE]
>로그인이 실패하는 것은 일반적으로 잘못 구성된 IdentityExperienceFramework 앱 때문입니다.


## <a name="next-steps"></a>다음 단계

### <a name="add-facebook-as-an-identity-provider"></a>Facebook을 ID 공급자로 추가
Facebook을 설정하려면:
1. [developers.facebook.com에서 Facebook 응용 프로그램을 구성합니다.](active-directory-b2c-setup-fb-app.md)
2. [Azure AD B2C 테넌트에 Facebook 응용 프로그램 비밀을 추가합니다.](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies)
3. `Item Key="client_id"`의 TrustFrameworkExtensions 정책 파일에 Facebook 응용 프로그램 ID를 추가합니다.

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  <Metadata>
  <!--Replace the value of client_id in this technical profile with your the Facebook App ID"-->
    <Item Key="client_id">00000000000000</Item>
```
4. 테넌트에 TrustFrameworkExtensions.xml 정책 파일을 업로드합니다.
5. **지금 실행**을 사용하여 테스트하거나 등록된 응용 프로그램에서 바로 정책을 호출합니다.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Azure Active Directory를 ID 공급자로 추가
이 시작 가이드에서 사용한 기본 파일은 다른 ID 공급자를 추가하는 데 필요한 내용 중 일부를 포함합니다. Azure AD 계정을 사용하여 로그인을 설정하려면 [여기에서 계속](active-directory-b2c-setup-aad-custom.md)합니다.


## <a name="reference"></a>참조

ID 경험 프레임워크를 사용한 Azure AD B2C의 사용자 지정 정책 [개요](active-directory-b2c-overview-custom.md)

