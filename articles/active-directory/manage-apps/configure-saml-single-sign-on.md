---
title: Azure Active Directory의 앱에서 사용되는 SAML 기반 SSO(Single Sign-On) 이해
description: Azure Active Directory의 앱에서 사용되는 SAML 기반 SSO(Single Sign-On) 이해
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: f6422c3f751a7aaa430b93ee68ca1a3520ac915f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257695"
---
# <a name="understand-saml-based-single-sign-on"></a>SAML 기반 Single Sign-On 이해

애플리케이션 관리에 대한 [빠른 시작 시리즈](view-applications-portal.md)에서는 애플리케이션용 IdP(ID 공급자)로 Azure AD를 사용하는 방법을 알아보았습니다. 이 문서에서는 Single Sign-On에 사용할 수 있는 SAML 기반 옵션에 대해 자세히 설명합니다. 


## <a name="before-you-begin"></a>시작하기 전에

IdP(ID 공급자)로 Azure AD를 사용하고 SSO(Single Sign-On)를 구성하는 것은 사용 중인 애플리케이션에 따라 간단할 수도 있고 복잡할 수도 있습니다. 몇 가지 작업만 거치면 구성할 수 있는 애플리케이션이 있는 반면 면밀하게 구성해야 하는 애플리케이션이 있습니다. 이 정보를 간단히 확인하려면 애플리케이션 관리에 대한 [빠른 시작 시리즈](view-applications-portal.md)를 살펴보세요. 간단히 구성할 수 있는 애플리케이션을 추가하려면 이 문서를 읽지 않아도 됩니다. SAML 기반 SSO에 사용자 지정 구성을 해야 하는 애플리케이션을 추가하려면 이 문서를 참조하세요.

[빠른 시작 시리즈](add-application-portal-setup-sso.md)에는 Single Sign-On 구성에 관한 문서가 있습니다. 이 문서에서는 앱의 SAML 구성 페이지에 액세스하는 방법을 설명합니다. SAML 구성 페이지는 다섯 개의 섹션으로 되어 있으며 이 문서에서 각 섹션에 대한 세부 정보도 확인할 수 있습니다.

> [!IMPORTANT] 
> **엔터프라이즈 애플리케이션** 에서 애플리케이션을 탐색할 때 **Single Sign-On** 옵션이 표시되지 않는 몇 가지 경우가 있습니다. 
>
> **앱 등록** 을 사용하여 애플리케이션을 등록하면 Single Sign-On 기능은 OIDC OAuth를 기본값으로 사용하도록 구성됩니다. 이 경우 **엔터프라이즈 애플리케이션** 에서 **Single Sign-On** 옵션이 탐색에 표시되지 않습니다. **앱 등록** 을 사용하여 사용자 지정 앱을 추가하면 매니페스트 파일의 옵션을 구성하게 됩니다. 매니페스트 파일에 대한 자세한 내용은 [Azure Active Directory 앱 매니페스트](../develop/reference-app-manifest.md)를 참조하세요. SSO 표준에 대한 자세한 내용은 [Microsoft ID 플랫폼을 사용한 인증 및 권한 부여](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform)를 참조하세요. 
>
> 애플리케이션이 다른 테넌트에 호스트되거나 사용자 계정에 필요한 권한(전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자)이 없는 경우에는 탐색에서 **Single Sign-On** 이 표시되지 않을 수 있습니다. 사용 권한으로 **Single Sign-On** 을 열 수는 있지만 저장할 수 없는 경우가 발생할 수도 있습니다. Azure AD 관리자 역할에 대한 자세한 내용은 https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 의 설명을 참조하세요.


## <a name="basic-saml-configuration"></a>기본 SAML 구성

이 값은 애플리케이션 공급업체에서 받아야 합니다. 필드 값을 추출하려면 수동으로 값을 입력하거나 메타데이터 파일을 업로드할 수 있습니다.

> [!TIP]
> Azure AD와 함께 작동하도록 미리 구성된 앱이 많이 있습니다. Azure AD 테넌트에 앱을 추가할 때 앱 갤러리를 찾아보면 미리 구성된 앱이 나열됩니다. 해당 과정은 [빠른 시작 시리즈](add-application-portal-setup-sso.md)를 참조하세요. 갤러리의 앱에 대한 자세한 단계별 지침이 설명되어 있습니다. 단계에 액세스하려면 빠른 시작 시리즈에 설명된 대로 해당 앱의 SAML 구성 페이지 링크를 클릭하거나, [SaaS 앱 구성 자습서](../saas-apps/tutorial-list.md)의 모든 앱 구성 자습서 목록에서 찾아보세요.

