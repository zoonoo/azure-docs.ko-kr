---
title: '자습서: Litmos와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Litmos 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: jeedes
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: a0c70ee6419280b0975d77fb213f9406286708cc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428005"
---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>자습서: Litmos와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Litmos를 통합하는 방법에 대해 알아봅니다.

Litmos를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Litmos에 액세스할 수 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 Litmos에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Litmos와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Litmos Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Litmos 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-litmos-from-the-gallery"></a>갤러리에서 Litmos 추가
Litmos의 Azure AD 통합을 구성하려면 갤러리의 Litmos를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Litmos를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

1. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]
    
1. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

1. 검색 상자에 **Litmos**를 입력하고 결과 패널에서 **Litmos**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Litmos](./media/litmos-tutorial/tutorial_litmos_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 사용하여 Litmos에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Litmos 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Litmos의 관련 사용자 간에 연결이 형성되어야 합니다.

Litmos에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

Litmos에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
1. **[Litmos 테스트 사용자 만들기](#create-a-litmos-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Litmos에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Litmos 애플리케이션에서 Single Sign-On을 구성합니다.

**Litmos에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Litmos** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

1. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/litmos-tutorial/tutorial_litmos_samlbase.png)

1. **Litmos 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Litmos 도메인 및 URL Single Sign-On 정보](./media/litmos-tutorial/tutorial_litmos_url.png)

    a. **식별자** 텍스트 상자에서 `https://<companyname>.litmos.com/account/Login` 패턴을 사용하여 URL을 입력합니다.

    나. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 식별자와 회신 URL로 업데이트합니다. 이러한 값은 자습서의 뒷부분에 나오거나 [Litmos 지원 팀](https://www.litmos.com/contact-us/)에 문의하여 얻을 수 있습니다.

1. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/litmos-tutorial/tutorial_litmos_certificate.png)

1. 구성의 일부로 Litmos 애플리케이션에 대한 **SAML 토큰 특성** 을 사용자 지정해야 합니다.

    ![특성 섹션](./media/litmos-tutorial/tutorial_attribute.png)
           
    | 특성 이름   | 특성 값 |   
    | ---------------  | ----------------|
    | FirstName |user.givenname |
    | LastName  |user.surname |
    | Email |user.mail |

    a. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.

    ![특성 추가](./media/litmos-tutorial/tutorial_attribute_04.png)

    ![특성 추가 대화 상자](./media/litmos-tutorial/tutorial_attribute_05.png)

    나. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다.
    
    d. **Ok**를 클릭합니다.     

1. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/litmos-tutorial/tutorial_general_400.png)

1. 다른 브라우저 창에서 Litmos 회사 사이트에 관리자 권한으로 로그온합니다.

1. 왼쪽의 탐색 모음에서 **계정**을 클릭합니다.
   
    ![앱 쪽의 계정 섹션][22] 

1. **통합** 탭을 클릭합니다.
   
    ![통합 탭][23] 

1. **통합** 탭에서 **3rd 파티 통합**에 아래로 스크롤한 다음 **SAML 2.0** 탭을 클릭합니다.
   
    ![SAML 2.0 섹션][24] 

1. **The SAML endpoint for litmos is:**(litmos에 대한 SAML 끝점:) 아래의 값을 복사하여 Azure Portal에서 **Litmos 도메인 및 URL**에 있는 **회신 URL** 텍스트 상자에 붙여넣습니다. 
   
    ![SAML 끝점][26] 

1. **Litmos** 응용 프로그램에서 다음 단계를 수행합니다.
    
     ![Litmos 애플리케이션][25] 
     
     a. **SAML 사용**을 클릭합니다.
    
     나. Base 64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 전체 인증서를 **SAML X.509 인증서** 텍스트 상자에 붙여넣습니다.
     
     다. **변경 내용 저장**을 클릭합니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/litmos-tutorial/create_aaduser_01.png)

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/litmos-tutorial/create_aaduser_02.png)

1. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/litmos-tutorial/create_aaduser_03.png)

1. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/litmos-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
  
### <a name="create-a-litmos-test-user"></a>Litmos 테스트 사용자 만들기

이 섹션은 Litmos에서 Britta Simon이라는 사용자를 만들기 위한 것입니다.  
Litmos 애플리케이션은 적시에 프로비전을 지원합니다. 즉, 필요한 경우 액세스 패널을 사용하여 애플리케이션에 액세스하는 동안 사용자 계정은 자동으로 만들어집니다.

**Litmos에서 Britta Simon이라는 사용자를 만들려면 다음 단계를 수행합니다.**

1. 다른 브라우저 창에서 Litmos 회사 사이트에 관리자 권한으로 로그온합니다.

1. 왼쪽의 탐색 모음에서 **계정**을 클릭합니다.
   
    ![앱 쪽의 계정 섹션][22] 

1. **통합** 탭을 클릭합니다.
   
    ![통합 탭][23] 

1. **통합** 탭에서 **3rd 파티 통합**에 아래로 스크롤한 다음 **SAML 2.0** 탭을 클릭합니다.
   
    ![SAML 2.0][24] 
    
1. **Autogenerate Users**(사용자 자동 생성)를 선택합니다.
   
    ![사용자 자동 생성][27] 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Litmos에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Litmos에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

1. 애플리케이션 목록에서 **Litmos**를 선택합니다.

    ![애플리케이션 목록의 Litmos 링크](./media/litmos-tutorial/tutorial_litmos_app.png)  

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.  

액세스 패널에서 Litmos 타일을 클릭하면 Litmos 애플리케이션에 자동으로 로그온됩니다. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/litmos-tutorial/tutorial_general_01.png
[2]: ./media/litmos-tutorial/tutorial_general_02.png
[3]: ./media/litmos-tutorial/tutorial_general_03.png
[4]: ./media/litmos-tutorial/tutorial_general_04.png
[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png

[100]: ./media/litmos-tutorial/tutorial_general_100.png

[200]: ./media/litmos-tutorial/tutorial_general_200.png
[201]: ./media/litmos-tutorial/tutorial_general_201.png
[202]: ./media/litmos-tutorial/tutorial_general_202.png
[203]: ./media/litmos-tutorial/tutorial_general_203.png

