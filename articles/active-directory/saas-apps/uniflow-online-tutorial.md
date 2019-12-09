---
title: '자습서: uniFLOW Online과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 uniFLOW Online 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28313d27-638c-4d50-92ad-d093f2ae9ecf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eb369047574ef76dd31996fd16399380ea027c8
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74823155"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>자습서: uniFLOW Online과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 uniFLOW Online을 통합하는 방법에 대해 알아봅니다. uniFLOW Online을 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 uniFLOW Online에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 uniFLOW Online에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* uniFLOW Online SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* uniFLOW Online은 **SP** 시작 SSO를 지원합니다.

## <a name="adding-uniflow-online-from-the-gallery"></a>갤러리에서 uniFLOW Online 추가

uniFLOW Online이 Azure AD에 통합되도록 구성하려면 갤러리의 uniFLOW Online을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **uniFLOW Online**을 입력합니다.
1. 결과 패널에서 **uniFLOW Online**을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-uniflow-online"></a>uniFLOW Online에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 uniFLOW Online에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 uniFLOW Online의 관련 사용자 간에 연결 관계를 설정해야 합니다.

uniFLOW Online에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[uniFLOW Online SSO 구성](#configure-uniflow-online-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[uniFLOW Online 테스트 사용자 만들기](#create-uniflow-online-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 uniFLOW Online에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **uniFLOW Online** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **로그온 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다.

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.us.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.sg.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.jp.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.au.uniFLOWonline.com`|

    b. **식별자(엔터티 ID)** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다.

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.us.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.sg.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.jp.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.au.uniFLOWonline.com`|

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [uniFLOW Online 클라이언트 지원 팀](mailto:support@nt-ware.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. uniFLOW Online 애플리케이션에는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 하는 특정 형식의 SAML 어설션이 필요합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 uniFLOW Online 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | Name |  원본 특성|
    | -----------| --------------- |
    | displayname | user.displayname |
    | nickname | user.onpremisessamaccountname |

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사한 후 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예: `B.Simon@contoso.com`
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 uniFLOW Online에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **uniFLOW Online**을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-uniflow-online-sso"></a>uniFLOW Online SSO 구성

1. 다른 브라우저 창에서 uniFLOW Online 웹 사이트에 관리자 권한으로 로그온합니다.

1. 왼쪽 탐색 패널에서 **User**(사용자)를 클릭합니다.

    ![uniFLOW Online 구성](./media/uniflow-online-tutorial/configure1.png)

1. **ID 공급자**를 클릭합니다.

    ![uniFLOW Online 구성](./media/uniflow-online-tutorial/configure2.png)

1. **Add identity provider**(ID 공급자 추가)를 클릭합니다.

    ![uniFLOW Online 구성](./media/uniflow-online-tutorial/configure3.png)

1. **ADD IDENTITY PROVIDER**(ID 공급자 추가) 섹션에서 다음 단계를 수행합니다.


    ![uniFLOW Online 구성](./media/uniflow-online-tutorial/configure4.png)

    a. Display name(표시 이름)을 입력합니다(예: *AzureAD SSO*).

    b. **Provider type**(공급자 유형)의 드롭다운에서 **WS-Fed** 옵션을 선택합니다.

    다. **WS-Fed type**(WS-Fed 유형)의 드롭다운에서 **Azure Active Directory** 옵션을 선택합니다.

    d. **저장**을 클릭합니다.

1. **General**(일반) 탭에서 다음 단계를 수행합니다.

    ![uniFLOW Online 구성](./media/uniflow-online-tutorial/configure5.png)

    a. Display name(표시 이름)을 입력합니다(예: *AzureAD SSO*).

    b. **ADGS Federation Metadata**(ADGS 페더레이션 메타데이터)에 대해 **From URL**(URL에서) 옵션을 선택합니다.

    다. Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL** 값을 **Federation Metadata URl**(페더레이션 메타데이터 URl) 텍스트 상자에 붙여넣습니다.

    d. **Identity provider**(ID 공급자)를 **Enabled**(사용)로 선택합니다.

    e. **Automatic user registration**(자동 사용자 등록)을 **Activated**(활성화됨)로 선택합니다.

    f. **저장**을 클릭합니다.

### <a name="create-uniflow-online-test-user"></a>uniFLOW Online 테스트 사용자 만들기

1. 다른 브라우저 창에서 uniFLOW Online 웹 사이트에 관리자 권한으로 로그온합니다.

1. 왼쪽 탐색 패널에서 **User**(사용자)를 클릭합니다.

    ![uniFLOW Online 구성](./media/uniflow-online-tutorial/configure1.png)

1. **Add user**(사용자 추가)를 클릭합니다.

    ![uniFLOW Online 구성](./media/uniflow-online-tutorial/user1.png)

1. **Create user manually**(수동으로 사용자 만들기)를 클릭합니다.

    ![uniFLOW Online 구성](./media/uniflow-online-tutorial/user2.png)

1. **CREATE USER MANUALLY**(수동으로 사용자 만들기) 탭에서 조직의 요구 사항에 따라 필요한 값을 제공합니다.

    ![uniFLOW Online 구성](./media/uniflow-online-tutorial/user3.png)

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 uniFLOW Online 타일을 클릭하면 SSO를 설정한 uniFLOW Online에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD에서 uniFLOW Online 사용해 보기](https://aad.portal.azure.com/)