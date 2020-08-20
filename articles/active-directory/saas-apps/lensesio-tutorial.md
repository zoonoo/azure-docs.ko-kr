---
title: '자습서: Lenses.io와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Lenses.io 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.openlocfilehash: 0b8d736ab169ad07bd23a21d3a420bb6a044bf01
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88528644"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>자습서: Lenses.io DataOps 포털과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 [Lenses.io](https://lenses.io/) DataOps 포털을 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다. Azure AD와 Lenses.io를 통합하면 다음 작업을 수행할 수 있습니다.

* Lenses.io 포털에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Lenses에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Lenses 포털 인스턴스. Lenses 포털은 [다양한 방법](https://lenses.io/product/deployment/)으로 배포할 수 있습니다.
* SSO(Single Sign-On)를 지원하는 Lenses.io [라이선스](https://lenses.io/product/pricing/)

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Lenses.io에서 **SP** 시작 SSO를 지원합니다.

* Lenses.io가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-lensesio-from-the-gallery"></a>갤러리에서 Lenses.io 추가

Lenses.io가 Azure AD에 통합되도록 구성하려면 갤러리에서 Lenses.io를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Lenses.io**를 입력합니다.
1. 결과 패널에서 **Lesson.ly**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Lenses.io에 대한 Azure AD SSO 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Lenses.io 포털에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Lenses.io의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Lenses.io에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 및 그룹 만들기](#create-an-azure-ad-test-user-and-group)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Lenses.io SSO 구성](#configure-lensesio-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Lenses.io 테스트 그룹 권한 만들기](#create-lensesio-test-group-permissions)** - Lenses.io(권한 부여)에서 B.Simon이 액세스해야 하는 대상을 제어합니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Lenses.io** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<CUSTOMER_LENSES_BASE_URL>` 패턴을 사용하여 URL을 입력합니다. 예: `https://lenses.my.company.com`

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<CUSTOMER_LENSES_BASE_URL>` 패턴을 사용하여 URL을 입력합니다. 예: `https://lenses.my.company.com`

    다. **회신 URL** 텍스트 상자에서 `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client` 패턴을 사용하여 URL을 입력합니다.
    예: `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값은 Lenses 포털 인스턴스의 기본 URL을 기반으로 실제 로그온 URL, 회신 URL 및 식별자를 사용하여 업데이트합니다. 자세한 내용은 [Lenses.io SSO 설명서](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)에서 참조하세요.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Lenses.io 설정** 섹션에서 위의 XML 파일을 사용하여 Azure SSO에 대해 Lenses를 구성합니다.

### <a name="create-an-azure-ad-test-user-and-group"></a>Azure AD 테스트 사용자 및 그룹 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다. B.Simon에 대한 테스트 그룹도 만듭니다. 이것은 Lenses에서 B.Simon의 액세스 권한을 제어하는 데 사용됩니다.
Lenses에서 그룹 멤버 자격 매핑을 권한 부여에 사용하는 방법은 [Lenses SSO 설명서](https://docs.lenses.io/install_setup/configuration/security.html#id3)에서 찾을 수 있습니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

그룹을 만들려면 다음을 수행합니다.
1. **Azure Active Directory**로 돌아가서 **그룹**을 선택합니다.
1. 화면 위쪽에서 **새 그룹**을 선택합니다.
1. **그룹 속성**에서 다음 단계를 수행합니다.
   1. **그룹 유형** 필드에서 `Security`를 선택합니다.
   1. **그룹 이름** 필드에 `LensesUsers`를 입력합니다.
   1. **만들기**를 클릭합니다.
1. `LensesUsers` 그룹을 선택하고 **개체 ID**(예: `f8b5c1ec-45de-4abd-af5c-e874091fb5f7`)를 적어둡니다. 이 ID는 Lenses에서 해당 그룹의 사용자를 [올바른 권한](https://docs.lenses.io/install_setup/configuration/security.html#id3)에 매핑하는 데 사용됩니다.  
   
테스트 사용자에게 그룹을 할당하려면 다음을 수행합니다. 
1. **Azure Active Directory**로 돌아가서 **사용자**를 선택합니다.
1. 테스트 사용자 `B.Simon`을 선택합니다.
1. **그룹**을 선택합니다.
1. 화면 맨 위에서 **멤버 자격 추가**를 선택합니다.
1. `LensesUsers`를 검색하여 선택합니다.
1. **선택**을 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Lenses.io에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Lenses.io**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-lensesio-sso"></a>Lenses.io SSO 구성

**Lenses.io** 포털에서 Single Sign-On을 구성하려면 다운로드한 **페더레이션 메타데이터 XML**을 Lenses 인스턴스에 설치하고 [SSO를 사용하도록 Lenses를 구성](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses)합니다. 

### <a name="create-lensesio-test-group-permissions"></a>Lenses.io 테스트 그룹 권한 만들기

이 섹션에서는 사용자 [생성 섹션](#create-an-azure-ad-test-user-and-group)에서 적어둔 `LensesUsers` 그룹의 **개체 ID**를 사용하여 Lenses에서 그룹을 만듭니다.
`B.Simon`이 Lenses에서 가져야 하는 권한을 할당합니다.
자세한 내용은 [Azure - Lenses 그룹 매핑](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups)에서 찾을 수 있습니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Lenses.io 타일을 클릭하면 SSO를 설정한 Lenses.io 포털에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [ Lenses.io 인스턴스에서 SSO 설정 ](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 Lenses.io 사용해보기](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security의 세션 제어란?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 컨트롤을 사용하여 Lenses.io를 보호하는 방법](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
