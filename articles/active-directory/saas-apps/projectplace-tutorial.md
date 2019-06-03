---
title: '자습서: Projectplace와 Azure Active Directory 통합 | Microsoft Docs'
description: 이 자습서에서는 Azure Active Directory 및 Projectplace 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 17fbc6bc4f022a15c34c5ca7b9465be392cdc639
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560638"
---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>자습서: Projectplace와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Projectplace를 통합하는 방법에 대해 알아봅니다.

이렇게 통합하면 다음과 같은 이점이 제공됩니다.

* Azure AD를 사용하여 Projectplace에 액세스할 수 있는 사용자를 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Projectplace에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Projectplace와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)에 가입할 수 있습니다.
* Projectplace Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Projectplace에서 SP 시작 SSO를 지원합니다.

## <a name="add-projectplace-from-the-gallery"></a>갤러리에서 Projectplace 추가

Projectplace가 Azure AD에 통합되도록 설정하려면 갤러리의 Projectplace를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 선택](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션** > **모든 애플리케이션**으로 차례로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 애플리케이션을 추가하려면 창의 위쪽에서 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 선택](common/add-new-app.png)

4. 검색 상자에서 **Projectplace**를 입력합니다. 검색 결과에서 **Projectplace**를 선택한 다음, **추가**를 선택합니다.

     ![검색 결과](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 Britta Simon이라는 테스트 사용자를 사용하여 Projectplace에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On을 사용하도록 설정하려면 Azure AD 사용자와 Projectplace의 해당 사용자 간에 연결 관계를 설정해야 합니다.

Projectplace에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 단계를 완료해야 합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD Single Sign-On을 구성](#configure-azure-ad-single-sign-on)** 합니다.
2. 애플리케이션 쪽에서 **[Projectplace Single Sign-On 구성](#configure-projectplace-single-sign-on)** 합니다.
3. Azure AD Single Sign-On을 테스트할 **[Azure AD 테스트 사용자를 만듭니다](#create-an-azure-ad-test-user)** .
4. 사용자가 Azure AD Single Sign-On을 사용할 수 있도록 **[Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)** 합니다.
5. 사용자의 Azure AD 표현과 연결된 **[Projectplace 테스트 사용자를 만듭니다](#create-a-projectplace-test-user)** .
6. **[Single Sign-On을 테스트](#test-single-sign-on)** 하여 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Projectplace에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Projectplace** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 선택](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 방법 선택](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 선택하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![편집 아이콘](common/edit-urls.png)

4. **기본 SAML 구성** 대화 상자의 **로그온 URL** 상자에서 URL을 다음 패턴으로 입력합니다.

    `https://<company>.projectplace.com`

   ![기본 SAML 구성 대화 상자](common/sp-signonurl.png)
    > [!NOTE]
    > 이 값은 자리 표시자입니다. 실제 로그온 URL을 사용해야 합니다. 이 값을 얻으려면 [Projectplace 지원 팀](https://success.planview.com/Projectplace/Support)에 문의하세요. Azure Portal의 **기본 SAML 구성** 대화 상자에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 요구 사항에 따라 **페더레이션 메타데이터 XML** 옆의 **다운로드** 링크를 선택하고, 해당 인증서를 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **Projectplace 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    1. **로그인 URL**

    1. **Azure AD 식별자**

    1. **로그아웃 URL**

### <a name="configure-projectplace-single-sign-on"></a>Projectplace Single Sign-On 구성

**Projectplace** 쪽에서 Single Sign-On을 구성하려면 다운로드한 **페더레이션 메타데이터 XML**과 Azure Portal에서 복사한 URL을 [Projectplace 지원 팀](https://success.planview.com/Projectplace/Support)으로 보내야 합니다. 이 팀은 SAML SSO 연결이 양쪽에서 제대로 설정되었는지 확인합니다.

>[!NOTE]
>Single Sign-On 구성을 [Projectplace 지원 팀](https://success.planview.com/Projectplace/Support)에서 수행할 수 있습니다. 구성이 완료되는 즉시 알림을 받습니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.

    ![모든 사용자 선택](common/users.png)

2. 다음과 같이 화면의 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 선택](common/new-user.png)

3. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    1. **이름** 상자에 **BrittaSimon**을 입력합니다.
  
    1. **사용자 이름** 상자에서 **BrittaSimon@\<yourcompanydomain>.\<extension>** 을 입력합니다. (예: BrittaSimon@contoso.com)

    1. **암호 표시**를 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.

    1. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure AD Single Sign-On을 사용할 수 있도록 Britta Simon에게 Projectplace에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Projectplace**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Projectplace**를 선택합니다.

    ![애플리케이션 목록](common/all-applications.png)

3. 다음과 같이 왼쪽 창에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 및 그룹 선택](common/users-groups-blade.png)

4. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 선택](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.

6. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택합니다. 화면의 아래쪽에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당**을 선택합니다.

### <a name="create-a-projectplace-test-user"></a>Projectplace 테스트 사용자 만들기

Azure AD 사용자가 Percolate에 로그인할 수 있도록 설정하려면 해당 사용자를 Projectplace에 추가해야 합니다. 수동으로 추가해야 합니다.

사용자 계정을 만들려면 다음 단계를 수행합니다.

1. **Projectplace** 회사 사이트에 관리자 권한으로 로그인합니다.

2. **People**(사람)로 이동한 다음, **Members**(멤버)를 선택합니다.
   
    ![People로 이동한 다음, Members 선택](./media/projectplace-tutorial/ic790228.png "People")

3. **Add Member**(멤버 추가)를 클릭합니다.
   
    ![Add Member 선택](./media/projectplace-tutorial/ic790232.png "Member 추가")

4. **Add Member** 섹션에서 다음 단계를 수행합니다.
   
    ![Add Member 섹션](./media/projectplace-tutorial/ic790233.png "New Members")(새 멤버)
   
    1. **New Members** 텍스트 상자에서 추가하려는 유효한 Azure AD 계정의 이메일 주소를 입력합니다.
   
    1. **보내기**를 선택합니다.

   계정이 활성화되기 전에 해당 계정을 확인하기 위한 링크가 포함된 이메일이 Azure AD 계정 보유자에게 보내집니다.

>[!NOTE]
>또한 Projectplace에서 제공하는 다른 사용자 계정 만들기 도구 또는 API를 사용하여 Azure AD 사용자 계정을 추가할 수 있습니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이제 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트해야 합니다.

액세스 패널에서 Projectplace 타일을 선택하면 SSO를 설정한 Projectplace 인스턴스에 자동으로 로그인됩니다. 자세한 내용은 [My Apps 포털에서 앱 액세스 및 사용](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)을 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [SaaS 애플리케이션과 Azure Active Directory 통합을 위한 자습서](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
