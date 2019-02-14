---
title: '자습서: Workplace by Facebook과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Workplace by Facebook 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6cdc7ef8ee991719153f6daed01fbb76f945a7a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56194941"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>자습서: Workplace by Facebook과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Workplace by Facebook을 통합하는 방법에 대해 알아봅니다.
Workplace by Facebook과 Azure AD를 통합하면 다음과 같은 이점이 제공됩니다.

* Azure AD에서는 Workplace by Facebook에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 Workplace by Facebook에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Workplace by Facebook과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Workplace by Facebook Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

> [!NOTE]
> Facebook에는 Workplace Standard(무료)와 Workplace Premium(유료)의 두 가지 제품이 있습니다. 모든 Workplace Premium 테넌트는 비용 또는 필요한 라이선스에 영향을 미치지 않고 SCIM 및 SSO 통합을 구성할 수 있습니다. SSO 및 SCIM은 Workplace Standard에서 사용할 수 없는 합니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Workplace by Facebook이 **SP**에서 시작된 SSO를 지원
* Workplace by Facebook이 **Just-In-Time 프로비전**을 지원
* Workplace by Facebook이 **[자동 사용자 프로비전](workplacebyfacebook-provisioning-tutorial.md)** 을 지원

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>갤러리에서 Workplace by Facebook 추가

Azure AD에 Workplace by Facebook을 통합하도록 구성하려면 갤러리의 Workplace by Facebook을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Workplace by Facebook을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Workplace by Facebook**을 입력하고 결과 패널에서 **Workplace by Facebook**을 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Workplace by Facebook](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Workplace by Facebook에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면, Azure AD 사용자와 Workplace by Facebook의 관련 사용자 간에 링크 관계가 설정되어야 합니다.

Workplace by Facebook에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Workplace by Facebook Single Sign-On 구성](#configure-workplace-by-facebook-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Workplace by Facebook 테스트 사용자 만들기](#create-workplace-by-facebook-test-user)** - Britta Simon의 Azure AD 표현과 연결된 사용자를 Workplace by Facebook에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Workplace by Facebook에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Workplace by Facebook** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Workplace by Facebook 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그온 URL** 텍스트 상자에서 `https://<instancename>.facebook.com` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에 `https://www.facebook.com/company/<instanceID>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. Workplace 커뮤니티에 대한 올바른 값을 보려면 Workplace 회사 대시보드의 인증 페이지를 참조하세요.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **Workplace by Facebook 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-workplace-by-facebook-single-sign-on"></a>Workplace by Facebook Single Sign-On 구성

1. 다른 웹 브라우저 창에서 Workplace by Facebook 회사 사이트에 관리자로 로그인합니다.
  
   > [!NOTE]
   > SAML 인증 프로세스의 일환으로 Workplace는 Azure AD에 매개 변수를 전달하기 위해 최대 2.5KB 크기의 쿼리 문자열을 사용할 수 있습니다.

2. **관리 패널**에서 차례로 **보안** 탭 및 **인증**으로 이동합니다.

3. **SAML 인증** 아래 드롭다운 목록에서 **SSO 전용**을 선택합니다.

4. Azure Portal의 **Workplace by Facebook 구성** 섹션에서 복사한 값을 해당 필드에 입력합니다.

    * **SAML URL** 텍스트 상자에, Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.
    * **SAML 발급자 URL 텍스트 상자**에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.
    * **SAML 로그아웃 리디렉션**(선택 사항)에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.
    * Azure Portal에서 다운로드한 **base-64로 인코딩된 인증서**를 메모장에서 열고, 콘텐츠를 클립보드에 복사한 다음 **SAML 인증서** 텍스트 상자에 붙여 넣습니다.

5. **기본 SAML 구성** 섹션에 나열된 대상 그룹 URL, 받는 사람 URL 및 ACS(Assertion Consumer Service) URL을 입력해야 할 수 있습니다.

6. 섹션 맨 아래로 스크롤하여 **SSO 테스트** 단추를 클릭합니다. Azure AD 로그인 페이지가 있는 팝업 창이 나타납니다. 일반적인 인증처럼 자격 증명을 입력합니다.

    **문제 해결:** Azure AD에서 반환되는 이메일 주소가 로그인한 Workplace 계정과 동일한지 확인합니다.

7. 테스트가 성공적으로 완료되면 페이지 하단으로 스크롤하여 **저장** 단추를 클릭합니다.

8. Workplace를 사용하는 모든 사용자에게 이제 인증을 위한 Azure AD 로그인 페이지가 제공됩니다.

9. **SAML 로그아웃 리디렉션(선택 사항)** -

    Azure AD의 로그아웃 페이지를 가리키는 데 사용할 수 있는 SAML 로그아웃 URL을 선택적으로 구성하도록 선택할 수 있습니다. 이 설정을 사용하도록 설정하고 구성하면 더 이상 사용자가 Workplace 로그아웃 페이지로 이동하지 않습니다. 대신 SAML 로그아웃 리디렉션 설정에 추가된 URL로 사용자가 리디렉션됩니다.

### <a name="configuring-reauthentication-frequency"></a>재인증 빈도 구성

매일, 3일, 1주, 2주, 매달을 주기로 SAML 확인을 요청하거나 SAML 확인을 요청하지 않도록 Workplace를 구성할 수 있습니다.

> [!NOTE]
> 모바일 애플리케이션에서 SAML 확인에 대한 최소값은 일주일로 설정되어 있습니다.

단추를 사용하여 모든 사용자에 대해 강제로 SAML 재설정을 수행할 수도 있습니다. 모든 사용자에 대해 SAML 인증이 필요합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 Workplace by Facebook에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택하고 **Workplace by Facebook**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Workplace by Facebook**을 입력하고 선택합니다.

    ![애플리케이션 목록의 Workplace by Facebook 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-workplace-by-facebook-test-user"></a>Workplace by Facebook 테스트 사용자 만들기

이 섹션에서는 Workplace by Facebook에서 Britta Simon이라는 사용자를 만듭니다. Workplace by Facebook은 기본적으로 사용하도록 설정된 Just-In-Time 프로비전을 지원합니다.

이 섹션에는 사용자의 작업이 없습니다. Workplace by Facebook에 사용자가 없는 경우 Workplace by Facebook에 액세스하려고 하면 새 사용자가 만들어집니다.

>[!Note]
>사용자를 수동으로 만들어야 하는 경우 [Workplace by Facebook 클라이언트 지원 팀](https://workplace.fb.com/faq/)에 문의합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Workplace by Facebook 타일을 클릭하면 SSO를 설정한 Workplace by Facebook에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [사용자 프로비저닝 구성](workplacebyfacebook-provisioning-tutorial.md)
