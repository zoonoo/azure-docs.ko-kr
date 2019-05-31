---
title: '자습서: Coralogix와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Coralogix 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0095a825f5582dc795f5bebdcf08be07a92946e
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65900407"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>자습서: Coralogix와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Coralogix를 통합하는 방법에 대해 알아봅니다.
Coralogix를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Coralogix에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 Coralogix에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Coralogix와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독 Azure AD 환경이 없으면 [1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)을 얻을 수 있습니다.
- Coralogix Single Sign-On이 설정된 구독 

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Coralogix는 SP 시작 SSO를 지원합니다.

## <a name="add-coralogix-from-the-gallery"></a>갤러리에서 Coralogix 추가

Coralogix의 Azure AD 통합을 구성하려면 먼저 갤러리의 Coralogix를 관리형 SaaS 앱 목록에 추가해야 합니다.

갤러리에서 Coralogix를 추가하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory** 아이콘을 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Coralogix**를 입력합니다. 결과 패널에서 **Coralogix**를 선택한 다음, **추가** 단추를 선택하여 애플리케이션을 추가합니다.

     ![결과 목록의 Coralogix](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 Britta Simon이라는 테스트 사용자를 기반으로 Coralogix에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Coralogix의 관련 사용자 간에 연결을 설정해야 합니다.

Coralogix에서 Azure AD Single Sign-On을 구성하고 테스트하려면 먼저 다음 구성 요소를 완료해야 합니다.

1. [Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on) - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. [Coralogix Single Sign-On 구성](#configure-coralogix-single-sign-on) - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user) - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. [Coralogix 테스트 사용자 만들기](#create-a-coralogix-test-user) - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Coralogix에 만듭니다.
6. [Single Sign-On 테스트](#test-single-sign-on) - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Coralogix에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Coralogix** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML**을 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 선택하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 대화 상자에서 다음 단계를 수행합니다.

    ![Coralogix 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그온 URL** 상자에 `https://<SUBDOMAIN>.coralogix.com` 패턴의 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에 다음과 같은 URL을 입력합니다.
    
    `https://api.coralogix.com/saml/metadata.xml`

    또는

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > 이 로그온 URL 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트하세요. 이 값을 얻으려면 [Coralogix 클라이언트 지원 팀](mailto:info@coralogix.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 있는 패턴을 참조할 수도 있습니다.

5. Coralogix 애플리케이션에는 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 애플리케이션 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 선택하여 **사용자 특성** 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

6. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 **편집** 아이콘을 사용하여 클레임을 편집합니다. 이전 이미지에서 보여드린 것처럼 SAML 토큰 특성을 구성하는 **새 클레임 추가**를 사용하여 클레임을 추가할 수도 있습니다. 다음 단계를 수행합니다.
    
    a. **편집 아이콘**을 선택하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](./media/coralogix-tutorial/tutorial_usermail.png) ![이미지](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. **이름 식별자 형식 선택** 목록에서 **이메일 주소**를 선택합니다.

    다. **원본 특성** 목록에서 **user.mail**을 선택합니다.

    d. **저장**을 선택합니다.

7. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 선택하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드합니다. 그런 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

8. **Coralogix 설정** 섹션에서 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-coralogix-single-sign-on"></a>Coralogix Single Sign-On 구성

**Coralogix** 쪽에서 Single Sign-On을 구성하려면 Azure Portal에서 다운로드한 **페더레이션 메타데이터 XML**과 복사한 URL을 [Coralogix 지원 팀](mailto:info@coralogix.com)으로 보내야 합니다. 두 항목은 SAML SSO 연결이 양쪽에서 올바르게 설정되도록 보장합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 "brittasimon@yourcompanydomain.extension"을 입력합니다. 예를 들어 이 예에서는 "brittasimon@contoso.com"을 입력할 수 있습니다.

    다. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Coralogix에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Coralogix**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Coralogix**를 선택합니다.

    ![애플리케이션 목록의 Coralogix 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 선택합니다. **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다. 그런 다음, 화면의 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션의 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적합한 역할을 선택합니다. 그런 다음, 화면의 아래쪽에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

### <a name="create-a-coralogix-test-user"></a>Coralogix 테스트 사용자 만들기

이 섹션에서는 Coralogix에서 Britta Simon이라는 사용자를 만듭니다. Coralogix 플랫폼에서 사용자를 추가하려면  [Coralogix 지원 팀](mailto:info@coralogix.com)에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 MyApps 포털을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

MyApps 포털에서 Coralogix 타일을 선택하면 Coralogix에 자동으로 로그인됩니다. MyApps 포털에 대한 자세한 내용은 [MyApps 포털이란?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)을 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory를 사용하여 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

