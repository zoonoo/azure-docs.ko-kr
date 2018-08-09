---
title: '자습서: GitHub와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 GitHub 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 7f932346cdfd6363bd76f8167b8598d3a1199de3
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427791"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>자습서: GitHub와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 GitHub를 통합하는 방법에 대해 알아봅니다.

GitHub를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- GitHub에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 GitHub에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

GitHub와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- GitHub Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 GitHub 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-github-from-the-gallery"></a>갤러리에서 GitHub 추가
Azure AD에 GitHub 통합을 구성하려면 갤러리의 GitHub를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 GitHub를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

1. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
1. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

1. 검색 상자에 **GitHub**를 입력하고 결과 패널에서 **GitHub**를 선택한 다음, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 GitHub](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 GitHub에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 GitHub 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 GitHub의 관련 사용자 간에 연결이 설정되어야 합니다.

GitHub에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
1. **[GitHub 테스트 사용자 만들기](#create-a-github-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 GitHub에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 GitHub 응용 프로그램에서 Single Sign-On을 구성합니다.

**GitHub에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **GitHub** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

1. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/github-tutorial/tutorial_github_samlbase.png)

1. **GitHub 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![GitHub 도메인 및 URL Single Sign-On 정보](./media/github-tutorial/tutorial_github_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://github.com/orgs/<entity-id>/sso`

    나. **식별자(엔터티 ID)** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다. `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 로그온 URL 및 식별자로 업데이트해야 합니다. 식별자에는 고유한 문자열 값을 사용하는 것이 좋습니다. GitHub 관리자 섹션으로 이동하여 이러한 값을 검색합니다.

1. **사용자 특성** 섹션에서 **사용자 식별자**를 user.mail로 선택합니다.

    ![Configure Single Sign-On](./media/github-tutorial/tutorial_github_attribute_new01.png)

1. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/github-tutorial/tutorial_github_certificate.png) 

1. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/github-tutorial/tutorial_general_400.png)

1. **GitHub 구성** 섹션에서 **GitHub 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![GitHub 구성](./media/github-tutorial/tutorial_github_configure.png) 

1. 다른 웹 브라우저 창에서 GitHub 회사 사이트에 관리자로 로그인합니다.

1. **설정**으로 이동하고 **보안**을 클릭합니다.

    ![설정](./media/github-tutorial/tutorial_github_config_github_03.png)

1. **SAML 인증 사용** 상자를 확인하여 Single Sign-On 구성 필드를 표시합니다. 그런 다음 Single Sign-On URL 값을 사용하여 Azure AD 구성에서 Single Sign-On URL을 업데이트합니다.

    ![설정](./media/github-tutorial/tutorial_github_config_github_13.png)

1. 다음 필드를 구성합니다.

    a. **로그온 URL** 텍스트 상자에 Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 붙여넣습니다.

    나. **발급자** 텍스트 상자에 Azure Portal에서 복사한 **SAML 엔터티 ID** 값을 붙여넣습니다.

    다. Azure Portal에서 다운로드한 인증서 파일의 내용을 메모장에 복사한 다음, **공용 인증서** 텍스트 상자에 붙여넣습니다.

    ![설정](./media/github-tutorial/tutorial_github_config_github_051.png)

1. **SAML 구성 테스트**를 클릭하여 SSO 동안 유효성 검사 실패 또는 오류가 없는지 확인합니다.

    ![설정](./media/github-tutorial/tutorial_github_config_github_06.png)

1. 페이지 맨 아래에 있는 **저장**

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/github-tutorial/create_aaduser_01.png)

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/github-tutorial/create_aaduser_02.png)

1. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/github-tutorial/create_aaduser_03.png)

1. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/github-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-github-test-user"></a>GitHub 테스트 사용자 만들기

이 섹션은 GitHub에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. GitHub는 자동 사용자 프로비전을 지원하며 기본적으로 사용하도록 설정되어 있습니다. 자동 사용자 프로비전 구성 방법에 대한 자세한 내용은 [여기](github-provisioning-tutorial.md)에서 제공합니다.

**사용자를 수동으로 만들어야 할 경우 다음 단계를 수행합니다.**

1. GitHub 회사 사이트에 관리자 권한으로 로그인합니다.

1. **피플**을 클릭합니다.

    ![사람](./media/github-tutorial/tutorial_github_config_github_08.png "사람")

1. **멤버 초대**를 클릭합니다.

    ![사용자 초대](./media/github-tutorial/tutorial_github_config_github_09.png "사용자 초대")

1. **멤버 초대** 대화 상자 페이지에서 다음 단계를 수행합니다.

    a. **전자 메일** 텍스트 상자에 Britta Simon 계정의 전자 메일 주소를 입력합니다.

    ![피플 초대](./media/github-tutorial/tutorial_github_config_github_10.png "피플 초대")

    나. **초대 보내기**를 클릭합니다.

    ![피플 초대](./media/github-tutorial/tutorial_github_config_github_11.png "피플 초대")

    > [!NOTE]
    > Azure Active Directory 계정 보유자는 활성화되기 전에 전자 메일을 받고 링크를 따라 계정을 확인합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 GitHub에 대한 액세스 권한을 부여하여 Britta Simon이 Azure Single Sign-On을 사용하도록 설정합니다.

![사용자 역할 할당][200] 

**Britta Simon을 GitHub에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201]

1. 응용 프로그램 목록에서 **GitHub**를 선택합니다.

    ![응용 프로그램 목록의 GitHub 링크](./media/github-tutorial/tutorial_github_app.png)  

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 GitHub 타일을 클릭하면 GitHub 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/github-tutorial/tutorial_general_01.png
[2]: ./media/github-tutorial/tutorial_general_02.png
[3]: ./media/github-tutorial/tutorial_general_03.png
[4]: ./media/github-tutorial/tutorial_general_04.png

[100]: ./media/github-tutorial/tutorial_general_100.png

[200]: ./media/github-tutorial/tutorial_general_200.png
[201]: ./media/github-tutorial/tutorial_general_201.png
[202]: ./media/github-tutorial/tutorial_general_202.png
[203]: ./media/github-tutorial/tutorial_general_203.png

