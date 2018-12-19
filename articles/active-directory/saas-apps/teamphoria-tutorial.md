---
title: '자습서: Teamphoria와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Teamphoria 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: jeedes
ms.openlocfilehash: 554930b18a271a677aeb5e82c3e62a94965a8e7f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439612"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>자습서: Teamphoria와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Teamphoria를 통합하는 방법에 대해 알아봅니다.

Teamphoria를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Teamphoria에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Teamphoria에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](../manage-apps/what-is-single-sign-on.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

Teamphoria와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Teamphoria Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.
 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Teamphoria 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-teamphoria-from-the-gallery"></a>갤러리에서 Teamphoria 추가
Teamphoria의 Azure AD 통합을 구성하려면 갤러리의 Teamphoria를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Teamphoria를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Active Directory][1]

1. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![애플리케이션][2]
    
1. 대화 상자 위쪽에 있는 **추가** 단추를 클릭합니다.

    ![애플리케이션][3]

1. 검색 상자에 **Teamphoria**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/teamphoria-tutorial/tutorial_teamphoria_search.png)

1. 결과 창에서 **Teamphoria**를 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Teamphoria에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Teamphoria 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Teamphoria의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

Teamphoria에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
1. **[Teamphoria 테스트 사용자 만들기](#creating-a-teamphoria-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Teamphoria에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Teamphoria 응용 프로그램에서 Single Sign-On을 구성합니다.

**Teamphoria에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Teamphoria** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

1. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

1. **Teamphoria 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/tutorial_teamphoria_url.png)

    **로그온 URL** 텍스트 상자에서 `https://<sub-domain>.teamphoria.com/login` 패턴을 사용하여 URL을 입력합니다.   

    > [!NOTE] 
    > 로그온 URL 값은 실제 값이 아닙니다. 이 값은 실제 로그온 URL로 업데이트해야 합니다. 로그온 URL을 가져오려면 [Teamphoria 클라이언트 지원 팀](https://www.teamphoria.com/)에 문의합니다.

1. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서를 저장합니다.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/tutorial_teamphoria_certificate.png)

1. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/tutorial_general_400.png)

1. **Teamphoria 구성** 섹션에서 **Teamphoria 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/tutorial_teamphoria_configure.png)

1. **Teamphoria** 측에서 Single Sign-On을 구성하려면 관리자 권한으로 Teamphoria 응용 프로그램에 로그인합니다.

1. 왼쪽 도구 모음에서 **관리 설정** 옵션으로 이동하고 구성 탭 아래에서 **SINGLE SIGN-ON**을 클릭하여 SSO 구성 창을 엽니다.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/admin_sso_configure.png)

1. 오른쪽 위 모서리에서 **새 ID 공급자 추가** 옵션을 클릭하여 SSO에 대한 설정을 추가하기 위한 양식을 엽니다.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/add_new_identity_provider.png)

1. 아래에서 설명한 대로 필드에 세부 정보를 입력합니다.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **표시 이름**: 관리 페이지에서 플러그 인의 표시 이름을 입력합니다.

    나. **단추 이름**: SSO를 통한 로그인에 대해 로그인 페이지에 표시하는 탭의 이름입니다.

    다. **인증서**: 메모장에서 Azure Portal에서 이전에 다운로드한 인증서를 열고 동일한 내용을 복사하고 상자에 붙여 넣습니다.

    d. **진입점**: Azure Portal에서 이전에 복사한 **SAML Single Sign-On 서비스 URL**을 붙여 넣습니다.

    e. 옵션을 **ON**으로 전환하고 **저장**을 클릭합니다.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 "Britta Simon"이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/teamphoria-tutorial/create_aaduser_01.png) 

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/teamphoria-tutorial/create_aaduser_02.png) 

1. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/teamphoria-tutorial/create_aaduser_03.png)

1. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/teamphoria-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="creating-a-teamphoria-test-user"></a>Teamphoria 테스트 사용자 만들기

Azure AD 사용자가 Teamphoria에 로그인할 수 있도록 하려면 Teamphoria로 프로비전되어야 합니다. Teamphoria의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. Teamphoria 회사 사이트에 관리자 권한으로 로그인합니다.

1. 왼쪽 도구 모음에서 **관리자** 설정을 클릭하고 **관리** 탭 아래에서 **사용자**를 클릭하여 사용자에 대한 관리 페이지를 엽니다.

    ![직원 추가](./media/teamphoria-tutorial/admin_manage_users.png)

1. **수동 초대** 옵션을 클릭합니다.

    ![피플 초대](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. 이 페이지에서 다음 작업을 수행합니다.
    
    ![피플 초대](./media/teamphoria-tutorial/manual_user_invite.png)

    a. **전자 메일 주소** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    나. **이름** 텍스트 상자에 **Britta**를 입력합니다.

    다. **성** 텍스트 상자에 **Simon**을 입력합니다.

    d. **1 사용자 초대**를 클릭합니다. 사용자는 시스템에서 생성할 초대를 수락해야 합니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Teamphoria에 대한 액세스를 허용합니다.

![사용자 할당][200]

**Britta Simon을 Teamphoria에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음, 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201]

1. 응용 프로그램 목록에서 **Teamphoria**를 선택합니다.

    ![Configure Single Sign-On](./media/teamphoria-tutorial/tutorial_teamphoria_app.png) 

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202]

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/teamphoria-tutorial/tutorial_general_203.png
