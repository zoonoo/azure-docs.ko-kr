---
title: '자습서: MyWorkDrive와 Azure Active Directory 통합 | Microsoft 문서'
description: Azure Active Directory 및 MyWorkDrive 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 61173d21c52d061f0d02ab02eb2f1083507983c2
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991665"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>자습서: MyWorkDrive와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 MyWorkDrive를 통합하는 방법에 대해 알아봅니다. Azure AD와 MyWorkDrive를 통합하는 경우 다음을 수행할 수 있습니다.

* MyWorkDrive에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 MyWorkDrive에 자동으로 로그인(Single Sign-on)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 다운로드할 수 있습니다.
* MyWorkDrive SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. MyWorkDrive는 **SP** 및 **IDP** 시작 SSO를 지원합니다.

## <a name="adding-myworkdrive-from-the-gallery"></a>갤러리에서 MyWorkDrive 추가

MyWorkDrive의 Azure AD 통합을 구성하려면 갤러리의 MyWorkDrive를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **MyWorkDrive**를 입력합니다.
1. 결과 패널에서 **MyWorkDrive**를 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

테스트 사용자 **Britta Simon**을 사용하여 MyWorkDrive로 Azure AD SSO를 구성 및 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 MyWorkDrive의 관련 사용자 간에 연결이 형성되어야 합니다.

MyWorkDrive에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[MyWorkDrive SSO 구성](#configure-myworkdrive-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[MyWorkDrive 테스트 사용자 만들기](#create-myworkdrive-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 MyWorkDrive에 만듭니다.
6. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 따릅니다.

1. [Azure Portal](https://portal.azure.com/)의 **MyWorkDrive** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집/연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 페이지에서 다음 필드 값을 입력합니다.

    **회신 URL** 텍스트 상자에 `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<SERVER.DOMAIN.COM>/Account/Login-saml` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 귀사의 MyWorkDrive 서버 호스트 이름을 입력합니다. 예를 들면 다음과 같습니다.
    > 
    > 회신 URL: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > 로그온 URL: `https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > 사용자 고유의 호스트 이름 및 이러한 값에 대한 SSL 인증서를 설정하는 방법을 잘 모르는 경우 [MyWorkDrive 지원 팀](mailto:support@myworkdrive.com)에 문의합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 클립보드에 복사합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>MyWorkDrive SSO 구성

1. 다른 웹 브라우저 창에서 MyWorkDrive에 보안 관리자 권한으로 로그인합니다.

2. 관리자 패널의 MyWorkDrive 서버에서 **ENTERPRISE**를 클릭하고 다음 단계를 수행합니다.

    ![관리자](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. **SAML/ADFS SSO**를 사용하도록 설정합니다.

    b. **SAML-Azure AD** 선택

    다. **Azure 앱 페더레이션 메타데이터 Url** 텍스트 상자에 Azure Portal에서 복사한 **앱 페더레이션 메타데이터 Url** 값을 붙여넣습니다.

    d. 페이지 맨 아래에 있는 **저장**

    > [!NOTE]
    > 추가 정보를 보려면 [MyWorkDrive Azure AD 지원 문서](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/)를 검토하세요.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `Britta Simon`을 입력합니다.  
   1. **사용자 이름** 필드에 username@companydomain.extension을 입력합니다 예: `BrittaSimon@contoso.com`
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 MyWorkDrive에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **MyWorkDrive**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾은 후 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. SAML 어설션에서 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-myworkdrive-test-user"></a>MyWorkDrive 테스트 사용자 만들기

이 섹션에서는 MyWorkDrive에서 Britta Simon이라는 사용자를 만듭니다. MyWorkDrive 플랫폼에서 사용자를 추가하려면 [MyWorkDrive 지원 팀](mailto:support@myworkdrive.com)에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

### <a name="test-sso"></a>SSO 테스트

액세스 패널에서 MyWorkDrive 타일을 선택하면 SSO를 설정한 MyWorkDrive에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)