| 기본 SAML 구성 설정 | SP 시작 | idP 시작 | Description |
|:--|:--|:--|:--|
| **ID(엔터티 ID)** | 일부 앱의 경우 필수 | 일부 앱의 경우 필수 | 애플리케이션을 고유하게 식별합니다. Azure AD는 SAML 토큰의 대상 매개 변수로 애플리케이션에 식별자를 보냅니다. 애플리케이션이 식별자의 유효성을 검사해야 합니다. 또한 이 값은 애플리케이션에서 제공하는 모든 SAML 메타데이터 내에서 엔터티 ID로 표시됩니다. 'https://<subdomain>.contoso.com' 패턴을 사용하여 URL을 입력합니다. *이 값은 애플리케이션에서 보낸 **AuthnRequest**(SAML 요청)에서 **Issuer**(발급자) 요소로 찾을 수 있습니다.* |
| **회신 URL** | 필수 | 필수 | 애플리케이션이 SAML 토큰을 수신해야 하는 위치를 지정합니다. 회신 URL은 ACS(Assertion Consumer Service) URL이라고도 합니다. 추가 회신 URL 필드를 사용하여 여러 회신 URL을 지정할 수 있습니다. 예를 들어 여러 하위 도메인에 대해 추가 회신 URL이 필요할 수 있습니다. 또는 테스트를 위해 한 번에 여러 회신 URL(로컬 호스트 및 공용 URL)을 지정할 수 있습니다. |
| **로그온 URL** | 필수 | 지정하지 않음 | 사용자가 이 URL을 열면 서비스 공급자가 Azure AD를 리디렉션하여 사용자를 인증하고 로그온하도록 합니다. Azure AD는 URL을 사용하여 Microsoft 365 또는 Azure AD 내 앱에서 애플리케이션을 시작합니다. 비어 있는 경우, 사용자가 Microsoft 365, Azure AD 내 앱 또는 Azure AD SSO URL에서 애플리케이션을 실행할 때 IdP에서 시작하는 로그온을 수행합니다.|
| **릴레이 상태** | 옵션 | 옵션 | 인증이 완료되면 사용자를 리디렉션할 위치를 애플리케이션에 지정합니다. 일반적으로 이 값은 애플리케이션에 대한 올바른 URL입니다. 그러나 일부 애플리케이션에서는 이 필드를 다르게 사용합니다. 자세한 내용은 애플리케이션 공급 업체에 요청하세요.
| **로그아웃 URL** | 옵션 | 옵션 | SAML 로그아웃 응답을 애플리케이션에 다시 보내는 데 사용됩니다.

## <a name="user-attributes-and-claims"></a>사용자 특성 및 클레임 

사용자가 애플리케이션에 인증할 때 Azure AD는 고유하게 식별하는 사용자에 대한 정보(또는 클레임)를 포함하는 SAML 토큰을 애플리케이션에 발급합니다. 기본적으로 이 정보는 사용자의 사용자 이름, 이메일 주소, 이름 및 성을 포함합니다. 예를 들어 애플리케이션에 사용자 이름이 아닌 특정 클레임 값 또는 **이름** 형식이 필요한 경우 이러한 클레임을 사용자 지정해야 할 수 있습니다. 

> [!IMPORTANT]
> 미리 구성된 앱이 이미 앱 갤러리에 많이 있으므로 사용자 및 그룹 클레임을 설정하는 것에 대해 걱정하지 않아도 됩니다. 앱을 추가하고 구성하는 과정은 [빠른 시작 시리즈](add-application-portal.md)를 참조하세요.


**고유한 사용자 ID(이름 ID)** 식별자 값은 필수 클레임이며 중요합니다. 기본값은 *user.userprincipalname* 입니다. 사용자 ID는 애플리케이션 내에서 각 사용자를 고유하게 식별합니다. 예를 들어 이메일 주소가 사용자 이름 및 고유 식별자 모두인 경우 값을 *user.mail* 로 설정합니다.

