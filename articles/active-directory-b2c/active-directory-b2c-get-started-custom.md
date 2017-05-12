---
title: "Azure Active Directory B2C: 사용자 지정 정책 시작 | Microsoft Docs"
description: "Azure Active Directory B2C 사용자 지정 정책을 사용하여 시작하는 방법에 대한 항목"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 93fc922433f0ba60ee438db8d4dcb14527c208fc
ms.contentlocale: ko-kr
ms.lasthandoff: 05/02/2017

---
# <a name="azure-active-directory-b2c-getting-started-with-custom-policies"></a>Azure Active Directory B2C: 사용자 지정 정책 시작

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서의 단계를 완료한 후에 사용자 지정 정책은 전자 메일 및 암호를 사용하는 "로컬 계정" 등록 또는 로그인을 지원합니다. 또한 추가 ID 공급자(예: Facebook 또는 Azure AD)를 추가하기 위한 환경을 준비합니다.  Azure AD B2C의 ID 경험 엔진 및 여러 필수적인 개념을 사용하도록 시작하기 전에 이러한 단계를 완료해야 합니다.

## <a name="prerequisites"></a>필수 조건

계속하기 전에 Azure AD B2C 테넌트가 있는지를 확인합니다. Azure AD B2C 테넌트는 모든 사용자, 앱, 정책 등의 컨테이너입니다. Azure AD B2C 테넌트가 없는 경우 [만들어야](active-directory-b2c-get-started.md) 합니다.

### <a name="confirming-your-b2c-tenant"></a>B2C 테넌트 확인

사용자 지정 정책이 비공개 미리 보기 상태이기 때문에 Azure AD B2C 테넌트가 사용자 지정 정책 업로드에 대해 활성화되었는지 확인합니다.

