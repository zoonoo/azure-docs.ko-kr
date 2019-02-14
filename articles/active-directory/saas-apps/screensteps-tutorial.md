---
title: '자습서: ScreenSteps와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 ScreenSteps 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07abc79979bcb557b6513ff16472ff5c95d0e12f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56184519"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>자습서: ScreenSteps와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 ScreenSteps를 통합하는 방법에 대해 알아봅니다.

ScreenSteps를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- ScreenSteps에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 ScreenSteps에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

ScreenSteps와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- ScreenSteps Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 ScreenSteps 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-screensteps-from-the-gallery"></a>갤러리에서 ScreenSteps 추가
ScreenSteps가 Azure AD에 통합되도록 구성하려면 갤러리에서 ScreenSteps를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 ScreenSteps를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

1. **엔터프라이즈 애플리케이션**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]
    
1. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

1. 검색 상자에 **ScreenSteps**를 입력하고 결과 패널에서 **ScreenSteps**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 ScreenSteps](./media/screensteps-tutorial/tutorial_screensteps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 ScreenSteps에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 ScreenSteps 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 ScreenSteps의 관련 사용자 간에 연결이 형성되어야 합니다.

ScreenSteps에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

ScreenSteps에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
1. **[ScreenSteps 테스트 사용자 만들기](#create-a-screensteps-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 ScreenSteps에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 ScreenSteps 애플리케이션에서 Single Sign-On을 구성합니다.

**ScreenSteps에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **ScreenSteps** 애플리케이션 통합 페이지에서 **Single sign-on**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

1. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/screensteps-tutorial/tutorial_screensteps_samlbase.png)

1. **ScreenSteps 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![ScreenSteps 도메인 및 URL Single Sign-On 정보](./media/screensteps-tutorial/tutorial_screensteps_url.png)

    **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<tenantname>.ScreenSteps.com`

    > [!NOTE] 
    > 이 값은 실제 값이 아닙니다. 자습서 뒷부분에 설명된 실제 로그온 URL로 이 값을 업데이트합니다. 

1. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/screensteps-tutorial/tutorial_screensteps_certificate.png) 

1. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/screensteps-tutorial/tutorial_general_400.png)

1. **ScreenSteps 구성** 섹션에서 **ScreenSteps 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![ScreenSteps 구성](./media/screensteps-tutorial/tutorial_screensteps_configure.png) 

1. 다른 웹 브라우저 창에서 ScreenSteps 회사 사이트에 관리자로 로그인합니다.

1. **계정 설정**을 클릭합니다.

    ![계정 관리](./media/screensteps-tutorial/ic778523.png "계정 관리")

1. **Single Sign-On**을 클릭합니다.

    ![원격 인증](./media/screensteps-tutorial/ic778524.png "원격 인증")

1. **Single Sign-on 엔드포인트 만들기**를 클릭합니다.

    ![원격 인증](./media/screensteps-tutorial/ic778525.png "원격 인증")

1. **Single Sign-on 엔드포인트 만들기** 섹션에서 다음 단계를 수행합니다.

    ![인증 엔드포인트 만들기](./media/screensteps-tutorial/ic778526.png "인증 엔드포인트 만들기")
    
    a. **제목** 텍스트 상자에 제목을 입력합니다.
    
    b. **모드** 목록에서 **SAML**을 선택합니다.
    
    다. **만들기**를 클릭합니다.

1. 새 엔드포인트를 **편집**합니다.

    ![엔드포인트 편집](./media/screensteps-tutorial/ic778528.png "엔드포인트 편집")

1. **Single Sign-on 엔드포인트 편집** 섹션에서 다음 단계를 수행합니다.

    ![원격 인증 엔드포인트](./media/screensteps-tutorial/ic778527.png "원격 인증 엔드포인트")

    a. **새 SAML 인증서 파일 업로드**를 클릭한 후 Azure Portal에서 다운로드한 인증서를 업로드합니다.
    
    b. Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 **원격 로그인 URL** 텍스트 상자에 붙여 넣습니다.
    
    다. Azure Portal에서 복사한 **로그아웃 URL** 값을 **로그아웃 URL** 텍스트 상자에 붙여넣습니다.
    
    d. 사용자가 프로비전될 때 사용자를 할당할 **그룹**을 선택합니다.
    
    e. **업데이트**를 클릭합니다.

    f. **SAML 소비자 URL**을 클립보드로 복사하여 **ScreenSteps 도메인 및 URL** 섹션의 **로그온 URL** 텍스트 상자에 붙여넣습니다.
    
    g. **Single Sign-On 엔드포인트 편집**으로 돌아갑니다.
    
    h. **계정의 기본값으로 지정** 단추를 클릭하여 ScreenSteps에 로그온하는 모든 사용자에 대해 이 엔드포인트를 사용합니다. 또는 **사이트에 추가** 단추를 클릭하여 이 엔드포인트를 **ScreenSteps**의 특정 사이트에 대해 사용할 수 있습니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal ](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory &gt; 엔터프라이즈 애플리케이션** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/screensteps-tutorial/create_aaduser_01.png)

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/screensteps-tutorial/create_aaduser_02.png)

1. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/screensteps-tutorial/create_aaduser_03.png)

1. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/screensteps-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-screensteps-test-user"></a>ScreenSteps 테스트 사용자 만들기

이 섹션에서는 ScreenSteps에서 Britta Simon이라는 사용자를 만듭니다. ScreenSteps 플랫폼에서 사용자를 추가하려면  [ScreenSteps 클라이언트 지원 팀](https://www.screensteps.com/contact) 에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다. 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 ScreenSteps에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 ScreenSteps에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

1. 애플리케이션 목록에서 **ScreenSteps**를 선택합니다.

    ![애플리케이션 목록의 ScreenSteps 링크](./media/screensteps-tutorial/tutorial_screensteps_app.png)  

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 ScreenSteps 타일을 클릭하면 ScreenSteps 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/screensteps-tutorial/tutorial_general_01.png
[2]: ./media/screensteps-tutorial/tutorial_general_02.png
[3]: ./media/screensteps-tutorial/tutorial_general_03.png
[4]: ./media/screensteps-tutorial/tutorial_general_04.png

[100]: ./media/screensteps-tutorial/tutorial_general_100.png

[200]: ./media/screensteps-tutorial/tutorial_general_200.png
[201]: ./media/screensteps-tutorial/tutorial_general_201.png
[202]: ./media/screensteps-tutorial/tutorial_general_202.png
[203]: ./media/screensteps-tutorial/tutorial_general_203.png