SAML 클레임을 사용자 지정하는 방법에 대한 자세한 내용은 [방법: 엔터프라이즈 애플리케이션의 SAML 토큰에서 발급된 클레임 사용자 지정](../develop/active-directory-saml-claims-customization.md)을 참조하세요.

새 클레임을 추가할 수 있습니다. 자세한 내용은 [애플리케이션별 클레임 추가](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims)를 참조하세요. 그룹 클레임을 추가하려면 [그룹 클레임 구성](../hybrid/how-to-connect-fed-group-claims.md)을 참조하세요.


> [!NOTE]
> Azure AD에서 애플리케이션에 대한 SAML 토큰을 사용자 지정하는 다른 방법은 다음 리소스를 참조하세요.
>- Azure Portal를 통해 사용자 지정 역할을 만들려면 [역할 클레임 구성](../develop/active-directory-enterprise-app-role-management.md)을 참조하세요.
>- PowerShell을 통해 클레임을 사용자 지정하려면 [클레임 사용자 지정 - PowerShell](../develop/active-directory-claims-mapping.md)을 참조하세요.
>- 애플리케이션에 대한 선택적 클레임을 구성하도록 애플리케이션 매니페스트를 수정하려면 [선택적 클레임 구성](../develop/active-directory-optional-claims.md)을 참조하세요.
>- 새로 고침 토큰, 액세스 토큰, 세션 토큰 및 ID 토큰에 대 한 토큰 수명 정책을 설정하려면 [토큰 수명 구성](../develop/active-directory-configurable-token-lifetimes.md)을 참조하세요. 또는 Azure AD 조건부 액세스를 통해 인증 세션을 제한하려면 [인증 세션 관리 기능](../conditional-access/howto-conditional-access-session-lifetime.md)을 참조하세요.

## <a name="saml-signing-certificate"></a>SAML 서명 인증서

Azure AD는 인증서를 사용하여 애플리케이션에 보내는 SAML 토큰에 서명합니다. 이 인증서는 Azure AD와 애플리케이션 간의 신뢰를 구성하는데 필요합니다. 인증서 형식에 대한 자세한 내용은 애플리케이션의 SAML 설명서를 참조합니다. 자세한 내용은 [페더레이션된 Single Sign-On 인증서 관리](manage-certificates-for-federated-single-sign-on.md) 및 [SAML 토큰의 고급 인증서 서명 옵션](certificate-signing-options.md)을 참조하세요.

> [!IMPORTANT]
> 미리 구성된 앱이 이미 앱 갤러리에 많이 있으므로 인증서를 자세히 살펴볼 필요가 없습니다. 앱을 추가하고 구성하는 과정은 [빠른 시작 시리즈](add-application-portal.md)를 참조하세요.

Azure AD에서는 Base64 또는 원시 형식의 활성 인증서를 기본 **SAML로 Single Sign-On 설정** 페이지에서 직접 다운로드할 수 있습니다. 또는 애플리케이션 메타데이터 XML 파일을 다운로드하거나 앱의 페더레이션 메타데이터 URL을 사용하여 활성 인증서를 가져올 수 있습니다. 인증서(활성 또는 비활성)를 보거나 만들거나 다운로드 하려면 다음 단계를 수행합니다.

인증서를 확인하기 위해 살펴봐야 할 일반적인 사항은 다음과 같습니다. 
   - *올바른 만료 날짜.* 만료 날짜는 최대 3년 이후까지 구성할 수 있습니다.
   - *올바른 인증서의 활성 상태.* 상태가 **비활성** 이면 상태를 **활성** 으로 변경합니다. 상태를 변경하려면 인증서의 행을 마우스 오른쪽 단추로 클릭하고 **인증서를 활성 상태로 만들기** 를 선택합니다.
   - *올바른 서명 옵션 및 알고리즘*
   - *올바른 알림 이메일*. 활성 인증서의 만료 날짜가 다가오면 Azure AD는 이 필드에 구성된 이메일 주소에 알림을 전송합니다.

인증서를 다운로드해야 하는 경우도 있습니다. 저장할 위치에 주의하세요! 인증서를 다운로드하려면 Base64 형식, 원시 형식 또는 페더레이션 메타데이터 XML에 대한 옵션 중 하나를 선택합니다. 또한 Azure AD는 `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` 형식의 애플리케이션과 관련된 메타데이터에 액세스할 수 있는 **앱 페더레이션 메타데이터 URL** 을 제공합니다.

