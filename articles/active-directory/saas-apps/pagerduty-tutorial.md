---
title: '자습서: PagerDuty와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 PagerDuty 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 703bc11ec7b0de0e8b0cfe3b99b7516afe8cac0b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810026"
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>자습서: PagerDuty와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 PagerDuty를 통합하는 방법을 알아봅니다.

PagerDuty를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- PagerDuty에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 PagerDuty에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

PagerDuty와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- PagerDuty Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 PagerDuty 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-pagerduty-from-the-gallery"></a>갤러리에서 PagerDuty 추가
PagerDuty가 Azure AD에 통합되도록 구성하려면 갤러리의 PagerDuty를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 PagerDuty를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추][1]

1. **엔터프라이즈 애플리케이션**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]

1. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

1. 검색 상자에서 **PagerDuty**를 입력하고, 결과 패널에서 **PagerDuty**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/pagerduty-tutorial/tutorial_pagerduty_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 하여 PagerDuty에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD 사용자에 해당하는 PagerDuty 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 PagerDuty의 관련 사용자 간에 링크 관계가 설정되어야 합니다.

PagerDuty에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

PagerDuty에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
1. **[PagerDuty 테스트 사용자 만들기](#create-a-pagerduty-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 PagerDuty에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-on 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 PagerDuty 애플리케이션에서 Single Sign-On을 구성합니다.

**PagerDuty에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **PagerDuty** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

1. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 대화 상자](./media/pagerduty-tutorial/tutorial_pagerduty_samlbase.png)

1. **PagerDuty 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![PagerDuty 도메인 및 URL Single Sign-On 정보](./media/pagerduty-tutorial/tutorial_pagerduty_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<tenant-name>.pagerduty.com`

    b. **식별자** 텍스트 상자에서 `https://<tenant-name>.pagerduty.com` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 이러한 값을 얻으려면 [PagerDuty 클라이언트 지원 팀](https://www.pagerduty.com/support/)에 문의하세요.

1. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/pagerduty-tutorial/tutorial_pagerduty_certificate.png)

1. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/pagerduty-tutorial/tutorial_general_400.png)

1. **PagerDuty 구성** 섹션에서 **PagerDuty 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![PagerDuty 구성](./media/pagerduty-tutorial/tutorial_pagerduty_configure.png)

1. 다른 웹 브라우저 창에서 Pagerduty 회사 사이트에 관리자로 로그인합니다.

1. 위쪽의 메뉴에서 **계정 설정**을 클릭합니다.

    ![계정 설정](./media/pagerduty-tutorial/ic778535.png "계정 설정")

1. **Single Sign-On**을 클릭합니다.

    ![Single Sign-On](./media/pagerduty-tutorial/ic778536.png "Single Sign-On")

1. **SSO(Single Sign-On) 사용** 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-On 사용](./media/pagerduty-tutorial/ic778537.png "Single Sign-On 사용")

    a. Azure Portal에서 다운로드한 Base 64로 인코딩된 인증서를 메모장에서 열고, 콘텐츠를 클립보드에 복사한 다음, **X.509 인증서** 텍스트 상자에 붙여넣습니다.
  
    b. **로그인 URL** 텍스트 상자에서 Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL**을 붙여넣습니다.
  
    다. **로그아웃 URL** 텍스트 상자에서 Azure Portal에서 복사한 **로그아웃 URL**을 붙여넣습니다.

    d. **Allow username/password login**(사용자 이름/암호 로그인 허용)을 선택합니다.

    e. **Require EXACT authentication context comparison**(정확하게 일치 인증 컨텍스트 비교 필요) 확인란을 선택합니다.

    f. **변경 내용 저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](./media/pagerduty-tutorial/create_aaduser_01.png) 

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/pagerduty-tutorial/create_aaduser_02.png) 

1. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![추가 단추](./media/pagerduty-tutorial/create_aaduser_03.png) 

1. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![사용자 대화 상자](./media/pagerduty-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-pagerduty-test-user"></a>PagerDuty 테스트 사용자 만들기

Azure AD 사용자가 PagerDuty에 로그인할 수 있도록 하려면 이 사용자를 PagerDuty로 프로비전해야 합니다.  
PagerDuty의 경우 프로비전은 수동 작업입니다.

>[!NOTE]
>다른 PagerDuty 사용자 계정 생성 도구 또는 PagerDuty에서 제공하는 API를 사용하여 Azure Active Directory 사용자 계정을 프로비전할 수 있습니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. **Pagerduty** 테넌트에 로그인합니다.

1. 위쪽의 메뉴에서 **사용자**를 클릭합니다.

1. **사용자 추가**를 클릭합니다.
   
    ![사용자 추가](./media/pagerduty-tutorial/ic778539.png "사용자 추가")

1.  **팀 초대** 대화 상자에서 다음 단계를 수행합니다.
   
    ![팀 초대](./media/pagerduty-tutorial/ic778540.png "팀 초대")

    a. 사용자의 **성과 이름**을 입력합니다(예: **Britta Simon**). 
   
    b. 사용자의 **이메일** 주소를 입력합니다(예: **brittasimon@contoso.com**).
   
    다. **추가**를 클릭한 다음 **초대장 보내기**를 클릭합니다.
   
    >[!NOTE]
    >추가된 모든 사용자는 PagerDuty 계정을 만들도록 초대를 받게 됩니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 PagerDuty에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200]

**Britta Simon을 PagerDuty에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

1. 애플리케이션 목록에서 **PagerDuty**를 선택합니다.

    ![애플리케이션 목록의 PagerDuty 링크](./media/pagerduty-tutorial/tutorial_pagerduty_app.png) 

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 PagerDuty 타일을 클릭하면 PagerDuty 애플리케이션에 자동으로 로그온됩니다.

액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/pagerduty-tutorial/tutorial_general_01.png
[2]: ./media/pagerduty-tutorial/tutorial_general_02.png
[3]: ./media/pagerduty-tutorial/tutorial_general_03.png
[4]: ./media/pagerduty-tutorial/tutorial_general_04.png

[100]: ./media/pagerduty-tutorial/tutorial_general_100.png

[200]: ./media/pagerduty-tutorial/tutorial_general_200.png
[201]: ./media/pagerduty-tutorial/tutorial_general_201.png
[202]: ./media/pagerduty-tutorial/tutorial_general_202.png
[203]: ./media/pagerduty-tutorial/tutorial_general_203.png
