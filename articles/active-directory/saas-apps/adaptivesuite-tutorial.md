---
title: '자습서: Adaptive Insights와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Adaptive Insights 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeedes
ms.openlocfilehash: 74f38ef3043cfa0e6df40c264b72cc5eae38544e
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430292"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>자습서: Adaptive Insights와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Adaptive Insights를 통합하는 방법에 대해 알아봅니다.

Adaptive Insights를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Adaptive Insights에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 Azure AD 계정으로 Adaptive Insights에 자동으로 로그온(Single Sign-on)할 수 있도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Adaptive Insights와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Adaptive Insights Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Adaptive Insights 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-adaptive-insights-from-the-gallery"></a>갤러리에서 Adaptive Insights 추가
Adaptive Insights가 Azure AD에 통합되도록 구성하려면 갤러리의 Adaptive Insights를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Adaptive Insights를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![이미지](./media/adaptivesuite-tutorial/selectazuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![이미지](./media/adaptivesuite-tutorial/a_select_app.png)
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![이미지](./media/adaptivesuite-tutorial/a_new_app.png)

4. 검색 상자에 **Adaptive Insights**를 입력하고 결과 패널에서 **Adaptive Insights**를 선택한 다음, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

     ![이미지](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 Adaptive Insights에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Adaptive Insights 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Adaptive Insights의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Adaptive Insights에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Adaptive Insights 테스트 사용자 만들기](#create-an-adaptive-insights-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Adaptive Insights에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Adaptive Insights 응용 프로그램에서 Single Sign-On을 구성합니다.

**Adaptive Insights에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. [Azure Portal](https://portal.azure.com/)의 **Adaptive Insights** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![이미지](./media/adaptivesuite-tutorial/B1_B2_Select_SSO.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![이미지](./media/adaptivesuite-tutorial/b1_b2_saml_sso.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![이미지](./media/adaptivesuite-tutorial/b1-domains_and_urlsedit.png)

4. **IDP** 시작 모드에서 응용 프로그램을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![이미지](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    a. **식별자(엔터티 ID)** 텍스트 상자에서 `https://login.adaptiveinsights.com:443/samlsso/<unique-id>` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > Adaptive Insights의 **SAML SSO 설정** 페이지에서 식별자(엔터티 ID)와 응답 URL 값을 가져올 수 있습니다.
 
5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 **인증서(Base64)** 를 컴퓨터에 다운로드하고 저장합니다.

    ![이미지](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_certficate.png) 

6. **Adaptive Insights 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    이 URL은 다음과 같을 수 있습니다.

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

    ![이미지](./media/adaptivesuite-tutorial/d1_samlsonfigure.png) 

7. 다른 웹 브라우저 창에서 Adaptive Insights 회사 사이트에 관리자로 로그인합니다.

8. **관리자**로 이동합니다.

    ![관리자](./media/adaptivesuite-tutorial/IC805644.png "관리자")

9. **사용자 및 역할** 섹션에서 **SAML SSO 설정 관리**를 클릭합니다.

    ![SAML SSO 설정 관리](./media/adaptivesuite-tutorial/IC805645.png "SAML SSO 설정 관리")

10. **SAML SSO 설정** 페이지에서 다음 단계를 수행합니다.

    ![SAML SSO 설정](./media/adaptivesuite-tutorial/IC805646.png "SAML SSO 설정")

    a. **ID 공급자 이름** 텍스트 상자에 구성할 이름을 입력합니다.

    b. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **ID 공급자 엔터티 ID** 텍스트 상자에 붙여 넣습니다.

    다. Azure Portal에서 복사한 **로그인 URL** 값을 **ID 공급자 SSO URL** 텍스트 상자에 붙여 넣습니다.

    d. Azure Portal에서 복사한 **로그아웃 URL** 값을 **ID 공급자 SSO URL** 텍스트 상자에 붙여 넣습니다.

    e. 다운로드한 인증서를 업로드하려면 **파일 선택**을 클릭합니다.

    f. 다음과 같이 선택합니다.

    * **SAML 사용자 ID**에 대해 **사용자의 Adaptive Insights 사용자 이름**을 선택합니다.

    * **SAML 사용자 ID 위치**에 대해 **제목의 NameID에서 사용자 ID**를 선택합니다.

    * **SAML NameID 형식**에 대해 **전자 메일 주소**를 선택합니다.

    * **SAML 사용**에 대해 **SAML SSO 및 Adaptive Insights 직접 로그인 허용**을 선택합니다.

    g. **Adaptive Insights SSO URL**을 복사하여 Azure Portal의 **Adaptive Insights 도메인 및 URL** 섹션에서 **식별자(엔터티 ID)** 와 **응답 URL** 텍스트 상자에 붙여넣습니다.

    h. **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![이미지](./media/adaptivesuite-tutorial/d_users_and_groups.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![이미지](./media/adaptivesuite-tutorial/d_adduser.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![이미지](./media/adaptivesuite-tutorial/d_userproperties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **속성**을 선택하고 **암호 표시** 확인란을 선택한 다음, 암호 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-an-adaptive-insights-test-user"></a>Adaptive Insights 테스트 사용자 만들기

Azure AD 사용자가 Adaptive Insights에 로그인할 수 있도록 하려면 Adaptive Insights로 프로비전되어야 합니다. Adaptive Insights의 경우 프로비전은 수동 작업입니다.

**사용자 프로비전을 구성하려면 다음 단계를 수행합니다.** 

1. **Adaptive Insights** 회사 사이트에 관리자 권한으로 로그인합니다.
2. **관리자**로 이동합니다.

   ![관리자](./media/adaptivesuite-tutorial/IC805644.png "관리자")

3. **사용자 및 역할** 섹션에서 **사용자 추가**를 클릭합니다.

   ![사용자 추가](./media/adaptivesuite-tutorial/IC805648.png "사용자 추가")
   
4. **새 사용자** 섹션에서 다음 단계를 수행합니다.

   ![제출](./media/adaptivesuite-tutorial/IC805649.png "제출")

   a. 관련된 텍스트 상자에 프로비전할 유효한 Azure Active Directory 사용자의 **이름**, **로그인**, **전자 메일**, **암호**를 입력합니다.

   b. **역할**을 선택합니다.

   다. **제출**을 클릭합니다.

>[!NOTE]
>다른 Adaptive Insights 사용자 계정 생성 도구 또는 Adaptive Insights가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.
>

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Adaptive Insights에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 응용 프로그램**을 선택한 다음, **모든 응용 프로그램**을 선택합니다.

    ![이미지](./media/adaptivesuite-tutorial/d_all_applications.png)

2. 응용 프로그램 목록에서 **Adaptive Insights**를 선택합니다.

    ![이미지](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_app.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![이미지](./media/adaptivesuite-tutorial/d_leftpaneusers.png)

4. **추가** 단추를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![이미지](./media/adaptivesuite-tutorial/d_assign_user.png)

4. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

5. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Adaptive Insights 타일을 클릭하면 Adaptive Insights 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