1. [Azure Portal](https://portal.azure.com)에서 [Azure AD B2C 테넌트의 컨텍스트로 전환](active-directory-b2c-navigate-to-b2c-context.md)하고 Azure AD B2C 블레이드를 엽니다.
1. **모든 정책**을 클릭합니다.
1. **업로드 정책**을 사용할 수 있는지 확인합니다.  단추를 비활성화한 경우 AADB2CPreview@microsoft.com을 전자 메일로 보냅니다.

## <a name="set-up-keys-for-your-custom-policy"></a>사용자 지정 정책의 키 설정

사용자 지정 정책을 사용하는 데 필요한 첫 번째 단계는 키를 설정하는 것입니다.

[!INCLUDE [active-directory-b2c-setup-keys-custom.md](../../includes/active-directory-b2c-setup-keys-custom.md)]

## <a name="download-starter-pack-and-modify-policies"></a>시작 팩 다운로드 및 정책 수정

사용자 지정 정책은 Azure AD B2C 테넌트에 업로드해야 하는 일련의 XML 파일입니다. 시작하는 데 사용할 수 있는 시작 팩을 제공합니다. 시작 팩에는 다음과 같은 항목이 포함되어 있습니다.

* 정책의 [기본 파일](active-directory-b2c-overview-custom.md#policy-files) 기본 파일에 몇 가지 수정이 필요합니다.
* 정책의 [확장 파일](active-directory-b2c-overview-custom.md#policy-files)  이 파일은 구성이 대부분 변경되었습니다.

이제 시작하겠습니다.

1. GitHub에서 "Azure AD B2C 사용자 지정 정책 시작 팩"을 다운로드합니다.  [Zip 다운로드](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) 또는 실행

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. `SocialIDPAndLocalAccounts` 폴더를 엽니다.  이 폴더의 기본 파일(`TrustFrameworkBase.xml`)은 로컬 및 사회/회사 계정 모두에 필요한 콘텐츠를 포함합니다. 소셜 콘텐츠는 로컬 계정을 가져오고 실행하기 위한 단계를 방해하지 않습니다.
1. `TrustFrameworkBase.xml`을(를) 엽니다.  XML 편집기를 해야하는 경우 간단한 크로스 플랫폼 편집기인 [Visual Studio Code를 다운로드](https://code.visualstudio.com/download)합니다.
1. 루트 `TrustFrameworkPolicy` 요소에서 `TenantId` 및 `PublicPolicyUri` 특성을 업데이트하여 `{tenantName}`를 Azure AD B2C 테넌트로 바꿉니다.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="{tenantName}.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://{tenantName}.onmicrosoft.com">
    ```

1. 파일을 저장합니다.
1. `TrustFrameworkExtensions.xml`을 열고 `{tenantName}`를 Azure AD B2C 테넌트로 바꿔서 동일하게 변경합니다. 파일을 저장합니다.
1. `SignUpOrSignIn.xml`을 열고 `{tenantName}`를 Azure AD B2C 테넌트로 바꿔서 동일하게 변경합니다. 파일을 저장합니다.

>[!NOTE]
>XML 편집기에서 유효성 검사를 지원하는 경우 시작 팩의 루트 폴더에 있는 `TrustFrameworkPolicy_0.3.0.0.xsd` XML 스키마 파일에 대해 패일의 유효성을 검사하려고 합니다. 업로드하기 전에 XML 스키마 유효성 검사가 오류를 식별합니다.

## <a name="register-policy-engine-applications"></a>정책 엔진 응용 프로그램 등록

Azure AD B2C는 등록 및 로그인 사용자에 대한 엔진에 의해 사용되는 두 개의 추가 응용 프로그램을 등록해야 합니다.

>[!NOTE]
>아래에서 로컬 계정을 사용하여 로그인할 수 있는 PolicyEngine에서 권한을 위임한 PolicyEngine(웹앱) 및 PolicyEngineProxy(네이티브 앱)라는 두 개의 응용 프로그램을 만들었습니다. 로컬 계정을 사용하는 Azure AD B2C 테넌트의 경우에만 이 섹션이 필요합니다.

### <a name="create-the-policy-engine-application"></a>정책 엔진 응용 프로그램 만들기

1. [Azure Portal](https://portal.azure.com)에서 [Azure AD B2C 테넌트의 컨텍스트로 전환합니다](active-directory-b2c-navigate-to-b2c-context.md).
1. `Azure Active Directory` 블레이드(Azure AD B2C 블레이드 아님)를 엽니다. **> 추가 서비스**를 클릭하여 찾을 수 있습니다.
1. **앱 등록**을 선택합니다.
1. **+ 새 응용 프로그램 등록**을 클릭합니다.
   * 이름: `PolicyEngine`
   * 응용 프로그램 형식: `Web app/API`
   * 로그온 URL: `https://login.microsoftonline.com/{tenantName}.onmicrosoft.com`. 여기서 `{tenantName}`는 Azure AD B2C 테넌트입니다.
1. **만들기**를 클릭합니다.
1. 일단 만들게 되면 새로 만든 응용 프로그램 `PolicyEngine`을 선택하고 응용 프로그램 ID를 복사하고 나중에 사용하기 위해 저장합니다.

### <a name="create-the-policy-engine-proxy-application"></a>정책 엔진 프록시 응용 프로그램 만들기

1. **앱 등록**을 선택합니다.
1. **+ 새 응용 프로그램 등록**을 클릭합니다.
   * 이름: `PolicyEngineProxy`
   * 응용 프로그램 형식: `Native`
   * 로그온 URL: `https://login.microsoftonline.com/{tenantName}.onmicrosoft.com`. 여기서 `{tenantName}`는 Azure AD B2C 테넌트입니다.
1. **만들기**를 클릭합니다.
1. 일단 만들게 되면 응용 프로그램 `PolicyEngineProxy`을 선택하고 응용 프로그램 ID를 복사하고 나중에 사용하기 위해 저장합니다.
1. **필요한 사용 권한**, **+ 추가** 및 **API 선택**을 차례로 선택합니다.
1. 이름인 `PolicyEngine`을 검색하고 결과에서 PolicyEngine을 선택한 다음 **선택**을 클릭합니다.
1. `Access PolicyEngine` 옆의 확인란을 선택하고 **선택**을 클릭합니다.
1. **Done**을 클릭합니다.

### <a name="add-the-application-ids-to-your-custom-policy"></a>사용자 지정 정책에 응용 프로그램 ID 추가

로컬 계정을 사용하는 사용자 지정 정책을 만들려면 확장 파일(`TrustFrameworkExtensions.xml`)에 응용 프로그램 ID를 추가해야 합니다.

1. 확장 파일(`TrustFrameworkExtensions.xml`)에서 `<TechnicalProfile Id="login-NonInteractive">` 요소를 찾습니다.
1. `{Policy Engine Proxy Application ID}`의 두 인스턴스를 [만든 정책 엔진 프록시 응용 프로그램](#create-the-policy-engine-proxy-application)의 응용 프로그램 ID로 바꿉니다.
1. `{Policy Engine Application ID}`의 두 인스턴스를 [만든 정책 엔진 응용 프로그램](#create-the-policy-engine-application)의 응용 프로그램 ID로 바꿉니다.
1. 확장 파일을 저장합니다.

## <a name="upload-the-policies-to-your-tenant"></a>테넌트에 정책 업로드

1. [Azure Portal](https://portal.azure.com)에서 [Azure AD B2C 테넌트의 컨텍스트로 전환](active-directory-b2c-navigate-to-b2c-context.md)하고 Azure AD B2C 블레이드를 엽니다.
1. **모든 정책**을 클릭합니다.
1. **정책 업로드**를 선택합니다.

    >[!WARNING]
    >다음과 같은 순서로 사용자 지정 정책 파일을 업로드해야 합니다.

1. `TrustFrameworkBase.xml`을 업로드합니다.
1. `TrustFrameworkExtensions.xml`을 업로드합니다.
1. `SignUpOrSignin.xml`을 업로드합니다.

파일을 업로드하는 경우 이름에는 `B2C_1A_`가 추가됩니다.  기본 제공 정책은 대신 `B2C_1_`으로 시작합니다.

## <a name="test-the-custom-policy-using-run-now"></a>"지금 실행"을 사용하여 사용자 지정 정책 테스트

1. **Azure AD B2C 블레이드**를 열고 **모든 정책**으로 이동합니다.
1. 업로드한 사용자 지정 정책을 선택하고 **지금 실행** 단추를 클릭합니다.
1. 전자 메일 주소를 사용하여 등록할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 시작 가이드에서 사용한 기본 파일은 다른 ID 공급자를 추가하는 데 필요한 내용 중 일부를 포함합니다. Azure AD 계정을 사용하여 로그인을 설정하려면 [여기에서 계속](active-directory-b2c-setup-aad-custom.md)합니다.

## <a name="reference"></a>참조

* **TP(기술 프로필)**은 끝점의 이름, 해당 메타데이터, 해당 프로토콜을 정의하고 ID 경험 엔진이 수행해야 하는 클레임의 교환에 대해 자세히 설명하는 요소입니다.  로컬 계정 로그인은 로컬 계정 로그인을 수행하기 위해 ID 경험 엔진에 의해 사용되는 TechnicalProfile입니다.

