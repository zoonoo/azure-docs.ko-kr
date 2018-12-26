---
title: '자습서: GetThere와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 GetThere 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0441087e-953f-4b51-9842-316da7b72392
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: jeedes
ms.openlocfilehash: bcefa3966a6c854f02ce7b3a75306b3d1c888ecd
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49433110"
---
# <a name="tutorial-azure-active-directory-integration-with-getthere"></a>자습서: GetThere와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 GetThere를 통합하는 방법에 대해 알아봅니다.

GetThere를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- GetThere에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 GetThere에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

GetThere와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- GetThere Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 GetThere 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-getthere-from-the-gallery"></a>갤러리에서 GetThere 추가
GetThere의 Azure AD 통합을 구성하려면 갤러리의 GetThere를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 GetThere를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![이미지](./media/getthere-tutorial/selectazuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![이미지](./media/getthere-tutorial/a_select_app.png)
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![이미지](./media/getthere-tutorial/a_new_app.png)

4. 검색 상자에 **GetThere**를 입력하고 결과 패널에서 **GetThere**를 선택한 다음, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

     ![이미지](./media/getthere-tutorial/tutorial_getthere_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 GetThere에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 GetThere 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 GetThere의 관련 사용자 간에 연결이 형성되어야 합니다.

GetThere에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[GetThere 테스트 사용자 만들기](#create-a-getthere-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 GetThere에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 GetThere 응용 프로그램에서 Single Sign-On을 구성합니다.

**GetThere에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. [Azure Portal](https://portal.azure.com/)의 **GetThere** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![이미지](./media/getthere-tutorial/B1_B2_Select_SSO.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![이미지](./media/getthere-tutorial/b1_b2_saml_sso.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![이미지](./media/getthere-tutorial/b1-domains_and_urlsedit.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![이미지](./media/getthere-tutorial/tutorial_getthere_url.png)

    a. **식별자** 텍스트 상자에 아래 URL 중 하나를 입력합니다.
    | |
    |--|
    | `getthere.com` |
    | `http://idp.getthere.com` |

    b. **회신 URL** 텍스트 상자에 아래 URL 중 하나를 입력합니다.
    | |
    |--|
    | `https://wx1.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/ssoaasvalidate.act` |
    | `https://wx1.getthere.net/login/saml/ssoaavalidate.act` |
    
5. GetThere 응용 프로그램을 사용하려면 Username 클레임에 고유한 **Username** 값이 있어야 합니다. 고객은 Username 클레임의 올바른 값을 매핑할 수 있습니다. 아래 예제에서는 **Username**을 **user.mail**에 매핑했으나 조직 설정에 따라 이 매핑을 변경할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![이미지](./media/getthere-tutorial/i4-attribute.png)

6. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
    
    | 이름 |  원본 특성 |  네임스페이스 |
    | ---------------| --------------- | --------------- |
    | Sitename | “조직별로 값 제공” | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sitename |
    | 사용자 이름 |  user.mail | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/username |
    
    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](./media/getthere-tutorial/i2-attribute.png)

    ![이미지](./media/getthere-tutorial/i3-attribute.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스** 텍스트 상자에 해당 행에 대해 표시되는 특성을 입력합니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **저장**을 클릭합니다.

7. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 **인증서(Base64)** 를 컴퓨터에 다운로드하고 저장합니다.

    ![이미지](./media/getthere-tutorial/tutorial_getthere_certficate.png) 

8. **설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    이 URL은 다음과 같을 수 있습니다.

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

    ![이미지](./media/getthere-tutorial/d1_samlsonfigure.png) 

9. **GetThere** 쪽에서 Single Sign-On을 구성하려면 다운로드한 **인증서(Base64)** 및 복사한 **로그인 URL, Azure AD 식별자, 로그아웃 URL**을 [GetThere Client 지원 팀](mailto:dataintegration@sabre.com)으로 보내야 합니다. 이렇게 설정하면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![이미지](./media/getthere-tutorial/d_users_and_groups.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![이미지](./media/getthere-tutorial/d_adduser.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![이미지](./media/getthere-tutorial/d_userproperties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **속성**을 선택하고 **암호 표시** 확인란을 선택한 다음, 암호 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-a-getthere-test-user"></a>GetThere 테스트 사용자 만들기

이 섹션에서는 GetThere에서 Britta Simon이라는 사용자를 만듭니다. GetThere 플랫폼에서 사용자를 추가하려면 [GetThere 클라이언트 지원 팀](mailto:dataintegration@sabre.com)에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 GetThere에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 응용 프로그램**을 선택한 다음, **모든 응용 프로그램**을 선택합니다.

    ![이미지](./media/getthere-tutorial/d_all_applications.png)

2. 응용 프로그램 목록에서 **GetThere**를 선택합니다.

    ![이미지](./media/getthere-tutorial/tutorial_getthere_app.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![이미지](./media/getthere-tutorial/d_leftpaneusers.png)

4. **추가** 단추를 선택하고 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![이미지](./media/getthere-tutorial/d_assign_user.png)

4. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

5. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 GetThere 타일을 클릭하면 GetThere 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
