---
title: '자습서: GitHub Enterprise Managed User와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 GitHub Enterprise Managed User 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/15/2021
ms.author: jeedes
ms.openlocfilehash: 6038db88610e1fde8d95c2c31a9bcfa2c5ad5ac2
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904250"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-managed-user"></a>자습서: GitHub Enterprise Managed User와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 GitHub EMU(Enterprise Managed User)를 Azure AD(Azure Active Directory)와 통합하는 방법을 알아봅니다. Azure AD와 GitHub Enterprise Managed User를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 GitHub Enterprise Managed User에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 GitHub Enterprise Managed User에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* GitHub Enterprise Managed User SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* GitHub Enterprise Managed User에서 **SP 및 IDP** 시작 SSO를 지원합니다.
* GitHub Enterprise Managed User에 [**자동** 사용자 프로비저닝](./github-enterprise-managed-user-provisioning-tutorial.md)이 필요합니다.

## <a name="adding-github-enterprise-managed-user-from-the-gallery"></a>갤러리에서 GitHub Enterprise Managed User 추가

GitHub Enterprise Managed User가 Azure AD에 통합되도록 구성하려면 갤러리의 GitHub Enterprise Managed User를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **GitHub Enterprise Managed User** 를 입력합니다.
1. 결과 패널에서 **GitHub Enterprise Managed User** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-managed-user"></a>GitHub Enterprise Managed User에 대한 Azure AD SSO 구성 및 테스트

GitHub Enterprise Managed User에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - AAD 테넌트에서 SAML Single Sign-On을 사용하도록 설정합니다.
1. **[GitHub Enterprise Managed User SSO 구성](#configure-github-enterprise-managed-user-sso)** - GitHub Enterprise에서 Single Sign-On 설정을 구성합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **GitHub Enterprise Managed User** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. 시작하기 전에 엔터프라이즈 URL이 있는지 확인합니다. 아래에 언급된 ENTITY 필드는 EMU 지원 엔터프라이즈 URL의 엔터프라이즈 이름입니다. 예를 들어 https://github.com/enterprises/contoso - **contoso** 가 ENTITY입니다. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 `https://github.com/enterprises/<ENTITY>` 패턴을 사용하여 URL을 입력합니다.
    
    > [!NOTE]
    > 식별자 형식은 애플리케이션의 제안된 형식과 다릅니다. 위의 형식을 따르세요. 또한 **식별자가 후행 슬래시를 포함하지 않는지 확인하세요.
    
    b. **회신 URL** 텍스트 상자에서 `https://github.com/enterprises/<ENTITY>/saml/consume` 패턴을 사용하여 URL을 입력합니다.
    

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://github.com/enterprises/<ENTITY>/sso` 패턴을 사용하여 URL을 입력합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificate-base64-download.png)

1. **GitHub Enterprise Managed User 설정** 섹션에서 아래 URL을 복사하고 아래 GitHub 구성을 위해 저장합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 SSO 설정을 완료하기 위해 GitHub Enterprise Managed User에게 계정을 할당합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **GitHub Enterprise Managed User** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 계정을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. **역할 선택** 대화 상자에서 **엔터프라이즈 소유자** 역할을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다. 다음 자습서에서 계정을 프로비전할 때 계정이 GitHub 인스턴스의 엔터프라이즈 소유자로 할당됩니다. 
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-github-enterprise-managed-user-sso"></a>GitHub Enterprise Managed User SSO 구성

**GitHub Enterprise Managed User** 쪽에서 Single Sign-On을 구성하려면 다음 항목이 필요합니다.

1. 위의 AAD Enterprise Managed User 애플리케이션의 URL, 로그인 URL, Azure AD 식별자 및 로그아웃 URL
1. GitHub Enterprise 첫 번째 관리자의 계정 이름 및 암호. 자격 증명은 GitHub 솔루션 엔지니어링 담당자가 보내는 암호 재설정 이메일을 통해 제공됩니다. 

### <a name="enable-github-enterprise-managed-user-saml-sso"></a>GitHub Enterprise Managed User SAML SSO 사용

이 섹션에서는 위의 AAD에서 제공된 정보를 가져다가 엔터프라이즈 설정에 입력하여 SSO 지원이 가능하도록 설정합니다.

1. https://github.com 로 이동
1. 오른쪽 위 모서리에 있는 Sign In(로그인)을 클릭합니다.
1. 첫 번째 관리자 사용자 계정의 자격 증명을 입력합니다. 로그인 핸들은 다음과 같은 형식이어야 합니다. `<your enterprise short code>_admin`
1. `https://github.com/enterprises/` `<your enterprise name>`로 이동합니다. 이 정보는 솔루션 엔지니어링 담당자가 제공해야 합니다.
1. 왼쪽 탐색 메뉴에서 **설정**, **보안** 를 차례로 선택합니다.
1. **Enable SAML authentication**(SAML 인증 사용) 확인란을 선택합니다.
1. 로그온 URL을 입력합니다. 이 URL은 위의 AAD에서 복사한 로그인 URL입니다.
1. 발급자를 입력합니다. 이 URL은 위의 AAD에서 복사한 Azure AD 식별자입니다.
1. 공용 인증서를 입력합니다. 위에서 다운로드한 Base64 인증서를 열고 이 파일의 텍스트 콘텐츠를 이 대화 상자에 붙여넣습니다.
1. **Test SAML configuration**(SAML 구성 테스트)을 클릭합니다. 그러면 Azure AD 자격 증명으로 로그인하여 SAML SSO가 올바르게 구성되었는지 확인할 수 있는 대화 상자가 열립니다. AAD 자격 증명으로 로그인합니다. 유효성 검사에 성공하면 **Passed: Successfully authenticated your SAML SSO identity**(통과: SAML SSO ID 인증 성공) 메시지를 받게 됩니다.
1. 이 설정을 유지하려면 **저장** 을 클릭합니다.
1. 복구 코드를 안전한 장소에 저장(다운로드, 인쇄 또는 복사)합니다.
1. **SAML 인증 사용** 을 클릭합니다.
1. 이 시점에는 SSO가 있는 계정만 회사에 로그인할 수 있습니다. SSO로 지원되는 계정을 프로비전하려면 프로비저닝에 대한 아래 문서의 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

GitHub Enterprise Managed User는 모든 계정을 자동 사용자 프로비저닝을 통해 **만들어야** 합니다. 자동 사용자 프로비저닝을 구성하는 방법에 대한 자세한 내용은 [여기](./github-enterprise-managed-user-provisioning-tutorial.md)에서 확인할 수 있습니다.
