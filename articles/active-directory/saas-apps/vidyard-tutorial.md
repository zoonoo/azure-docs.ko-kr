---
title: '자습서: Vidyard와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Vidyard 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jeedes
ms.openlocfilehash: 871942db15d6a3cff45584e33b2191e21d2281a0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426458"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>자습서: Azure Active Directory와 Vidyard 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Vidyard를 통합하는 방법에 대해 알아봅니다.

Vidyard를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Vidyard에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Vidyard에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Vidyard와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Vidyard Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Vidyard 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-vidyard-from-the-gallery"></a>갤러리에서 Vidyard 추가
Vidyard의 Azure AD 통합을 구성하려면 갤러리의 Vidyard를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Vidyard를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

1. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]
    
1. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

1. 검색 상자에 **Vidyard**를 입력하고 결과 패널에서 **Vidyard**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Vidyard](./media/vidyard-tutorial/tutorial_vidyard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 Vidyard에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Vidyard 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Vidyard의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Vidyard에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
1. **[Vidyard 테스트 사용자 만들기](#create-a-vidyard-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Vidyard에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Vidyard 애플리케이션에서 Single Sign-On을 구성합니다.

**Vidyard에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Vidyard** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

1. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/vidyard-tutorial/tutorial_vidyard_samlbase.png)

1. **Vidyard 도메인 및 URL** 섹션에서 **IDP** 시작 모드로 응용 프로그램을 구성하려는 경우 다음 단계를 수행합니다.

    ![Vidyard 도메인 및 URL Single Sign-On 정보](./media/vidyard-tutorial/tutorial_vidyard_url2.png)

    a. **식별자** 텍스트 상자에서 `https://secure.vidyard.com/sso/saml/<unique id>/metadata` 패턴을 사용하여 URL을 입력합니다.

    나. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://secure.vidyard.com/sso/saml/<unique id>/consume`

1. **SP** 시작 모드에서 응용 프로그램을 구성하려면 **고급 URL 설정 표시**를 확인하고 다음 단계를 수행합니다.

    ![Vidyard 도메인 및 URL Single Sign-On 정보](./media/vidyard-tutorial/tutorial_vidyard_url1.png)

    **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 자습서 뒷부분에 설명된 실제 식별자, 응답 URL 및 로그온 URL로 이 값을 업데이트하게 됩니다.

1. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/vidyard-tutorial/tutorial_vidyard_certificate.png) 

1. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/vidyard-tutorial/tutorial_general_400.png)

1. **Vidyard 구성** 섹션에서 **Vidyard 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Vidyard 구성](./media/vidyard-tutorial/tutorial_vidyard_configure.png)

1. 다른 웹 브라우저 창에서 Vidyard Software 회사 사이트에 관리자로 로그인합니다.

1. Vidyard 대시보드에서 **그룹** > **보안**을 선택합니다.

    ![Vidyard 구성](./media/vidyard-tutorial/configure1.png)

1. **새 프로필** 탭을 클릭합니다.

    ![Vidyard 구성](./media/vidyard-tutorial/configure2.png)

1. **SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Vidyard 구성](./media/vidyard-tutorial/configure3.png)

    a. **프로필 이름** 텍스트 상자에 일반 프로필 이름을 붙여넣습니다.

    나. **SSO 사용자 로그인 페이지** 값을 복사하여 Azure Portal의 **Vidyard 도메인 및 URL 섹션**에서 **로그온 URL** 텍스트 상자에 붙여넣습니다.

    다. **ACS URL** 값을 복사하여 Azure Portal의 **Vidyard 도메인 및 URL 섹션**에서 **응답 URL** 텍스트 상자에 붙여넣습니다.

    d. **발급자/메타데이터 URL** 값을 복사하여 Azure Portal의 **Vidyard 도메인 및 URL 섹션**에서 **식별자** 텍스트 상자에 붙여넣습니다.

    e. Azure Portal에서 다운로드한 인증서파일을 메모장에서 열고 복사하여 **X.509 인증서** 텍스트 상자에 붙여넣습니다.

    f. Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 **SAML 엔드포인트 URL** 텍스트 상자에 붙여넣습니다.

    g. **확인**을 클릭합니다.

1. Single Sign On 탭에서 기존 프로필 옆에 있는 **할당**을 선택합니다.

    ![Vidyard 구성](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > SSO 프로필을 만든 후 사용자가 Azure를 통해 액세스를 요청할 모든 그룹에 할당합니다. 할당된 그룹 안에 사용자가 없는 경우 Vidyard는 자동으로 사용자 계정을 만들고 실시간으로 역할을 할당합니다.

1. **할당할 수 있는 그룹**에 표시되는 조직 그룹을 선택합니다.

    ![Vidyard 구성](./media/vidyard-tutorial/configure5.png)

1. **현재 할당된 그룹**에서 할당된 그룹을 확인할 수 있습니다. 사용자 조직에 따라 그룹의 역할을 선택하고 **확인**을 클릭합니다.

    ![Vidyard 구성](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > 자세한 내용은 [이 문서](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard)를 참조하세요.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/vidyard-tutorial/create_aaduser_01.png)

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/vidyard-tutorial/create_aaduser_02.png)

1. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/vidyard-tutorial/create_aaduser_03.png)

1. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/vidyard-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-vidyard-test-user"></a>Vidyard 테스트 사용자 만들기

이 섹션은 Vidyard에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. Vidyard는 적시에 프로비전을 지원하며 기본적으로 사용하도록 설정합니다. 이 섹션에 작업 항목이 없습니다. 새 사용자가 아직 없는 경우 Vidyard에 액세스를 시도하는 동안 새로 생성됩니다.
>[!Note]
>사용자를 수동으로 만들어야 하는 경우 [Vidyard 지원 팀](mailto:support@vidyard.com)에 문의하세요.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Vidyard에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Vidyard에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

1. 애플리케이션 목록에서 **Vidyard**를 선택합니다.

    ![애플리케이션 목록의 Vidyard 링크](./media/vidyard-tutorial/tutorial_vidyard_app.png)  

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Vidyard 타일을 클릭하면 Vidyard 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vidyard-tutorial/tutorial_general_01.png
[2]: ./media/vidyard-tutorial/tutorial_general_02.png
[3]: ./media/vidyard-tutorial/tutorial_general_03.png
[4]: ./media/vidyard-tutorial/tutorial_general_04.png

[100]: ./media/vidyard-tutorial/tutorial_general_100.png

[200]: ./media/vidyard-tutorial/tutorial_general_200.png
[201]: ./media/vidyard-tutorial/tutorial_general_201.png
[202]: ./media/vidyard-tutorial/tutorial_general_202.png
[203]: ./media/vidyard-tutorial/tutorial_general_203.png