> [!NOTE]
> https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id} 사용 시 애플리케이션은 렌더링된 XML에 나타나는 바이트 순서 표시를 처리할 수 있어야 합니다. XML 데이터를 검토할 때 바이트 순서 표시는 인쇄할 수 없는 ASCII 문자(»¿)로 표시되고 16진수에서는 EF BB BF로 표시됩니다.

인증서를 변경하려면 편집 단추를 선택합니다. **SAML 서명 인증서** 페이지에서 수행할 수 있는 몇 가지 작업은 다음과 같습니다.
   - 새 인증서 만들기: **새 인증서**, **만료 날짜**, **저장** 을 차례로 선택합니다. 인증서를 활성화하려면 상황에 맞는 메뉴( **...** ), **인증서를 활성 상태로 만들기** 를 차례로 선택합니다.
   - 프라이빗 키 및 pfx 자격 증명을 사용하여 인증서 업로드: **인증서 가져오기** 를 선택한 다음, 해당 인증서를 찾습니다. **PFX 암호** 를 입력한 다음, **추가** 를 선택합니다.  
   - 고급 인증서 서명을 구성합니다. 이 옵션에 대한 자세한 내용은 [고급 인증서 서명 옵션](certificate-signing-options.md)을 참조하세요.
   - 활성 인증서의 만료 날짜가 가까워질 때 추가 사용자에게 알림: **알림 이메일 주소** 필드에 이메일 주소를 입력합니다.

## <a name="set-up-the-application-to-use-azure-ad"></a>Azure AD를 사용하도록 애플리케이션 설정

**설정\<applicationName>** 섹션에는 애플리케이션에서 구성해야 하는 값이 나열되어 있으므로 SAML ID 공급자로 Azure AD를 사용하게 됩니다. 값은 해당 애플리케이션 웹 사이트의 구성 페이지에서 설정할 수 있습니다. 예를 들어 GitHub를 구성하는 경우 github.com 사이트에서 값을 설정합니다. 이미 Azure AD 갤러리에 애플리케이션이 미리 구성되어 있다면 **단계별 지침 보기** 링크가 표시됩니다. 표시되지 않는 경우, 구성 중인 애플리케이션의 설명서를 찾아보아야 합니다. 

**로그인 URL** 및 **로그아웃 URL** 값은 모두 동일한 엔드포인트로 확인됩니다. 이 엔드포인트는 Azure AD 테넌트에 대한 SAML 요청 처리 엔드포인트입니다. 

**Azure AD 식별자** 는 애플리케이션에 발급된 SAML 토큰의 **Issuer**(발급자) 값입니다.

## <a name="test-single-sign-on"></a>Single Sign-On 테스트

Azure AD를 SAML 기반 ID 공급자로 사용하도록 애플리케이션을 구성한 후에는 설정을 테스트하여 계정에 Single Sign-On이 작동하는지 확인할 수 있습니다. 

**테스트** 를 선택한 다음 현재 로그인한 사용자 또는 다른 사용자로 테스트를 선택합니다. 

로그온에 성공하면 사용자 및 그룹을 SAML 애플리케이션에 할당할 준비가 된 것입니다. 축하합니다!

오류 메시지가 표시되면 다음 단계를 수행합니다.

1. 세부 정보를 **어떤 오류인 것 같습니까?** 상자에 복사하고 붙여넣습니다.

    ![해결 지침 가져오기](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. **해결 지침 가져오기** 를 선택합니다. 근본 원인 및 해결 지침이 표시됩니다.  이 예제에서 사용자는 애플리케이션에 할당되지 않았습니다.

3. 해결 지침을 읽은 다음, 문제를 해결해 봅니다.

4. 성공적으로 완료될 때까지 테스트를 다시 실행합니다.

자세한 내용은 [Azure Active Directory에서 SAML 기반 Single Sign-On을 애플리케이션에 디버그](./debug-saml-sso-issues.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

- [애플리케이션 관리에 대한 빠른 시작 시리즈](view-applications-portal.md)
- [애플리케이션에 사용자 또는 그룹 할당](./assign-user-or-group-access-portal.md)
- [자동 사용자 계정 프로비저닝 구성](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [Single Sign-On SAML 프로토콜](../develop/single-sign-on-saml-protocol.md)
