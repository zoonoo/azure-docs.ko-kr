---
title: '자습서: Land Gorilla Client와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Land Gorilla Client 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2e1288885476aeeba01f966b615c23c5fe51081
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60260998"
---
# <a name="tutorial-azure-active-directory-integration-with-land-gorilla-client"></a>자습서: Land Gorilla Client와 Azure Active Directory 통합

이 자습서에서는 Land Gorilla Client를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다.

Land Gorilla Client를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Land Gorilla Client에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용의 Azure AD 계정으로 Land Gorilla Client에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure 관리 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](../manage-apps/what-is-single-sign-on.md)을 참조하세요.


## <a name="prerequisites"></a>필수 조건

Land Gorilla Client와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Land Gorilla Client Single Sign-On을 사용하도록 설정된 구독


> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.


이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.


## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Land Gorilla Client 추가
1. Azure AD Single Sign-on 구성 및 테스트


## <a name="adding-land-gorilla-client-from-the-gallery"></a>갤러리에서 Land Gorilla Client 추가
Land Gorilla Client가 Azure AD에 통합되도록 구성하려면 갤러리에서 Land Gorilla Client를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Land Gorilla Client를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure 관리 포털](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

1. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![애플리케이션][2]
    
1. 대화 상자 위쪽에 있는 **추가** 단추를 클릭합니다.

    ![애플리케이션][3]

1. 검색 상자에 **Land Gorilla Client**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/landgorilla-tutorial/tutorial_landgorilla_search.png)

1. 결과 창에서 **Land Gorilla Client**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/landgorilla-tutorial/tutorial_landgorilla_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Land Gorilla Client에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Land Gorilla Client 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Land Gorilla Client 관련 사용자 간에 연결 관계가 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Land Gorilla Client의 **Username** 값으로 할당하여 설정합니다.

Land Gorilla Client에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - 제한된 그룹으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
1. **[Land Gorilla 테스트 사용자 만들기](#creating-a-land-gorilla-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
1. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure 관리 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 Land Gorilla Client 애플리케이션에서 Single Sign-On을 구성합니다.

**Land Gorilla Client에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털의 **Land Gorilla Client** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

1. **Single sign on** 대화 상자에서 **모드**로 **SAML 기반 로그온**을 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/landgorilla-tutorial/tutorial_landgorilla_samlbase.png)

1. **Land Gorilla Client 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/landgorilla-tutorial/tutorial_landgorilla_url_02.png)

    a. **식별자** 텍스트 상자에 다음 패턴 중 하나를 사용하여 값을 입력합니다. 
    
    `https://<customer domain>.landgorilla.com/` 
    
    `https://www.<customer domain>.landgorilla.com`

    b. **회신 URL** 텍스트 상자에 다음 패턴 중 하나를 사용하여 URL을 입력합니다.

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`
    
    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트해야 합니다. 식별자에는 고유한 문자열 값을 사용하는 것이 좋습니다. [Land Gorilla Client 팀](https://www.landgorilla.com/support/)에 이러한 값을 문의합니다. 

1. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 XML 파일을 저장합니다.

    ![Configure Single Sign-On](./media/landgorilla-tutorial/tutorial_landgorilla_certificate.png) 

1. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/landgorilla-tutorial/tutorial_general_400.png) 

1. Land Gorilla 쪽에서 애플리케이션에 대한 SSO 구성이 완료되도록 하려면 [Land Gorilla Client 지원 팀](https://www.landgorilla.com/support/)에 문의하고 다운로드한 **“메타데이터 XML** 파일을 제공하십시오.


### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 관리 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 관리 포털**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/landgorilla-tutorial/create_aaduser_01.png) 

1. **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭하여 사용자 목록을 표시합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/landgorilla-tutorial/create_aaduser_02.png) 

1. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/landgorilla-tutorial/create_aaduser_03.png) 

1. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/landgorilla-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다. 

### <a name="creating-a-land-gorilla-test-user"></a>Land Gorilla 테스트 사용자 만들기

[Land Gorilla 지원 팀](https://www.landgorilla.com/support/)에 문의하여 Land Gorilla 플랫폼에 사용자를 추가하세요.
    
### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Land Gorilla Client에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Land Gorilla Client에 할당하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털에서 애플리케이션 보기를 열고 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

1. 애플리케이션 목록에서 **Land Gorilla Client**를 선택합니다.

    ![Configure Single Sign-On](./media/landgorilla-tutorial/tutorial_landgorilla_app.png) 

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    


### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Land Gorilla Client 타일을 클릭하면 Land Gorilla Client 애플리케이션에 자동으로 로그온됩니다.


## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/landgorilla-tutorial/tutorial_general_01.png
[2]: ./media/landgorilla-tutorial/tutorial_general_02.png
[3]: ./media/landgorilla-tutorial/tutorial_general_03.png
[4]: ./media/landgorilla-tutorial/tutorial_general_04.png

[100]: ./media/landgorilla-tutorial/tutorial_general_100.png
[200]: ./media/landgorilla-tutorial/tutorial_general_200.png
[201]: ./media/landgorilla-tutorial/tutorial_general_201.png
[202]: ./media/landgorilla-tutorial/tutorial_general_202.png
[203]: ./media/landgorilla-tutorial/tutorial_general_203.png
