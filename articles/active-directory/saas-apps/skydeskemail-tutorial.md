---
title: '자습서: SkyDesk Email과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 SkyDesk Email 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 058aad72ea8e5741bc632b3c27c032613683ae78
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444085"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>자습서: SkyDesk Email과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SkyDesk Email을 통합하는 방법에 대해 알아봅니다.

SkyDesk Email을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- SkyDesk Email에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 SkyDesk Email에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

SkyDesk Email와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- SkyDesk Email Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [평가판 제품](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 SkyDesk Email 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-skydesk-email-from-the-gallery"></a>갤러리에서 SkyDesk Email 추가
SkyDesk Email의 Azure AD 통합을 구성하려면 갤러리의 SkyDesk Email을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SkyDesk Email을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

1. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![애플리케이션][2]
    
1. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![애플리케이션][3]

1. 검색 상자에 **SkyDesk Email**을 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/skydeskemail-tutorial/tutorial_skydeskemail_search.png)

1. 결과 창에서 **SkyDesk Email**을 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/skydeskemail-tutorial/tutorial_skydeskemail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 SkyDesk Email에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 SkyDesk Email 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 SkyDesk Email의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

SkyDesk Email에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

SkyDesk Email에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
1. **[SkyDesk Email 테스트 사용자 만들기](#creating-a-skydesk-email-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 SkyDesk Email에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 SkyDesk Email 응용 프로그램에서 Single Sign-On을 구성합니다.

**SkyDesk Email에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **SkyDesk Email** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

1. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/skydeskemail-tutorial/tutorial_skydeskemail_samlbase.png)

1. **SkyDesk Email 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/skydeskemail-tutorial/tutorial_skydeskemail_url.png)

    **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE] 
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 값을 얻으려면 [SkyDesk Email 클라이언트 지원 팀](https://www.skydesk.sg/support/)에 문의하세요. 
 
1. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Configure Single Sign-On](./media/skydeskemail-tutorial/tutorial_skydeskemail_certificate.png) 

1. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/skydeskemail-tutorial/tutorial_general_400.png)

1. **SkyDesk Email 구성** 섹션에서 **SkyDesk Email 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/skydeskemail-tutorial/tutorial_skydeskemail_configure.png) 

1. **SkyDesk Email**에서 SSO를 사용하려면 다음 단계를 수행합니다.

    a. SkyDesk Email 계정에 관리자 권한으로 로그온합니다.

    나. 위쪽 메뉴에서 **설정**을 클릭한 다음 **조직**을 클릭합니다. 
    
      ![Configure Single Sign-On](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
    다. 왼쪽 패널에서 **도메인**을 클릭합니다.
    
      ![Configure Single Sign-On](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

    d. **도메인 추가**를 클릭합니다.
    
      ![Configure Single Sign-On](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

    e. 도메인 이름을 입력하고 도메인을 확인합니다.
    
      ![Configure Single Sign-On](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

    f. 왼쪽 패널에서 **SAML 인증** 을 클릭합니다.
    
      ![Configure Single Sign-On](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. **SAML 인증** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
      ![Configure Single Sign-On](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)
   
    >[!NOTE]
    >SAML 기반 인증을 사용하려면 **확인된 도메인** 또는 **포털 URL** 설정이 있어야 합니다. 고유의 이름으로 포털 URL을 설정할 수 있습니다.
    > 
    > 
   
    ![Configure Single Sign-On](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 **로그인 URL** 텍스트 상자에 붙여 넣습니다.
   
    나. Azure Portal에서 복사한 **로그아웃 URL** 값을 **로그아웃 URL** 텍스트 상자에 붙여 넣습니다.

    다. **암호 변경 URL** 은 선택 사항이므로 비워 둡니다.

    d. **파일에서 키 가져오기**를 클릭하여 Azure Portal에서 다운로드한 인증서를 선택하고 **열기**를 클릭하여 인증서를 업로드합니다.

    e. **알고리즘**으로 **RSA**를 선택합니다.

    f. **확인** 을 클릭하여 변경 내용을 저장합니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/skydeskemail-tutorial/create_aaduser_01.png) 

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/skydeskemail-tutorial/create_aaduser_02.png) 

1. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/skydeskemail-tutorial/create_aaduser_03.png) 

1. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/skydeskemail-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-skydesk-email-test-user"></a>SkyDesk Email 테스트 사용자 만들기

이 섹션에서는 SkyDesk Email에서 Britta Simon이라는 사용자를 만듭니다.

1. SkyDesk Email의 왼쪽 패널에서 **사용자 액세스**를 클릭하고 사용자 이름을 입력합니다. 

    ![Configure Single Sign-On](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

>[!NOTE] 
>일괄 사용자를 만들어야 하는 경우에는 [SkyDesk Email 클라이언트 지원 팀](https://www.skydesk.sg/support/)에 문의해야 합니다.


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SkyDesk Email에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 SkyDesk Email에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

1. 응용 프로그램 목록에서 **SkyDesk Email**를 선택합니다.

    ![Configure Single Sign-On](./media/skydeskemail-tutorial/tutorial_skydeskemail_app.png) 

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션은 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트하기 위한 것입니다.

액세스 패널에서 SkyDesk Email 타일을 클릭하면 SkyDesk Email 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/skydeskemail-tutorial/tutorial_general_04.png

[100]: ./media/skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/skydeskemail-tutorial/tutorial_general_201.png
[202]: ./media/skydeskemail-tutorial/tutorial_general_202.png
[203]: ./media/skydeskemail-tutorial/tutorial_general_203.png

